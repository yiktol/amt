---
title : "Create CI/CD Pipeline"
weight : 87
---


In the previous steps, you've analyzed your application, containerized it with App2Container and then deployed it to Amazon Elastic Container Service (ECS) and Amazon Elastic Container Registry (ECR).

Now you want to continue building on what you already deployed and introduce changes to the application source code. Luckily App2Container also provides capability to create a full CI/CD pipeline for your containerized application using services such as CodeBuild and CodeDeploy to build and deploy containers.

![app2container-discover](/static/create-pipeline/diagram.png)

In previous steps, when you ran "app2container generate app-deployment" command in the Create Deployment Artifacts section, App2Container creates a pipeline.json file under your application folder.

## 1. Prepare pipeline.json

1.  In your source Web Server, navigate to your application folder and open the pipeline.json file.

**Note:** Update the below path with your <java-app-id>.

::code[nano /root/app2container/<java-app-id>/pipeline.json]{showLineNumbers=true language=shell}

-   In the sourceInfo section, you can modify which Code Commit repository you want to use as source for your CI/CD pipeline.
-   In the releaseInfo section, you can define which cluster or service you wish to deploy your container to. Also, you could define it as "Beta" or "Prod" release.

![app2container-discover](/static/create-pipeline/pipeline-json-before.png)

To make this scenario more realistic, you will use an existing code repository to introduce changes to your application through a CI/CD pipeline to an ECS cluster that you have deployed earlier.

You already have a CodeCommit repo created for you in which you will place the application source code and then update the following parameter in the pipeline.json file.

-   repositoryName

Additionally, you will deploy our container into the existing Cluster and Service you've created in previous steps. Hence, you will need to insert the following parameters:

-   clusterName
-   serviceName
-   enabled to true (set attribute value to true)

2.  Now, go to AWS console to collect the repositoryName, clusterName and serviceName from your environment.

-   Navigate to CodeCommit service and copy the repository name as shown below

![ecs-cluster-1](/static/create-pipeline/code-commit-name.png)

-   Navigate to ECS service and copy the clusterName as shown below to your notepad.

![ecs-cluster-1](/static/deploy-to-aws/ecs-cluster-1.png)

-   Now, click to your Service and from the top of the screen, copy the name of your service.

![app2container-discover](/static/create-pipeline/ecs-service-name.png)

3.  You've have now your cluster Name and Service-Name. Now go to pipeline.json file and update the file with these.

-   Paste your repositoryName, clusterName, serviceName and set enabled to true as seen below.

![app2container-discover](/static/create-pipeline/pipeline-json-after.png)

Exit from the file with "ctrl+x" and type "y" and hit enter to save the file with the same name.


## 2. Generate Pipeline

Now your pipeline settings are defined and you are ready to generate a CI/CD pipeline.

1.  In the Source Web Serve, tun the following commands to generate the CI/CD pipeline CloudFormation Template

::code[app2container generate pipeline --application-id <java-app-id>]{showLineNumbers=true language=shell}

With this command, app2container creates

-   CodeCommit repository (in this scenario, we are using an existing CodeCommit repo)
-   Generates all buildspec files
-   Generate CloudFormation templates
-   Commit files to CodeCommit repository

And creates a CloudFormation template for creating a full CI/CD pipeline in your AWS environment.

:::code{showLineNumbers=true language=shell}
root@ip-10-0-0-202:~/app2container/java-tomcat-21fb1eb0# app2container generate pipeline --application-id java-tomcat-21fb1eb0
✔ Created CodeCommit repository
✔ Generated buildspec file(s)
✔ Generated CloudFormation templates
✔ Committed files to CodeCommit repository
👍 Pipeline resource template generation successful for application java-tomcat-21fb1eb0

💡 You're all set to use AWS CloudFormation to manage your pipeline stack.

Next Steps:
1. Edit the CloudFormation template as necessary.
2. Create a pipeline stack using the AWS CLI or the AWS Console. AWS CLI command:  

aws cloudformation deploy --template-file /root/app2container/java-tomcat-21fb1eb0/Artifacts/Pipeline/CodePipeline/ecs-pipeline-master.yml --capabilities CAPABILITY_NAMED_IAM --stack-name a2c-java-tomcat-21fb1eb0-ecs-pipeline-stack
:::

