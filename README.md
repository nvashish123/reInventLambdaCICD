# Lambda CICD 


Prerequisites for the builders session hands on :

1. You should be able to use an AWS account and log into it with admin role.
2. Basic familiarity with git commands

```bash

Create a new bucket to be used with sam package command to upload your lambda deployment package
or,
If you have an existing S3 bucket which you can use for this purpose, in your account, that's fine too

```

```bash
Create a new Codecommit repo from the AWS console, note down its endpoint
```

Perform the following steps to replicate the github repo into a CodeCommit repo -

Inside your cloud9 workspace, do the following - 

```bash
git clone https://github.com/nvashish123/reInventLambdaCICD.git
cd reInventLambdaCICD
git remote rm origin
git remote add origin <codecommit-endpoint.git>

hint : it should look like - https://git-codecommit.us-east-1.amazonaws.com/v1/repos/reInventCICD.git

now you should have a codecommit repo with the base code checked into it.
```

In order to build and deploy this serverless application, run the following commands - 

```bash
sam package  --output-template-file packaged.yaml  --s3-bucket vashi-lambda-code
```


```bash
sam deploy --template-file packaged.yaml --stack-name LambdaCICD --capabilities CAPABILITY_IAM
```

Create CodeBuild and code pipeline from console.

create a IAM role for CloudFormation - follow instructions here - https://docs.aws.amazon.com/lambda/latest/dg/build-pipeline.html#with-pipeline-create-cfn-role 

Start doing controlled deployments by adding the Lambda alias and preTrafficHook


