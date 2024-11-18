# Compute targets on Azure ML

Compute targets are physical or virtual computers on which jobs are run.

## Types of compute targets

The following types of compute are available on Azure ML:

- Compute instance: Notebook friendly
- Compute clusters: Ideal for training models and processing in parallel, and for production (scales automatically)
- Kubernetes clusters: Gives more control over how the compute is configured and managed
- Attached compute: Attach existing compute (Azure VM, Azure Databricks)
- Serverless compute: On-demand compute for training jobs

## Creating a compute instance

To create a compute instance with Python SDK:

```python
from azure.ai.ml.entities import ComputeInstance

ci_basic_name = "basic-ci-12345"
ci_basic = ComputeInstance(
    name=ci_basic_name,
    size="STANDARD_DS3_v2"
)
ml_client.begin_create_or_update(ci_basic).result()
```

Note that a compute instance can only be assigned to _one_ user.

## Creating a compute cluster

Similarly, a compute cluster can be used for the three scenarios:

- Pipeline job built in the Designer
- Automated ML job
- Running a job script

