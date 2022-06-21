---
title : "Create Deployment Artifacts"
weight : 85
---

In this step, App2Container generates artifacts needed to deploy your application container in AWS, based on application analysis and best practices.

::alert[In this scenario, you will deploy your application container into Amazon Elastic Container Register (Amazon ECR) with Amazon ECS.]

![app2container-intro](/static/create-deployment-artifacts/create-deployment-overview.png)

1.  Run the following command to create the AWS CloudFormation template
    
::code[app2container generate app-deployment --application-id <java-app-id>]{showLineNumbers=true language=shell}
    
2.  The process will take few mins. You should see the below output.
    
:::code{showLineNumbers=true language=shell}
# app2container generate app-deployment --application-id java-tomcat-21fb1eb0
✔ AWS prerequisite check succeeded
✔ Docker prerequisite check succeeded
✔ Created ECR Repository
✔ Registered ECS Task Definition with ECS
✔ Uploaded CloudFormation resources to S3 Bucket: app2container-bucket-0121
✔ Generated CloudFormation Master template at: /root/app2container/java-tomcat-6c144e52/EcsDeployment/ecs-master.yml
👍 CloudFormation templates and additional deployment artifacts generated successfully for application java-tomcat-21fb1eb0

💡 You're all set to use AWS CloudFormation to manage your application stack.
:::
    

With this command, App2container does the below steps:

1.  Creates an Amazon ECR repository and copies there the application container image you have created.
-   Verify this by logging into the AWS Console and navigating to Elastic Container Service (ECS) Service.
    
    -   Click "Amazon ECR" on the left pane.
    -   You will see that your container is automatically registered with this ECR repository.
    
    ![ecr-1](/static/create-deployment-artifacts/deployment-ecr.png)
    
    -   Once you click into your Repository, you will see that your container image with the "latest" tag is deployed.
    
    ![ecr-2](/static/create-deployment-artifacts/deployment-ecr-2.png)

::alert[You can update this tag and other container parameters in "analysis.json" file.]

2.  Creates a local ECS Task Definition.
    
    -   NOTE: ECS Task definitions is required to run docker images in ECS. For further information about Task Definitions, you can check [Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html) .
    
3.  Uploads all CloudFormation resources to your selected S3 bucket.
    
    -   NOTE: You have defined this bucket earlier in Install & Initialize App2container section.
    -   Navigate to the AWS S3 console and click on the bucket created by you.
    -   You will see that all the deployment artifacts are uploaded in this bucket. 

![ecs-task-definiton](/static/create-deployment-artifacts/deployment-s3-bucket.png)
    
4.  Creates a CloudFormation template.
    
    -   App2Container reviews your environment and updates the CloudFormation template with configuration defined in the previous steps, results of application analysis
    -   This CloudFormation template describes all required resources and their dependencies so you can launch and configure them together as a stack.
    -   On your web-server, locate the ecs-master.yml file location from your "app2container generate app-deployment" command output and open the file.
    

::alert[You do not need to make any changes in this file now, but be aware that you can customize it based on your requirements]

::code[cat /root/app2container/<java-app-id>/EcsDeployment/ecs-master.yml]{showLineNumbers=true language=shell}

Congratulations!, you are now all set and ready to deploy your Java Application to AWS ECS! Continue with the next section to trigger the deployment.