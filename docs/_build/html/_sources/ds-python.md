# Azure ML Python SDK

Install the Python SDK:

```bash
python -m pip install azure-ai-ml
```

## Connecting to the workspace
Connecting to a workspace can then be performed as follows:

```python
from azure.ai.ml import MLClient
from azure.identity import DefaultAzureCredential

ml_client = MLClient(
    DefaultAzureCredential(), subscription_id, resource_group, workspace
)
```

Here, `resource_group` and `workspace_name` are the names of your resource group and workspace, respectively.

## Starting a job in Python SDK
After connected to the workspace you can create a new job to train a model as follolws:
```python
from azure.ai.ml import command

job = command(
    code="./src",
    command="python train.py",
    environment="AzureML-sklearn-0.24-ubuntu18.04-py37-cpu@latest",
    compute="aml-cluster",
    experiment_name="train-model"
)

returned_job = ml_client.create_or_update(job)
```