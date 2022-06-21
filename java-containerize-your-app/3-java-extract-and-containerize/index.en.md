---
title : "Extract and Containerize"
weight : 84
---

In this module, you will transform your application with app2container. The Extract and Containerize phase activities depends on whether you are running all steps on the application server, or are using the application server for the analysis and a worker machine for containerization and deployment. 

![extract-and-containerize-intro](/static/extract-and-containerize/extract-containerize-overview.png)

::alert[In this scenario, you will be running all below steps on your Java Web Server.]

1.  Run the following commands to start the containerization process, replace <java-app-id> with your Java Application ID.
    
::code[app2container containerize --application-id <java-app-id>]{showLineNumbers=true language=shell}
    
-   Containerization process will take few minutes and App2Container will create the docker file and deployment.json file which includes all AWS deployment related configuration. App2Container also pre-validates your container to make sure it is working as expected.
-   Once containerization process is finished you should see output as below.
    
:::code{showLineNumbers=true language=shell}
# app2container containerize --application-id java-tomcat-21fb1eb0
✔ AWS prerequisite check succeeded
✔ Docker prerequisite check succeeded
✔ Extracted container artifacts for application
✔ Entry file generated
✔ Dockerfile generated under /root/app2container/java-tomcat-21fb1eb0/Artifacts
✔ Generated dockerfile.update under /root/app2container/java-tomcat-21fb1eb0/Artifacts
✔ Generated deployment file at /root/app2container/java-tomcat-21fb1eb0/deployment.json
✔ Deployment artifacts generated.
✔ Pre-validation succeeded.
👍 Containerization successful. Generated docker image java-tomcat-21fb1eb0

💡 You're all set to test and deploy your container image.

Next Steps:
1. View the container image with "docker images" and test the application with "docker run —name java-tomcat-21fb1eb0 -Pit java-tomcat-21fb1eb0".
2. When you're ready to deploy to AWS, adjust the appropriate fields in /root/app2container/java-tomcat-21fb1eb0/deployment.json to generate the desired deployment artifact. Note that by default "createEcsArtifacts" is set to true.
3. Generate deployment artifacts using "app2container generate app-deployment —application-id java-tomcat-21fb1eb0".
:::
    
2.  Now, you have your docker image ready. You can verify this by running the command below.

::code[docker images]{showLineNumbers=true language=shell}
    
-   As you can see, the container image was created for your Java Application with chosen container base image (ubuntu 18.4) and tag (latest).
    
:::code{showLineNumbers=true language=shell}
# docker images
REPOSITORY           TAG     IMAGE ID      CREATED        SIZE
java-tomcat-21fb1eb0 latest  5e382c3f2e6a  3 minutes ago  715MB
ubuntu               18.04   2c047404e52d  6 weeks ago    63.3MB
:::
    
With this step, you have successfully containerized your application. It is now ready to be deployed on AWS.
    
3.  You can also test your docker image locally. You can do that with the following commands:
    
::code[docker run -d <Image ID>]{showLineNumbers=true language=shell}
    
-   This will run the container in the background and output the container ID to the terminal:
    
:::code{showLineNumbers=true language=shell}
# docker run -d 5e382c3f2e6a
0884b713dcb60585b1bf273d8ee2f2a1cf0e122c1b1c6af9614bbadbb102ca03
:::
    
You can then inspect the running container to find the IP address and port that it is running on:
    
::code[docker inspect <Container ID>]{showLineNumbers=true language=shell}
    
-   This will dump a large amount of information about the container to the terminal output. You can find the IP address and port number the container is exposed on in the Network Settings section:
    
:::code{showLineNumbers=true language=json}
# docker inspect 0884b713dcb6
...
"Network Settings": {
    ...
    "Ports": {
        "8080/tcp": null
    }
    ...
    "IPAddress": "172.17.0.2"
:::
    
You can then curl the address and verify the endpoint is returning some html and javascript that is interpreted in the browser, for example:
    
:::code{showLineNumbers=true language=html}
# curl 172.17.0.2:8080
<!doctype html>
<html>
    <head>
        <meta charset="utf-8"/>
        <meta http-equiv="X-UA-Compatible" content="IE=edge" />
        <meta name="viewport" content="width=device-width, initial-scale=1" />

        <link href="css/bootstrap.css" rel="stylesheet" />
        <link href="css/snakes.css" rel="stylesheet" />

        <title>Does it have unicorns?</title>
    </head>
:::
    
4.  When you run "App2container containerize" command, it automatically creates a deployment.json file. This file includes AWS deployment configuration. You can update this file to customize the deployment in your AWS environment. E.g. You can select specific VPC for your target environment or define cpu/memory allocation setting for your container..
    

-   Open the file and review the configuration.
    
    NOTE: Update your file location in the below command from the command output.
    

::code[nano /root/app2container/<java-app-id>/deployment.json]{showLineNumbers=true language=shell}

-   Once you open the file, you will notice that there are various configuration settings for each AWS service. App2Container pre-builds those settings for you, but you can update these to adopt your environment.

In this workshop, you will need to check 2 places.

1.  Ensure "createEcsArtifacts" is set to "true".
    
-   ecsParameters section is for the settings when ECS (Elastic Container Service) is decided as the target environment. You need to set "createEcsArtifacts" to "false" if you dont want to deploy into ECS.
    
    ![java-deployment-1](/static/extract-and-containerize/java-deployment-1.png)
    
2.  Enter your Target vpc-id to deploy your application into your target network.
    
-   Go to AWS console and Navigate to the [VPC Console](https://us-west-2.console.aws.amazon.com/vpc/home?region=us-west-2#vpcs)
-   In your VPCs, find "TargetVPC" and copy the vpc-id as shown below.
    
    ![java-deployment-1](/static/extract-and-containerize/java-deployment-vpc.png)
    
-   In deployment.json file, Find "reuseResources" part and paste your copied for "vpcId" value.
    
    ![java-deployment-1](/static/extract-and-containerize/java-deployment-2.png)
    
3.  In deployment.json file, find "fireLensParameters" part.
    
-   Set "enableFireLensLogging" to "false".
    
    ![java-deployment-1](/static/extract-and-containerize/java-deployment-3.png)
    
4.  Exit from the file with "ctrl+x", type "y" and hit enter to save the file with the same name.
    
::alert[If you dont update your VPC-ID in deployment.json file, app2container will deploy your application into the Default VPC.]

Congratulations! You have defined all your target AWS configuration settings and ready to deploy your application.

In the next section you will create deployment artifacts for your deployment.