# Lambda CICD 


**** Prerequisites for the builders session hands on ****

```bash

a. You should be able to use an AWS account and log into it with admin role.
b. Basic familiarity with git commands


```

 - Instructions for the hands-on session:

1. Create a new bucket to be used with sam package command to upload your lambda deployment package
or, If you have an existing S3 bucket which you can use for this purpose in your account, that's fine too.
Also, Cloud9 is the IDE of choice for this session to avoid time installing cli tools. Create a new workspace in Cloud9


```bash
aws s3api create-bucket --bucket <your_bucket_name>
```

2. Create a new CodeCommit repo from the AWS console, note down its endpoint.


3. Perform the following steps to replicate the github repo into a CodeCommit repo -

Inside your cloud9 workspace, do the following - 

```bash
git clone https://github.com/nvashish123/reInventLambdaCICD.git
cd reInventLambdaCICD
git remote rm origin
git remote add origin <codecommit-originendpoint.git>

hint : codecommit-originendpoint.git should look like - https://git-codecommit.us-east-1.amazonaws.com/v1/repos/reInventCICD.git

make a change in the README file, like an extra space or something ( sometimes git does not recognize that the origin has changed unless a code change is forced)

then, checkin everything to the new CodeCommit repo :
    
git add *
git commit -m "initial commit"
git push origin master

```



now you should have a codecommit repo with the base code checked into it.


4. In order to build and deploy this serverless application, run the following commands - 

```bash
sam package  --output-template-file packaged.yaml  --s3-bucket <your-bucket-name>
```


```bash
sam deploy --template-file packaged.yaml --stack-name <stack-name-here> --capabilities CAPABILITY_IAM
```

Verify that the new serverless application has been created in your AWS console.

5. Open the buildspec.yml file from the root of this project and update the S3 bucket name to your S3 bucket name in EXPORT statement

6. Checkin the buildspec.yml to the repo

```bash

git add buildspec.yml
git commit -m "updated buildspec.yml"
git push origin master

```



7. Create CodeBuild project from the AWS console using your CodeCommit repo as the source.

8. Update the IAM role for this CodeBuild project to add S3FullAccess policy.

9. At this stage, we have created a CodeBuild project, and we will use it in the Codepipeline in next steps.

10. Create a new IAM role for CloudFormation - [follow instructions here] - (https://docs.aws.amazon.com/lambda/latest/dg/build-pipeline.html#with-pipeline-create-cfn-role)

This role will be used in next step when we will create a CodePipeline


11. Create CodePipeline from console using the following details :

```bash
    
    a. Use CodeCommit repo as source
    b. Use the Codebuild project we created above for the Build stage
    c. Use Cloudformation for the deployment stage - choose 'create or update a changeset' as action
    d. Review and create the Pipeline
    
```
The pipeline should kick off automatically. Let it finish, but you would notice that it has not deployed the Lambda function just yet.
that is because, we have only created a Cloudformation changeset, but not executed it yet.
So, edit the Pipeline to add another Cloudformation action as 'execute changeset' in order to execute the chageset.
Make a code change in Lambda, check in the code to CodeCommit, and watch the Pipeline getting kicked off automatically.
Test your Lambda function to ensure the new code is deployed. 

At this point, you have successfully created a CI/CD pipeline for your serverless function.


12. Start doing controlled deployments by adding the Lambda alias and preTrafficHook - 

```bash

Go to the template.yml file, uncomment line 20 - AutoPublishAlias: prod
Update the Lambda function code in app.js to make the changes visible.
Save and check in the changes
Pipeline should kick off automatically and now your Lambda function should have an Alias created with the name 'prod'


Now, uncomment next 4 lines, from line 21-25 :
    
    # DeploymentPreference:
      #   Type: Canary10Percent5Minutes
      #   Hooks:
      #     PreTraffic: !Ref CICDPreHookFunction
      
Also, uncomment the CICDPreHookFunction PreTraffic Hook Lambda function starting at line 33      

Update the Lambda function code in app.js to make the changes visible for testing purpose.

Save and check in the changes
Pipeline should kick off automatically and now your Lambda function should have a controlled deployment with Canary10Percent5Minutes deployment setting.
So, the new Lambda version will be called for the 10% of the traffic, for the first 5 minutes. After 5 minutes, all the traffic will go to the new lambda version code.


```

13. Simulate a test-case failure and automatic rollback scenrio :

```bash

update the source code of the preTrafficHook.js lambda function to return the status as 'Failed' on line 25
Update the Lambda function code in app.js to make the changes visible for testing purpose.
Save and check in the changes
The Pipeline should fail at the deploy stage and the new code should not be deployed.
This is intentional as our Lambda pretrafficHook returned a test failure and the deployment was rolled back.

```



