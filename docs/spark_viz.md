# Data visualization in Spark

When displaying a dataframe or running a SQL query in the Spark notebook, the results are displayed by default as a *
*table**.
Alternatively, change the results view to a **chart** and adjust the properties to visualize the data.

## Using `matplotlib` in code

An alternative to using the embedded Notebook visualization, you can use a Python visualization package such as
`matplotlib` or `Seaborn`:

```python
from matplotlib import pyplot as plt

# Get the data as a Pandas dataframe
data = spark.sql("SELECT Category, COUNT(ProductID) AS ProductCount \
                  FROM products \
                  GROUP BY Category \
                  ORDER BY Category").toPandas()

fig = plt.figure(figsize=(12, 8))

# Bar chart
plt.bar(x=data['Category'], height=data['ProductCount'], color='orange')
plt.title('Product Counts by Category')
plt.xlabel('Category')
plt.ylabel('Products')
plt.grid(color='#95a5a6', linestyle='--', linewidth=2, axis='y', alpha=0.7)
plt.xticks(rotation=70)
plt.show()
```

Note that matplotlib requires a `Pandas` dataframe, which a `Spark` dataframe can be converted to using `.toPandas()`.

