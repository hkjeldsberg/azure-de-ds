# Tuning hyperparameters

The choice of hyperparameter values can significantly affect the resulting model, making it important to select the best
possible values for your particular data and predictive performance goals.

**Hyperparameter tuning** is accomplished by training the multiple models, using the same algorithm and training data
but different hyperparameter values. The resulting model from each training run is then evaluated to determine the
performance metric for which you want to optimize (for example, accuracy), and the best-performing model is selected.

## Discrete hyperparameters

You can also select discrete values from any of the following discrete distributions:

- QUniform(min_value, max_value, q): Returns a value like round(Uniform(min_value, max_value) / q) * q
- QLogUniform(min_value, max_value, q): Returns a value like round(exp(Uniform(min_value, max_value)) / q) * q
- QNormal(mu, sigma, q): Returns a value like round(Normal(mu, sigma) / q) * q
- QLogNormal(mu, sigma, q): Returns a value like round(exp(Normal(mu, sigma)) / q) * q

## Continuous hyperparameters

Some hyperparameters are continuous - in other words you can use any value along a scale, resulting in an infinite
number of possibilities. To define a search space for these kinds of value, you can use any of the following
distribution types:

- Uniform(min_value, max_value): Returns a value uniformly distributed between min_value and max_value
- LogUniform(min_value, max_value): Returns a value drawn according to exp(Uniform(min_value, max_value)) so that the
  logarithm of the return value is uniformly distributed
- Normal(mu, sigma): Returns a real value that's normally distributed with mean mu and standard deviation sigma
- LogNormal(mu, sigma): Returns a value drawn according to exp(Normal(mu, sigma)) so that the logarithm of the return
  value is normally distributed

## Defining a search spate

A search space can be created by passing multiple values to the hyperparmeters for a `job()`:

```python
from azure.ai.ml.sweep import Choice, Normal

command_job_for_sweep = job(
    batch_size=Choice(values=[16, 32, 64]),
    learning_rate=Normal(mu=10, sigma=3),
)
```

## Sampling methods

The specific values used in a hyperparameter tuning run, or sweep job, depend on the type of sampling used.

There are three main sampling methods available in Azure Machine Learning:

- **Grid sampling**: Tries every possible combination.
- **Random sampling**: Randomly chooses values from the search space.
    - **Sobol**: Adds a seed to random sampling to make the results reproducible.
- **Bayesian sampling**: Chooses new values based on previous results.

## Sweep job

To run a sweep job, you need to create a training script just the way you would do for any other training job, except
that your script must:

- Include an argument for each hyperparameter you want to vary.
- Log the target performance metric with MLflow. A logged metric enables the sweep job to evaluate the performance of
  the trials it initiates, and identify the one that produces the best performing model.