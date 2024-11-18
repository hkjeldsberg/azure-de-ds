# Pipelines in Azure ML

To build a pipeline, you will need to create a **component**. Components allow you to create reusable scripts that can
easily be shared across users within the same Azure Machine Learning workspace.

A component consists of three parts:

- Metadata: Includes the component's name, version, etc.
- Interface: Includes the expected input parameters (like a dataset or hyperparameter) and expected output (like metrics
  and artifacts).
- Command, code and environment: Specifies how to run the code.

## Creating a Component

A component will consist of a Python file and a configuration file (yaml).
Example Python file:

```python

# import libraries
import argparse
import pandas as pd
import numpy as np
from pathlib import Path
from sklearn.preprocessing import MinMaxScaler

# setup arg parser
parser = argparse.ArgumentParser()

# add arguments
parser.add_argument("--input_data", dest='input_data',
                    type=str)
parser.add_argument("--output_data", dest='output_data',
                    type=str)

# parse args
args = parser.parse_args()

# read the data
df = pd.read_csv(args.input_data)

# remove missing values
df = df.dropna()

# normalize the data    
scaler = MinMaxScaler()
num_cols = ['feature1', 'feature2', 'feature3', 'feature4']
df[num_cols] = scaler.fit_transform(df[num_cols])

# save the data as a csv
output_df = df.to_csv(
    (Path(args.output_data) / "prepped-data.csv"),
    index=False
)
```

Corresponding config file (yaml):

```yaml
$schema: https://azuremlschemas.azureedge.net/latest/commandComponent.schema.json
name: prep_data
display_name: Prepare training data
version: 1
type: command
inputs:
  input_data:
    type: uri_file
outputs:
  output_data:
    type: uri_file
code: ./src
environment: azureml:AzureML-sklearn-0.24-ubuntu18.04-py37-cpu@latest
command: >-
  python prep.py 
  --input_data ${{inputs.input_data}}
  --output_data ${{outputs.output_data}}
```

## Creating a pipeline

To build a pipeline that first prepares the data, and then trains the model, use the following code:

```python
from azure.ai.ml.dsl import pipeline


@pipeline()
def pipeline_function_name(pipeline_job_input):
    prep_data = loaded_component_prep(input_data=pipeline_job_input)
    train_model = loaded_component_train(training_data=prep_data.outputs.output_data)

    return {
        "pipeline_job_transformed_data": prep_data.outputs.output_data,
        "pipeline_job_trained_model": train_model.outputs.model_output,
    }
```

## Running a pipeline

After configuration, you can submit a pipeline job by running the code:

```python
# submit job to workspace
pipeline_job = ml_client.jobs.create_or_update(
    pipeline_job, experiment_name="pipeline_job"
)
```

To schedule the job, you can use the `JobSchedule`class and the `RecurrenceTrigger`:

```python
from azure.ai.ml.entities import RecurrenceTrigger, JobSchedule

schedule_name = "run_every_minute"

recurrence_trigger = RecurrenceTrigger(
    frequency="minute",
    interval=1,
)

job_schedule = JobSchedule(
    name=schedule_name, trigger=recurrence_trigger, create_job=pipeline_job
)

job_schedule = ml_client.schedules.begin_create_or_update(
    schedule=job_schedule
).result()
```

