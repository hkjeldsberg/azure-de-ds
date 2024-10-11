# Azure Synapse serverless SQL pool

SQL is one of the most prevalent technologies within Azure Synapse Analytics.
Azure Synapse SQL offers **two** kinds of runtime environments:

- Serverless SQL pool: On-demand SQL processing
- Dedicated SQL pool: Enterprise-scale relation database instances

## Uses of serverless SQL pools

Common use cases of serverless SQL pools are:

- Data exploration (e.g. TOP 100 rows)
- Data transformation (e.g. part of a pipeline)
- Logical data warehouse (Data remains in data lake files, but is abstracted by a relation schema, accessible by
  clients)

## Select TOP 100 rows:

Simple command for extracting data from _CSV_ files:

```SQL
SELECT TOP 100 *
FROM OPENROWSET(
        BULK 'https://mydatalake.blob.core.windows.net/data/files/*.csv',
        FORMAT = 'csv',
        FIRSTROW = 2
     ) AS rows
```

Set header titles with `HEADER_ROW=TRUE` parameter.

## Creating external database objects

CREATE EXTERNAL TABLE AS SELECT (CETAS) statement can be used in a dedicated SQL pool or serverless SQL pool to persist
the results of a query in an external table, which stores its data in a file in the data lake.
Example; to create an external data source, run the following command:

```SQL
CREATE
EXTERNAL DATA SOURCE files
WITH (
    LOCATION = 'https://mydatalake.blob.core.windows.net/data/files/',
    TYPE = HADOOP, -- For dedicated SQL pool
    -- TYPE = BLOB_STORAGE, -- For serverless SQL pool
    CREDENTIAL = storageCred
);
```

## Creating external file format

To create an external file format:

```sql
CREATE
EXTERNAL FILE FORMAT ParquetFormat
WITH (
        FORMAT_TYPE = PARQUET,
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
    );
```

## Full example using `CREATE EXTERNAL TABLE`

```sql
CREATE
EXTERNAL TABLE SpecialOrders
    WITH (
        -- details for storing results
        LOCATION = 'special_orders/',
        DATA_SOURCE = files,
        FILE_FORMAT = ParquetFormat
    )
AS
SELECT OrderID, CustomerName, OrderTotal
FROM OPENROWSET(
         -- details for reading source files
             BULK 'sales_orders/*.csv',
             DATA_SOURCE = 'files',
             FORMAT = 'CSV',
             PARSER_VERSION = '2.0',
             HEADER_ROW = TRUE
     ) AS source_data
WHERE OrderType = 'Special Order';
```

## Creating a data transformation precedure

To create a stored procedure for data that already exists, use the `CREATE PROCEDURE` command.
Example:
```sql
CREATE PROCEDURE usp_special_orders_by_year @order_year INT
AS
BEGIN
	-- Drop the table if it already exists
	IF EXISTS (
                SELECT * FROM sys.external_tables
                WHERE name = 'SpecialOrders'
            )
        DROP EXTERNAL TABLE SpecialOrders

	-- Create external table with special orders
	-- from the specified year
	CREATE EXTERNAL TABLE SpecialOrders
		WITH (
			LOCATION = 'special_orders/',
			DATA_SOURCE = files,
			FILE_FORMAT = ParquetFormat
		)
	AS
	SELECT OrderID, CustomerName, OrderTotal
	FROM
		OPENROWSET(
			BULK 'sales_orders/*.csv',
			DATA_SOURCE = 'files',
			FORMAT = 'CSV',
			PARSER_VERSION = '2.0',
			HEADER_ROW = TRUE
		) AS source_data
	WHERE OrderType = 'Special Order'
	AND YEAR(OrderDate) = @order_year
END
```