# Registering an MLflow model in Azure ML

Registering models with MLflow can be done by enabling autologging or by using custom logging.

## Autologging, Manual logging, Model signature

Autologging can be enabled by including `mlflow.autolog()`. Autologging also supports which _flavour_ that you use with
autologging, e.g. Keras or TensorFlow.

For manual logging, set `log_models=False` and set your own parameters and meterics in the `autolog` parameter.
During manual logging, you can have mode control over the model's input and output schema by defining the _model
signature_.
E.g. to manually do this:

```python
from mlflow.models.signature import ModelSignature
from mlflow.types.schema import Schema, ColSpec

# Define the schema for the input data
input_schema = Schema([
    ColSpec("double", "sepal length (cm)"),
    ColSpec("double", "sepal width (cm)"),
    ColSpec("double", "petal length (cm)"),
    ColSpec("double", "petal width (cm)"),
])

# Define the schema for the output data
output_schema = Schema([ColSpec("long")])

# Create the signature object
signature = ModelSignature(inputs=input_schema, outputs=output_schema)
```

## MLflow model format

MLflow uses the MLModel format to store all relevant model assets in a folder or directory. One essential file in the
directory is the `MLmodel` file. The `MLmodel` file is the single source of truth about how the model should be loaded
and
used.
Example `MLmodel`file, trained with `fastai`:

```yaml
artifact_path: classifier
flavors:
  fastai:
    data: model.fastai
    fastai_version: 2.4.1
  python_function:
    data: model.fastai
    env: conda.yaml
    loader_module: mlflow.fastai
    python_version: 3.8.12
model_uuid: e694c68eba484299976b06ab9058f636
run_id: e13da8ac-b1e6-45d4-a9b2-6a0a5cfac537
signature:
  inputs: '[{"type": "tensor",
             "tensor-spec": 
                 {"dtype": "uint8", "shape": [-1, 300, 300, 3]}
           }]'
  outputs: '[{"type": "tensor", 
              "tensor-spec": 
                 {"dtype": "float32", "shape": [-1,2]}
            }]'
```

Note the:

- `flavour`: A flavor is the machine learning library with which the model was created.
- `signature`: Serve as data contracts between the model and the server running your model.

There are two types of signatures:

- Column-based: used for tabular data with a pandas.Dataframe as inputs.
- Tensor-based: used for n-dimensional arrays or tensors (often used for unstructured data like text or images), with
  numpy.ndarray as inputs.

## Registering a model

You can easily store a model in the Azure ML model registry. This stores and versions the model in the workspace.
There are three types of models that are registerable:

- MLflow: Model trained and tracked with MLflow. Recommended for standard use cases.
- Custom: Model type with a custom standard not currently supported by Azure Machine Learning.
- Triton: Model type for deep learning workloads. Commonly used for TensorFlow and PyTorch model deployments.

To register an MLflow model:

```python
from azure.ai.ml.entities import Model
from azure.ai.ml.constants import AssetTypes

job_name = returned_job.name

run_model = Model(
    path=f"azureml://jobs/{job_name}/outputs/artifacts/paths/model/",
    name="mlflow-diabetes",
    description="Model created from run.",
    type=AssetTypes.MLFLOW_MODEL,
)
# Uncomment after adding required details above
ml_client.models.create_or_update(run_model)
```

Note this is different from running a job with `ml_client.create_or_updaet(<job>)`



