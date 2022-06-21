---
title : "Update your Application with CI/CD pipeline"
weight : 88
---

## Make changes to your application source code

Great! Now you have your CI/CD pipeline in place and fully integrated with your Amazon ECS Cluster, you can now make a change in your application see your CI/CD pipeline in action.

To provide you with a great development experience we have created an IDE environment using [AWS Cloud9](https://aws.amazon.com/cloud9/) .

::alert[**AWS Cloud9** is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with a browser.]


## 1. Navigate to the Cloud9 service and open the available IDE instance.

1.  Navigate to the Cloud9 service using the AWS Console. You will see your IDE has been created for you. Click "Open IDE".

![cloud9](/static/create-pipeline/cloud9-env.png)

2.  Once you open your IDE, You should see a welcome message and a terminal window like on the image below.
    
![cloud9](/static/create-pipeline/cloud9-1.png)
    
3.  Notice the "app-source-code" directory on the left hand side. This directory is your git repository created in your account.
    
4.  Notice the "terminal" at the bottom of this screen. You will use this terminal to execute commands during this section.
    

## 2. Configure the environment with the application source code.


1.  Run the following commands in the terminal to pull the source code off your application and App2Container created artifacts from your CodeCommit repository.
    
:::code{showLineNumbers=true language=shell}
cd ~/environment/app-source-code
git pull
wget https://ee-assets-prod-us-east-1.s3.us-east-1.amazonaws.com/modules/d875db3580ac47c5b691eb3303cd71fb/v3/unicorns-movies.zip
unzip unicorns-movies.zip 
cp -r ./unicorns-movies/** ./
rm -rf ./unicorns-movies ./unicorns-movies.zip
:::
    
2.  On the left pane, confirm that the source code (src) and app2container created artifacts (Dockerfile and buildspec.yml) are pulled inside your folder directory.
    
![cloud9](/static/create-pipeline/cloud9-directory.png)
    
3.  Navigate and review `build.sh` file inside your folder. This file includes the build scripts of your application and once executed, it packages your source-code into a deployable `ROOT.war` file.
    
::alert[Do not make any changes to `build.sh` file.]{header='Important'}
    
![build-sh-file](/static/create-pipeline/build-sh-file.png)
    
::alert[After each update in your source code, this scripts will need to be run this to rebuild your application.]
    
4.  You can also run `git status` command while you are in app-source-code directory to see which files were added.
    
:::code{showLineNumbers=true language=shell}
cd ~/environment/app-source-code
git status
:::
    
![git status output](/static/create-pipeline/git-status.png)
    

## 3. Update the buildspec.yml file

Now you have your environment setup, you can build your automated CI/CD pipeline.

1.  Open the buildpsec.yml from the left pane inside the Cloud9 environment.
    
::alert[buildspec is a collection of build commands and related settings, in YAML format, that AWS CodeBuild uses it to run a build.]
    
![Buildspec.yml before change](/static/create-pipeline/before-build-sh.png)
    
2.  Add the `./build.sh` command to the `buildspec.yml` file as shown on the image below.
    
::alert[Because a build spec declaration must be valid YAML, the spacing in a build spec declaration is important. Make sure you follow the right spacing.]{type='warning'}
    
![Buildspec.yml updates](/static/create-pipeline/buildspec.png)
    
::alert[By adding your your application build commands (build.sh) into buildspec.yml file, Your CI/CD pipeline will use this to re-build your application after changes.]
    
3.  Make sure you save the file after editing it. You can use ctrl + s (Windows) or cmd +s (Mac OS).
    

## 4. Update the Dockerfile

::alert[At this stage, whenever there is a change in your source-code, your application will be re-built automatically and packaged into a deployable `ROOT.war` file.]

Now, you will update the Dockerfile (created by AWS App2Container) to re-deploy your application with the updated build(`ROOT.war`).

1.  Open the Dockerfile from the left pane inside the Cloud9 environment. ![Dockerfile before change](/static/create-pipeline/dockerfile-before.png)
    
2.  You will see the first line inside the Dockerfile `java-tomcat-<app-id>:latest` has been automatically populated by AWS App2Container.
    

::alert[Do not change the first line. Your previously created base image is inside this Amazon ECR repository.]{header='Important'}

3.  Update the Dockerfile' by copying the below command on **line 4**.
    
::code[COPY ["ROOT.war", "/usr/local/tomcat8/webapps/ROOT.war"]]{showLineNumbers=true}
    
Your Dockerfile should look like this:
    
![Dockerfile after change](/static/create-pipeline/dockerfile-after.png)
    
4.  Make sure you save the file after editing it. You can use ctrl + s (Windows) or cmd +s (Mac OS).
    

## 5. Add a change to the application source code.

Now you can introduce a sample change to your application source code.

1.  On the left pane, Open the "app-source-code/src/default.jsp" file, which should look like:

![Source code change](/static/create-pipeline/source-code-change.png)

2.  Update line 30 in "app-source-code/src/default.jsp" from:

::code[<h1 class="cover-heading">Unicorn Movies</h1>]{showLineNumbers=true language=html}

to:

::code[<h1 class="cover-heading">Unicorn Movies after a change</h1>]{showLineNumbers=true language=html}

3.  Make sure you save the file after editing it. You can use ctrl + s (Windows) or cmd +s (Mac OS)

## 6. Push your changes to the CodeCommit repository.

1.  To push you changes to the repository, run the following commands in /app-source-code directory on the Cloud9 IDE:
    
:::code{showLineNumbers=true language=shell}
cd ~/environment/app-source-code 
git add . 
git commit -m "Add an application change"
git push
:::
    
2.  Now, navigate to the **AWS CodeCommit** service and find the app-source-code repository. You can notice that all
    

the committed files that were pushed to your repository. It should look like at the image below:

![Pipeline running](/static/create-pipeline/app-source-code-status.png)

## 7. Observe the changes take place on the containerized application.

1.  Navigate to the **AWS CodePipeline** service to see the

container being build and later deployed to ECS.

![Pipeline running](/static/create-pipeline/pipeline-running-annotations.png)

2.  Press the "Details" button as seen above to view more details related to the build script that you have defined in `buildspec.yml` file.

::alert[You'll see the commands being executed including building the application, building the container image and later pushing it to the ECR repository.]

2.  Now wait for the pipeline to be completed and navigate to your your Load Balancer URL again. If you introduced our recommended change the website should look like this:

![Website after change](/static/create-pipeline/website-after-change.png)

## Congratulations!

You have now a fully automated CI/CD pipeline integrated with your containerized application. You can now focus more on developing your application !

