# Spark in Studio Notebook
### PYSPARK Optional
```shell
os.environ['PYSPARK_SUBMIT_ARGS'] = 'pyspark-shell'
```
### Notebook Bootstrap
```shell
import findspark
findspark.init()
import pyspark
sc = pyspark.SparkContext()
```

### Environment
```shell
export PYSPARK_SUBMIT_ARGS=PYSPARK_SUBMIT_ARGS
export SPARK_HOME=/usr/local/spark-3.1.1-bin-hadoop3.2
export PYTHONPATH=$SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.9-src.zip:$PYTHONPATH
export PATH=$SPARK_HOME/bin:$SPARK_HOME/python:$PATH
export PYSPARK_SUBMIT_ARGS=pyspark-shell
```


### Sample Code
```shell
%%time
import os
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

### Sample Code With SparkContext
```shell
%%time
import boto3
from pyspark import SparkContext, SparkConf
from pyspark.sql import SparkSession
SparkContext()
import sagemaker
from sagemaker import get_execution_role
import sagemaker_pyspark
role = get_execution_role()
jars = sagemaker_pyspark.classpath_jars()
classpath = ":".join(sagemaker_pyspark.classpath_jars())
spark = SparkSession.builder.config("spark.driver.extraClassPath", classpath).appName("Demo App").getOrCreate()
print("hello")
```
