---
title : "Install & Initialize App2Container"
weight : 82
---

## 1.App2Container Prerequisites

AWS App2Container requires set of prerequisites completed before you use it for the first time in your environment. In this lab, we have pre-installed all the prerequisites tools and created the source environment compatible with these prerequisites.

You can check the prerequisites check-list from [App2Container Prerequisites](https://docs.aws.amazon.com/app2container/latest/UserGuide/start-containerize-java-app.html)

## 2. Connect to the Webserver

1.  Switch to Oregon region (**us-west-2**). Go to **EC2** console, from the **Intances** connect to the **A2C-Java-Webserver** where your Java application runs.
    
    You have multiple options to connect your Web Server. Check [this article](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) for more detail on how to connect to your Linux instance.
    
    -   You can connect to your Web server via using AWS Systems Manager - [Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) . Session Manager provides secure and auditable instance management without the need to open inbound ports, maintain bastion hosts, or manage SSH keys.
        
        -   Go to your Amazon EC2 instances, select your Java Web Server and Click "Connect".
            
        -   Choose "Session Manager" and click "Connect"
            
            ![create-s3-bucket](/static/setting-up-app2container/session-manager.png)
            
    **OR**
    
    -   If you prefer to SSH into the webserver, you would need the following details.
        
        a. Public DNS Name of the webserver b. Username configured in the webserver c. SSHKey
        
        You can find this information in the output section of the cloudformation stack. The stack can be accessed by navigating to the [CloudFormation Console](https://us-west-2.console.aws.amazon.com/cloudformation/home?region=us-west-2#/) .
        
        | Parameter | Value |
        | --- | --- |
        | Username | ubuntu |
        | SSHKey | SSHKeyURL (You need to create .pem file from this key) |
        | Public DNS Name | JavaWebServerDNSName |
        
    

For SSH key, you need to create a .pem file via copying the key from the URL to your text editor and save it as .pem file.

## 3. Verify the instance Role

In the webserver, we need to verify if we have assigned the instance role correctly running 'aws sts get-caller-identity'

::code[aws sts get-caller-identity]{showLineNumbers=true language=shell}

You will see an output similar like this

:::code{showLineNumbers=true language=json}
$ aws sts get-caller-identity
{
    "UserId": "AROAYHNHTX3EBTZ7IF3PH:i-09b212f27269cef05",
    "Account": "0123456789012",
    "Arn": "arn:aws:sts::0123456789012:assumed-role/App2Container-EC2InstanceRole-F9LZMDBQW5XZ/i-09b212f27269cef05"
}
$ 
:::

## 4. Install App2Container

1.  Running App2Container commands on a Linux server requires elevated permissions. Run the commands as root using the command listed below when you log in to the server.

    ::code[sudo su -]{showLineNumbers=true language=shell}
    
2.  Download and install App2Container for Linux
    
    -   Once connected to the source linux server, Use the _curl_ command to download the App2Container installation package from Amazon S3.
    
    ::code[curl -o AWSApp2Container-installer-linux.tar.gz https://app2container-release-us-east-1.s3.us-east-1.amazonaws.com/latest/linux/AWSApp2Container-installer-linux.tar.gz]{showLineNumbers=true language=shell}
    
    ::alert[For this workshop we have used v1.12 of App2Container. In your own environment, you could replace 'v1.12' with 'latest' in the url above]
    
3.  Extract the package to a local folder on the server and navigate to that folder.
    
    ::code[tar xvf AWSApp2Container-installer-linux.tar.gz]{showLineNumbers=true language=shell}
    
4.  Run the install script from the folder where you extracted it and press "y" to accept the terms and conditions.

    ::code[./install.sh]{showLineNumbers=true language=shell}
    
5.  (optional) You can familiarize yourself with the available commands for App2Container
 
    ::code[app2container help]{showLineNumbers=true language=shell}
    

## 5. Initialize App2Container
The init command performs one-time initialization tasks for App2Container. This interactive command prompts for the information required to set up the local App2Container environment.

1.  First, You will need to create an Amazon S3 bucket where App2Container can store artifacts during the containerization process.
    
    -   Navigate to the S3 console and enter a unique name for your bucket (eg. app2container-Your Initials-Date) and click create and store the name of the bucket in a notepad (this will be required in subsequent steps). You can use all of the default bucket settings when creating the bucket.
    
    ![create-s3-bucket](/static/setting-up-app2container/s3-create-bucket.png)
    
2.  Login back to Source Web server and initiliaze app2container with "app2container init" command.
    
    ::code[app2container init]{showLineNumbers=true language=shell}

**You are prompted to provide the following information:**

| Parameter | Value |
| --- | --- |
| Workspace directory path | Leave Default. (A local directory where App2Container can store artifacts during the containerization process. The default is /root/app2container.) |
| Use AWS EC2 Instance profile | **Keep as Yes** |
| Which AWS Region to use? | us-west-2 |
| Amazon S3 bucket | Enter the Amazon S3 bucket name (app2container-Your-Initials-Date) you have created in previous step. (e.g. app2container-sd-jan-29) |
| Permission to collect usage metrics | Leave Default. (this option is to allow App2Container to collect information about the host operating system, application type, and the app2container commands that you run.) |
| Permission to Automatically upload logs and App2Container generated artifacts on crashes and internal errors | Leave Default. (this option is to allow App2Container to upload logs and artifacts on crashes and internal errors) |
| Whether to enforce signed images | Leave Default. (You can optionally require that images are signed using Docker Content Trust (DCT)) |

::alert[Make sure to enter the correct Amazon S3 bucket name (e.g. app2container-Your-Initials-Date) you've created in previous step once prompted. See example output below.]

::alert[Make sure to use Instance Profile during the configuration as seen below]

![create-s3-bucket](/static/setting-up-app2container/app2container-init.png)

Congratulations! You have installed and initialized App2Container and you are now ready to start containerizing your Java Application.

In the next modules, you will containerize your Web Application and deploy it to AWS ECS with App2Container.