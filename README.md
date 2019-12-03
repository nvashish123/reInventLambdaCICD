# Lambda CICD 

git clone https://github.com/nvashish123/reInventLambdaCICD.git

Prerequisites for the builders session hands on :

1. You should be able to use an AWS account and log into it with admin role.


```bash

Create a new bucket to be used with sam package command to upload your lambda deployment package
or,
If you have an existing S3 bucket which you can use for this purpose, in your account, that's fine too

```


```bash
sam package  --output-template-file packaged.yaml  --s3-bucket vashi-lambda-code
```


```bash
sam deploy --template-file packaged.yaml --stack-name LambdaCICD --capabilities CAPABILITY_IAM
```


Create CodeBuild and code pipeline from console - 

create a IAM role for CloudFormation - follow instructions here - https://docs.aws.amazon.com/lambda/latest/dg/build-pipeline.html#with-pipeline-create-cfn-role 

Start doing controlled deployments by adding the Lambda alias and preTrafficHook


