# Deploying a ML model

In Azure ML there are two types of endpoints:

- Managed online endpoints: Azure Machine Learning manages all the underlying infrastructure.
- Kubernetes online endpoints: Users manage the Kubernetes cluster which provides the necessary infrastructure.

## Deployment

To deploy your model to a managed online endpoint, you need to specify four things:

- Model assets like the model pickle file, or a registered model in the Azure Machine Learning workspace.
- Scoring script that loads the model.
- Environment which lists all necessary packages that need to be installed on the compute of the endpoint.
- Compute configuration including the needed compute size and scale settings to ensure you can handle the amount of
  requests the endpoint will receive.

### Blue/Green deployment

With blue/green deployment, multiple versions of the model can be deployed to the same endpoint.
In this case, the traffic is distributed between two versions of the model, e.g. 90% goes to the blue (v1) deployment,
while 10% goes to the green (v2) deployment. If
If the latest version doesn't perform better, the model can easily roll back to **v1**.

## Creating an endpoint

To create an endpoint use `ManagedOnlineEndpoint`:

```python
from azure.ai.ml.entities import ManagedOnlineEndpoint

# create an online endpoint
endpoint = ManagedOnlineEndpoint(
    name="endpoint-example",
    description="Online endpoint",
    auth_mode="key",
)

ml_client.begin_create_or_update(endpoint).result()
```

## Creating batch endpoints

To perform batch predictions, we need to deploy the model a batch endpoint.
The batch job can then be triggered from another service, such as **Synapse Analytics** or **Databricks**.

To create the back endpoint use the `BatchEndpoint` class:

```python
# create a batch endpoint
endpoint = BatchEndpoint(
    name="endpoint-example",
    description="A batch endpoint",
)

ml_client.batch_endpoints.begin_create_or_update(endpoint)
```

## Batch deployment

We can use **compute clusters** for batch deployments:

```python
from azure.ai.ml.entities import AmlCompute

cpu_cluster = AmlCompute(
    name="aml-cluster",
    type="amlcompute",
    size="STANDARD_DS11_V2",
    min_instances=0,
    max_instances=4,
    idle_time_before_scale_down=120,
    tier="Dedicated",
)

cpu_cluster = ml_client.compute.begin_create_or_update(cpu_cluster)

```

## Deploy MLFlow model to batch endpoint

To avoid using a scoring script and environment, the MLFlow model needs to be registered in the Azure ML workspace:

```python
from azure.ai.ml.constants import AssetTypes
from azure.ai.ml.entities import Model

model_name = 'mlflow-model'
model = ml_client.models.create_or_update(
    Model(name=model_name, path='./model', type=AssetTypes.MLFLOW_MODEL)
)
```

Then, to deploy the MLFlow model to a batch endpoint, use `BatchDeployment`:

```python
from azure.ai.ml.constants import BatchDeploymentOutputAction
from azure.ai.ml.entities import BatchDeployment, BatchRetrySettings

deployment = BatchDeployment(
    name="forecast-mlflow",
    description="A sales forecaster",
    endpoint_name=endpoint.name,
    model=model,
    compute="aml-cluster",
    instance_count=2,
    max_concurrency_per_instance=2,
    mini_batch_size=2,
    output_action=BatchDeploymentOutputAction.APPEND_ROW,
    output_file_name="predictions.csv",
    retry_settings=BatchRetrySettings(max_retries=3, timeout=300),
    logging_level="info",
)
ml_client.batch_deployments.begin_create_or_update(deployment)
```

### Deploying to a batch endpoint WITHOUT MLFlow

If you want to deploy a model to a batch endpoint without using the MLflow model format, you need to create the scoring
script and environment:

- Scoring script consists of `init` and `run` methods
- Environment is defined by a conda configuration (`conda.yaml`) and created with the `Environment` class


# Trigger a batch scoring job
To prepare data for batch predictions, register a folder as a data asset and then use the registered data asset as input:

```python
from azure.ai.ml import Input
from azure.ai.ml.constants import AssetTypes

input = Input(type=AssetTypes.URI_FOLDER, path="azureml:new-data:1")

job = ml_client.batch_endpoints.invoke(
    endpoint_name=endpoint.name,
    input=input)
```

For troubleshooting, investigate the output files:
- `job_error.txt`
- `job_progress_overview.txt`
- `job_result.txt`