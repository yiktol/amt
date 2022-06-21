---
title : "Containerize your Java App"
description: AWS
weight : 81
---

## AWS App2Container Overview

[App2Container](https://aws.amazon.com/app2container) (A2C) is a command line tool to help you lift and shift applications that run in your on-premises data centers or on virtual machines, so that they run in containers that are managed by Amazon ECS, Amazon EKS, or AWS App Runner.

Moving legacy applications to containers is often the starting point toward application modernization. There are many benefits to containerization:

-   Reduces operational overhead and infrastructure costs.
-   Increases development and deployment agility.
-   Standardizes build and deployment processes across an organization.

## How AWS App2Container works

You can use App2Container to generate container images for one or more applications running on Windows or Linux. This includes commercial off-the-shelf applications (COTS). App2Container does not need source code for the application to containerize it.

You can use App2Container directly on the application servers that are running your applications. If your server does not meet the requirements to containerize your application and deploy it to AWS, or if you do not want to install the Docker engine on the application server, you can set up and use a worker machine.

![app2container-intro](/static/app2container/app2container-diagram.png)

App2Container performs following tasks in 4 main categories:

1.  **Discover and Analyze**.

-   Creates an inventory list for the application server that identifies running Java application that could be candidates to containerize.
-   Analyzes the run-time dependencies of supported applications that are running, including cooperating processes and network port dependencies.

2.  **Extract and Containerize**.

-   Extracts application artifacts for containerization and generates a Dockerfile.
-   Initiates builds for the application container.

3.  **Create Deployment Artifacts**.

-   Generates the artifacts needed to deploy your application container in AWS.
-   Builds an AWS CloudFormation template to configure required compute, network, and security infrastructure to deploy containers using Amazon ECS or Amazon EKS.
-   Pre-fills key values in the artifacts based on your profile, the application analysis, and best practices.

4.  **Deploy to AWS Cloud**.

-   Deploys the containers on Amazon ECS or Amazon EKS.

5.  **Create CI/CD pipeline**.

-   App2Container creates a CI/CD pipeline with AWS CodePipeline and associated services, to automate building and deploying your application containers.

6.  **Update your application**

-   Pushes a change to the application source code and deploys it to the target container running on Fargate.


::alert[**AWS App2Container** is offered at no additional charge. You are charged only when you use other AWS services to run your containerized application, such as Amazon ECR, Amazon ECS, Amazon EKS and AWS AppRunner]

In this lab you will containerize a legacy Java application on Linux Ubuntu using App2Container, and deploy it on Amazon ECS (Elastic Container Service).
