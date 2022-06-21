---
title : "Deploy to AWS Cloud"
weight : 86
---

![app2container-intro](/static/deploy-to-aws/Deploy-to-AWS-overview.png)

At this stage:

-   Your application container image is already present in ECR (Elastic Container Registry) and ECS Task definition is created and registered.
-   App2container created the Master Cloudformation template for you to automatically deploy and configure all required resources in AWS.

In this section, you will launch your Cloudformation template and review the deployment process in AWS console.

1.  In your web server, run suggested command from "app2container generate app-deployment" output. Update the <java-app-id> with your ID.
    
::code[app2container generate app-deployment --application-id <java-app-id> --deploy]{showLineNumbers=true language=shell}
    
-   When you run this command, it will create a Cloudformation stack named as "a2c-<java-app-id>-ECS".
    
::alert[The process will take few mins. Go back to AWS Console to review the background activities during the deployment.]
    

:::code{showLineNumbers=true language=shell}
# app2container generate app-deployment --application-id <java-app-id> --deploy

✔ AWS prerequisite check succeeded
✔ Docker prerequisite check succeeded
✔ Processing application java-tomcat-2b5c6427...
✔ Created ECR repository 119729248552.dkr.ecr.us-west-2.amazonaws.com/java-tomcat-2b5c6427
✔ Pushed docker image 119729248552.dkr.ecr.us-west-2.amazonaws.com/java-tomcat-2b5c6427:latest to ECR repository
✔ Local ECS Task Definition file created
✔ Uploaded CloudFormation resources to S3 Bucket: a2c-assets-nov
✔ Generated CloudFormation Master template at: /root/app2container/java-tomcat-2b5c6427/EcsDeployment/ecs-master.yml
✔ Initiated CloudFormation stack creation. This may take a few minutes. To track progress, open the AWS CloudFormation console
✔ AWS CloudFormation Stack Created: https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/stackinfo?stackId=arn%3Aaws%3Acloudformation%3Aus-west-2%3A119729248552%3Astack%2Fa2c-java-tomcat-2b5c6427-ECS%2F36aa7bc0-43ca-11ec-8c18-022ade7f0375
✔ Stack a2c-java-tomcat-2b5c6427-ECS deployed successfully!
👍 Deployment successful for application java-tomcat-2b5c6427

💡 The URL to your Load Balancer Endpoint is:
a2c-j-Publi-JIAOSBRP1JTI-2133956503.us-west-2.elb.amazonaws.com
💡 Successfully created ECS stack a2c-java-tomcat-2b5c6427-ECS. Check the AWS CloudFormation Console for additional details.

3. Set up a pipeline for your application stack using app2container:

        app2container generate pipeline --application-id java-tomcat-2b5c6427
:::

2.  Navigate to the [Cloudformation Console](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks)
    
-   You will see that a stack has been created with name "a2c-java-tomcat-21fb1eb0-ECS".
-   Notice Nested stacks are triggered by main stack for each deployment. E.g. A separate stacks for ALB (Application LoadBalancer) and ECS Clusters.
-   You can see the triggered events under "events" tab for each stack.

![deploy-cloudformation-1](/static/deploy-to-aws/deploy-cloudformation-1.png)

3.  After ALB stack and Cluster stack have been completed, Navigate to ECS Service.
    
-   Click to "Clusters" on the left pane.
-   You will see new ECS Cluster is being created.
-   Click to "Cluster" Name as highlighted in below screen.

::alert[Notice that your container is deployed into FARGATE. This has been selected in deployment.json file in extract and containerize steps. You can change it to "EC2", if you prefer to have the control on the host servers and manage them.]

![deploy-cloudformation-2](/static/deploy-to-aws/ecs-cluster-1.png)

::alert[[AWS Fargate](https://aws.amazon.com/fargate) is a serverless compute engine for containers. With FARGATE, you dont need to provision and manage servers and you only pay for the resources required to run your containers.]

1.  After the cluster is created, under Services tab, you will see your application deployed and service status is “ACTIVE"

-   You can see the values of "Desired Tasks" and "Running Tasks" and Launch type are defined by App2container

![deploy-cloudformation-2](/static/deploy-to-aws/ecs-cluster-2.png)

5.  Click Tasks tab. Here you can see that Task has been created and is running.

-   Click on the Task to see its details.

![deploy-cloudformation-2](/static/deploy-to-aws/ecs-cluster-3.png)

6.  Click Clusters and when you are inside the clusters, Click Services tab and go into Service.
    
-   While you are inside the Service.
-   Under Load Balancing section, you will see that Target Group has automatically been created for your container.
-   Click Target Group.
    
::alert[Notice that , VPC, subnet and Security Groups are also defined. You can update those based on your environment requirements.]
    
![deploy-cloudformation-2](/static/deploy-to-aws/ecs-cluster-4.png)
    
7.  Inside the Target Groups, Review the Target Group configuration
    
-   [Target Groups](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-target-groups.html) are used to route requests to one or more registered targets. App2container creates Target Groups and registers it with your containers and Loadbalancer.
-   Click LoadBalancer
    
![deploy-cloudformation-2](/static/deploy-to-aws/loadbalancer-1.png)
    
8.  In the Loadbalancers section, Review the Load balancer configurations for your application.
    
-   Copy the Public DNS Name and paste it in your browser.

![deploy-cloudformation-2](/static/deploy-to-aws/loadbalancer-2.png)
    

## Congratulations

9.  Congratulations!! You have containerized your application and deployed it into Amazon ECS. 

![deploy-cloudformation-2](/static/deploy-to-aws/java-last.png)

10. Go to CloudFormation Service and notice the multiple stacks are created by App2container and all the required resources for your containerized application are automatically created for you. 

![deploy-cloudformation-2](/static/deploy-to-aws/cloudformation-ecs-final.png)

11. Go to your Source WebServer Console, You should see the below output stating that your application stack is created successfully!

![deploy-cloudformation-2](/static/deploy-to-aws/stack-final.png)