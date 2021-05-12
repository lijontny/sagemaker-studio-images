# Spark Image For Studio Notebook
### Docker File 
- Base Image - *python:3.7*
- Ref Project:- https://github.com/aws-samples/sagemaker-studio-custom-image-samples/tree/main/examples/python-poetry-image

### Docker Build
```shell
REGION=<aws-region>
ACCOUNT_ID=<account-id
docker build -t ${Account_ID}.dkr.ecr.${REGION}.amazonaws.com/smstudio-custom:spark .
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${Account_ID}.dkr.ecr.${REGION}.amazonaws.com
docker push ${Account_ID}.dkr.ecr.${REGION}.amazonaws.com/smstudio-custom:spark
```
### Note
This is a sample project for running spark context locally. More optimisation required before production use 
