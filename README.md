## Layout of the SageMaker ModelBuild Source Code

Here is the folder structure:

```
.
├── LICENSE
├── README.md
├── pipelines
│   ├── __init__.py
│   ├── __version__.py
│   ├── _utils.py
│   ├── customer_churn
│   │   ├── __init__.py
│   │   ├── evaluate.py
│   │   ├── pipeline.py
│   │   └── preprocess.py
│   ├── get_pipeline_definition.py
│   └── run_pipeline.py
├── setup.cfg
├── setup.py
├── tests
│   └── test_pipelines.py
└── tox.ini

```

The source codes are modified from this example:
 https://aws.amazon.com/blogs/machine-learning/building-automating-managing-and-scaling-ml-workflows-using-amazon-sagemaker-pipelines/

The following two steps need to be done to run the sample code.

In this example, we are solving the customer churn problem using synthetic data.

## Step 1: Get Dataset 
Get the data and save to your S3 data bucket:
```
!aws s3 cp s3://sagemaker-sample-files/datasets/tabular/synthetic/churn.txt ./
import boto3
import os
import sagemaker
prefix = 'sagemaker/DEMO-xgboost-churn'
region = boto3.Session().region_name
default_bucket = sagemaker.session.Session().default_bucket()
RawData = boto3.Session().resource('s3')\
.Bucket(default_bucket).Object(os.path.join(prefix, 'data/RawData.csv'))\
.upload_file('./churn.txt')
print(os.path.join("s3://",default_bucket, prefix, 'data/RawData.csv'))

```


## Step 2: Modify pipepine.py code


Be sure to replace the “InputDataUrl” default parameter with the Amazon S3 URL obtained in previous step:
```
input_data = ParameterString(
    name="InputDataUrl",
   default_value=f"s3://<default_bucket>/RawData.csv",
)

```
The following section provides an overview of how the code is organized. In particular, `pipelines/pipelines.py` contains the core of the business logic for this problem. It has the code to express the ML steps involved in generating an ML model. You will also find the code for that supports preprocessing and evaluation steps in `preprocess.py` and `evaluate.py` files respectively.

Once you understand the code structure described below, you can inspect the code and you can start customizing it for your own business case. This is only sample code, and you own this repository for your business use case. Please go ahead, modify the files, commit them and see the changes kicking off the SageMaker pipelines in the CICD system.

A description of some of the artifacts is provided below:

<br/><br/>
Your pipeline artifacts, which includes a pipeline module defining the required `get_pipeline` method that returns an instance of a SageMaker pipeline. This is the core business logic, and if you want to create your own folder, you can do so, and implement the `get_pipeline` interface as illustrated here.

```
|-- pipelines
|   |-- customer_churn
|   |   |-- evaluate.py
|   |   |-- __init__.py
|   |   |-- pipeline.py
|   |   `-- preprocess.py

```
<br/><br/>
Utility modules for getting pipeline definition jsons and running pipelines (you do not typically need to modify these):

```
|-- pipelines
|   |-- get_pipeline_definition.py
|   |-- __init__.py
|   |-- run_pipeline.py
|   |-- _utils.py
|   `-- __version__.py
```
<br/><br/>
Python package artifacts:
```
|-- setup.cfg
|-- setup.py
```
<br/><br/>
A stubbed testing module for testing your pipeline as you develop:
```
|-- tests
|   `-- test_pipelines.py
```
<br/><br/>
The `tox` testing framework configuration:
```
`-- tox.ini
```