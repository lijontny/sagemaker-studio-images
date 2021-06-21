# Spark in Studio Notebook
## Notebook Bootstrap
Run this from your notebook to set your spark context and local hostname (Just once in first start)
```shell
import pyspark
import os
sc = pyspark.SparkContext()
os.system('sudo -- sh -c "echo 127.0.0.1 $(hostname) >> /etc/hosts"')
```
## Sample Code
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
## Read Bucket key
####boto3
```shell
import boto3
s3 = boto3.resource('s3')

obj = s3.Object("sparktestaz12", "username.csv")
obj.get()['Body'].read().decode('utf-8')
```
####Hadoop s3a with IAM user credential
```shell
hadoopConf = sc._jsc.hadoopConfiguration()
hadoopConf.set("fs.s3a.access.key", "xxxxxxxx")
hadoopConf.set("fs.s3a.secret.key", "xxxxxxxx")
hadoopConf.set("fs.s3a.aws.credentials.provider", "org.apache.hadoop.fs.s3a.SimpleAWSCredentialsProvider")

file_s3 = "s3a://<bucket>/<key>"
df = spark.read.option("delimiter", ",").csv(file_s3)
df.show ()
```
####Hadoop s3a with IAM role
```shell
hadoopConf = sc._jsc.hadoopConfiguration()
hadoopConf.set("fs.s3a.aws.credentials.provider", "com.amazonaws.auth.DefaultAWSCredentialsProviderChain")

file_s3 = "s3a://<bucket>/<file>.csv"
df = spark.read.option("delimiter", ",").csv(file_s3)
df.show()
```