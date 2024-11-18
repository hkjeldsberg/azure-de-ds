# Track model training in Jupyter notebooks

Tracking models can be done with MLFlow; MLflow is an open-source library for tracking and managing your machine
learning experiments. In particular, MLflow Tracking is a component of MLflow that logs everything about the model
you're training, such as parameters, metrics, and artifacts.

To start tracking a local notebook, configure MLflow to point to the Azure ML workspare by setting
`mlflow.set_tracking_uri = "MLFLOW-TRACKING-URI"`.

## Using autologging

MLflow supports automatic logging for popular machine learning libraries. If you're using a library that is supported by
autolog, then MLflow tells the framework you're using to log all the metrics, parameters, artifacts, and models that the
framework considers relevant.
It is enabled by calling `autolog()`:

```python
import mlflow

mlflow.xgboost.autolog()
```

As soon as `mlflow.xgboost.autolog()` is called, MLflow will start a run within an experiment in Azure Machine Learning
to start tracking the experiment's run.

## Using custom logging

Alternatively, manually logging is possible. Common functions used with custom logging are:

- `mlflow.log_param()`: Logs a single key-value parameter. Use this function for an input parameter you want to log.
- `mlflow.log_metric()`: Logs a single key-value metric. Value must be a number. Use this function for any output you
  want
  to store with the run.
- `mlflow.log_artifact()`: Logs a file. Use this function for any plot you want to log, save as image file first.
- `mlflow.log_model()`: Logs a model. Use this function to create an MLflow model, which may include a custom signature,
  environment, and input examples.

Custom logging is then enabled by:

```python
mlflow.log_metric("accuracy", accuracy)
```

here used to specify the `accuracy` metric.