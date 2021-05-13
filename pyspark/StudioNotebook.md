# Spark in Studio Notebook
### Notebook Bootstrap
Run this from your notebook to set your spark context and local hostname (Just once in first start)
```shell
import pyspark
import os
os.system('sudo -- sh -c "echo 127.0.0.1 $(hostname) >> /etc/hosts"')
sc = pyspark.SparkContext()
```
### Sample Code
```shell
%%time
import boto3
from pyspark import SparkContext, SparkConf
from pyspark.sql import SparkSession
import sagemaker
from sagemaker import get_execution_role
import sagemaker_pyspark
role = get_execution_role()
jars = sagemaker_pyspark.classpath_jars()
classpath = ":".join(sagemaker_pyspark.classpath_jars())
spark = SparkSession.builder.config("spark.driver.extraClassPath", classpath).appName("Demo App").getOrCreate()
```
