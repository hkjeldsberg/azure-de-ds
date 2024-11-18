# Environments in Azure ML

Python code runs in the context of a virtual environment that defines the version of the Python runtime to be used as
well as the installed packages available to the code. In most Python installations, packages are installed and managed
in environments using `conda` or `pip`.

Environments on Azure use the prefix `AzureML`, e.g.:

```python
env = ml_client.environments.get("AzureML-sklearn-0.24-ubuntu18.04-py37-cpu", version=44)
print(env.description, env.tags)
```

Environment can be specified in the `command` method:

```python
from azure.ai.ml import command

# configure job
job = command(
    code="./src",
    command="python train.py",
    environment="AzureML-sklearn-0.24-ubuntu18.04-py37-cpu@latest",
    compute="aml-cluster",
    display_name="train-with-curated-environment",
    experiment_name="train-with-curated-environment"
)

# submit job
returned_job = ml_client.create_or_update(job)
```

A specific environment configuration may also be provided as such:

```python
from azure.ai.ml.entities import Environment

env_docker_conda = Environment(
    image="mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04",
    conda_file="./conda-env.yml",
    name="docker-image-plus-conda-example",
    description="Environment created from a Docker image plus Conda environment.",
)
ml_client.environments.create_or_update(env_docker_conda)
```


