# Designing a ML model deployment solution

Often the goal is to integrate the ML model into an application. This can be done using **endpoints**. When you deploy a
model to an endpoint, you can choose between:

- Real-time predictions
- Batch predictions

For live, instant predictions use real-time predictions. If you want the model to score new data in batches, and save
the results you need batch predictions.

To decide this, consider the following:

- How often should predictions be generated?
- How soon are the results needed?
- Should predictions be generated individually or in batches?
- How much compute power is needed to execute the model?

## Number of predictions

Deciding on the number of predictions depends on the problem to be solved.
Generally you can generate predictions:

- Individually – Model revives a single row of data
- Batch – Model receives multiple rows of data

## Cost of compute

Also consider the cost of compute. If you need real-time predictions, you need compute that is always available and able
to return the results (almost) immediately. Container technologies like Azure Container Instance (ACI) and Azure
Kubernetes Service (AKS) are ideal for such scenarios as they provide a lightweight infrastructure for your deployed
model.

Alternatively, if you need batch predictions, you need compute that can handle a large workload. Ideally, you'd use a
compute cluster that can score the data in parallel batches by using multiple nodes.

## Monitoring the ML model

Monitoring should be performed in:

- The model
- The data
- The infrastructure

By collecting metrics we can make better decisions on any necessary next steps.

### Monitoring the model

MLflow can be used to train and track the ML model.
Test the trained model on a small subset of data to verify that the model is still performing good.
Fairness could also be tested, considering issues like AI bias.

### Monitoring the data

Remember to monitor data drift over time, and retrain models as required to maintain predictive accuracy.

### Monitoring the infrastructure

Monitor the infrastructure to minimize cost and optimize performance.
For example, you can monitor the compute utilization of your compute during training and during deployment. By reviewing
compute utilization, you know whether you can scale down your provisioned compute, or whether you need to scale out to
avoid capacity constraints.