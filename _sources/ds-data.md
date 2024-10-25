# Data storage in Azure ML

The three protocols when working with data are:

- http(s): Public or private data stores in Azure Blob Storage or publicly available locations
- abfs(s): Azure Data Lake Storage
- azureml: Data stored in a _datastore_

## Azure Datastore

When you create a datastore in Azure Machine Learning, you'll store the connection and authentication information in the
workspace. Then, to access the data in the container, you can use the azureml protocol.

Note that a datastore is a reference to an existing storage account on Azure.

## Creating a datastore

Datastores can be created for multiple kinds of Azure data sources:

- Azure Blob Storage
- Azure File Share
- Azure Data Lake

Authentication to datastores is one of the two:

- Credential-based: Service principal, shared access signature token or account key
- Identity-based: With Entra ID or managed identity

To create a datastore with the Python SDK:

```python
blob_datastore = AzureBlobDatastore(
    name="blob_example",
    description="Datastore pointing to a blob container",
    account_name="mytestblobstore",
    container_name="data-container",
    credentials=AccountKeyConfiguration(
        account_key="XXXxxxXXXxXXXXxxXXX"
    ),
)
ml_client.create_or_update(blob_datastore)
```

Alternatively use `credentials=SasTokenConfiguration()` to use a SAS token.

## Data asset

Data assets are references to where the data is stored to easily get access to data used for training.
You can create data assets to get access to data in datastores, Azure storage services, public URLs, or data stored on
your local device.
Benefits are:

- Sharing and reusing data
- Seamlessly access data
- Versioning of metadata

The three main types of data assets are:

- URI file: Specific file
- URI folder: Specific folder
- MLTable: Folder or file, and includes a schema to read as tabular data

### Creating URI file data asset

To create a URI file, you will need to use one of the supported paths:

- Local: `./<path>`
- Azure Blob Storage:`wasbs://<account_name>.blob.core.windows.net/<container_name>/<folder>/<file>`
- Azure Data Lake Storage (Gen 2): `abfss://<file_system>@<account_name>.dfs.core.windows.net/<folder>/<file>`
- Datastore: `azureml://datastores/<datastore_name>/paths/<folder>/<file>`

Then to create a URI file data asset using the Python SDK:

```python
from azure.ai.ml.entities import Data
from azure.ai.ml.constants import AssetTypes

my_path = '<supported-path>'

my_data = Data(
    path=my_path,
    type=AssetTypes.URI_FILE,
    description="<description>",
    name="<name>",
    version="<version>"
)

ml_client.data.create_or_update(my_data)
```

### Creating URI folder data asset

To create a URI folder data asset:

```python
from azure.ai.ml.entities import Data
from azure.ai.ml.constants import AssetTypes

my_path = '<supported-path>'

my_data = Data(
    path=my_path,
    type=AssetTypes.URI_FOLDER,
    description="<description>",
    name="<name>",
    version='<version>'
)

ml_client.data.create_or_update(my_data)
```

### Creating a MLTable data asset

Use a MLTable when the schema of your data is complex or changes frequently.
Then you only have to change how to read the data in the data asset itself.
To create a MLTable, first create a schema (`schema.yaml`):

```yaml
type: mltable

paths:
  - pattern: ./*.txt
transformations:
  - read_delimited:
      delimiter: ','
      encoding: ascii
      header: all_files_same_headers
```
Then to create a MLTable data asset:

```python
from azure.ai.ml.constants import AssetTypes
from azure.ai.ml.entities import Data

my_path = '<path-including-mltable-file>'

my_data = Data(
    path=my_path,
    type=AssetTypes.MLTABLE,
    description="<description>",
    name="<name>",
    version='<version>'
)

ml_client.data.create_or_update(my_data)
```


