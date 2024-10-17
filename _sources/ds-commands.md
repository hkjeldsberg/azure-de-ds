# Introduction to Azure Machine Learning (ML)

Azure Machine Learning provides a platform for data scientists to train, deploy, and manage their machine learning
models on the Microsoft Azure platform. Azure Machine Learning provides a comprehensive set of resources and assets to
train and deploy effective machine learning models.

## Creating a Azure ML workspace

The workspace can be created in the Azure Portal, or using the Python SRK:

```python
from azure.ai.ml.entities import Workspace

workspace_name = "mlw-example"

ws_basic = Workspace(
    name=workspace_name,
    location="eastus",
    display_name="Basic workspace-example",
    description="This example shows how to create a basic workspace",
)
ml_client.workspaces.begin_create(ws_basic)
```

The workspace can now be found at `https://ml.azure.com`.

## Azure ML overview

Azure ML consists of the following main components:

- Author: To create jobs for training and tracking ML models
- Assets: Create and review assets used when training
- Manage: Create and manage resources needed to train models