## 3. Create Pipeline

1.  In your web server, run suggested "aws cloudformation deploy" command from "app2container generate pipeline" command output.

-   With this command will deploy the CloudFormation stack which is going to create and configure all required AWS resources in your environment.
-   Your suggested command should be like below:

::code[aws cloudformation deploy --template-file /root/app2container/java-tomcat-21fb1eb0/Artifacts/Pipeline/CodePipeline/ecs-pipeline-master.yml --capabilities CAPABILITY_NAMED_IAM --stack-name a2c-java-tomcat-21fb1eb0-ecs-pipeline-stack]{showLineNumbers=true language=shell}

-   When you run this command, it will create a Cloudformation stack that ends with "pipeline-stack".

::alert[The process will take few mins. Go back to AWS Console to review the background activities during the deployment.]


:::code{showLineNumbers=true language=shell}
root@ip-10-0-0-202:~/app2container/java-tomcat-21fb1eb0# aws cloudformation deploy --template-file /root/app2container/<java-app-id>/Artifacts/Pipeline/CodePipeline/ecs-pipeline-master.yml --capabilities CAPABILITY_NAMED_IAM --stack-name a2c-<java-app-id>-ecs-pipeline-stack

Waiting for changeset to be created..
Waiting for stack create/update to complete
...
:::

2.  In AWS Console, Go to the [Cloudformation Console](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/) from services and Click "Stacks".

-   You will see that a stack has been created with name "a2c-java-tomcat-21fb1eb0-ecs-pipeline-stack".
-   Notice Nested stacks are triggered by main stack for each deployment.
-   You can see the triggered events under "events" tab for each stack.

![deploy-cloudformation-1](/static/create-pipeline/pipeline-cf.png)

3.  Navigate to CodePipeline Service under Developer Tools

-   You will see that your pipeline is being created.
-   Click your pipeline

![deploy-cloudformation-1](/static/create-pipeline/codepipeline-1.png)

4.  Once you click your pipeline, you will review its lifecycle and status.

-   Notice your Beta Release is in the pipeline.

![deploy-cloudformation-1](/static/create-pipeline/pipeline-2.png)

5.  Navigate to ECS Service and select your Cluster.

-   Review the changes in your Cluster.
-   You will see that new task is being activated as part of your CI/CD pipeline.

::alert[Since your cluster is set to have 1 Desired containers, once you launch your container, the previous deployment will be removed.]

![deploy-cloudformation-1](/static/create-pipeline/pipeline-ecs-new-service.png)

6.  Once ECS service is successfully launched, Go to your Source WebServer Console, You should see the below output stating that your application stack is created successfully!


:::code{showLineNumbers=true language=shell}
root@ip-10-0-0-202:~/app2container/java-tomcat-21fb1eb0# aws cloudformation deploy --template-file /root/app2container/<java-app-id>/Artifacts/Pipeline/CodePipeline/ecs-pipeline-master.yml --capabilities CAPABILITY_NAMED_IAM --stack-name a2c-<java-app-id>-ecs-pipeline-stack

Waiting for changeset to be created..
Waiting for stack create/update to complete
Successfully created/updated stack - a2c-<java-app-id>-ecs-pipeline-stack
:::

Once the pipeline terminates, navigate again to your Load Balancer URL and see your the application deployed in the browser. You should see your the website like this:

![Website before change](/static/create-pipeline/website-before-change.png)

## Congratulations

You've created a CI/CD pipeline and launched a BETA version of your application container into your ECS Cluster.

Additionally, You can review artifacts from your defined S3 bucket.

1.  Go to S3 services and navigate to your defined S3 bucket

-   App2Container Creates a bucket for each of your application in your S3 bucket

![deploy-cloudformation-1](/static/create-pipeline/s3-1.png)

-   And you can see the separate sub-folders for your ECS and pipeline. E.g. If you decide EKS as your target deployment, there will be a separate bucket for EKS in this location.

![deploy-cloudformation-1](/static/create-pipeline/s3-2.png)