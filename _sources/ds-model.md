# Finding the "best" model by using AutoML

Going through trial and error to find the best performing model can be time-consuming. Instead of manually having to
test and evaluate various configurations to train a machine learning model, you can automate it with automated machine
learning or `AutoML`.

AutoML can also apply the following (optional) pre-processing transformations:

- Missing value imputation to eliminate nulls in the training dataset.
- Categorical encoding to convert categorical features to numeric indicators.
- Dropping high-cardinality features, such as record IDs.
- Feature engineering (for example, deriving individual date parts from DateTime features)

By default, AutoML will perform featurization on your data.

NB: AutoML needs a **MLTable** data asset as input


## Specifying metrics
The primary metric is the target performance metric for which the optimal model will be determined. 
List all the metrics by running:

```python
from azure.ai.ml.automl import ClassificationPrimaryMetrics

metrics = list(ClassificationPrimaryMetrics)
print(metrics)
```

## Submitting an AutoML experiment:

Run and monitor with the following code:
```python
# submit the AutoML job
returned_job = ml_client.jobs.create_or_update(
    classification_job
)

aml_url = returned_job.studio_url
print("Monitor your job at", aml_url)
```