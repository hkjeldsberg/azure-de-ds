# Running a training script in Azure ML

## From Notebook to Script

If you've worked primarily with notebooks, we will need to convert the notebook to a script.
This includes removing non-essential code, refactoring code into functions and adding tests.

E.g. changing:

```python
# read and visualize the data
print("Reading data...")
df = pd.read_csv('diabetes.csv')
df.head()

# split data
print("Splitting data...")
X, y = df[['Pregnancies', 'PlasmaGlucose', 'DiastolicBloodPressure', 'TricepsThickness', 'SerumInsulin', 'BMI',
           'DiabetesPedigree', 'Age']].values, df['Diabetic'].values

from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)
```

to

```python
def main(csv_file):
    # read data
    df = get_data(csv_file)

    # split data
    X_train, X_test, y_train, y_test = split_data(df)


# function that reads the data
def get_data(path):
    df = pd.read_csv(path)

    return df


# function that splits the data
def split_data(df):
    X, y = df[['Pregnancies', 'PlasmaGlucose', 'DiastolicBloodPressure', 'TricepsThickness',
               'SerumInsulin', 'BMI', 'DiabetesPedigree', 'Age']].values, df['Diabetic'].values

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.30, random_state=0)

    return X_train, X_test, y_train, y_test
```

## Running a script
A command job to run a file named `train.py` on the compute cluster `aml-cluster` can be done as follows:
```python
from azure.ai.ml import command

# configure job
job = command(
    code="./src",
    command="python train.py",
    environment="AzureML-sklearn-0.24-ubuntu18.04-py37-cpu@latest",
    compute="aml-cluster",
    display_name="train-model",
    experiment_name="train-classification-model"
    )

# submit job
returned_job = ml_client.create_or_update(job)
```

