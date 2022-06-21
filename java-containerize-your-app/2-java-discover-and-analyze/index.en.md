---
title : "Discover And Analyse"
weight : 83
---

Your modernization process starts with discovery of Java Applications running in your system, creating inventory and then analyzing them

![app2container-discover](/static/discover-and-analyze/discover-overview-diagram.png)

## 1. Create Application Inventory

1.  Run the following command to list applications that are running on your server
    
::code[app2container inventory]{showLineNumbers=true language=shell}
    
-   The output is a JSON object collection with a node for each application. Each object will include key/value pairs and will start with the Java-app-id as shown below.
    
:::code{showLineNumbers=true language=json}
"java-app-id": {
    "processId": pid,
    "cmdline": "/user/bin/java ...",
    "applicationType": "java-apptype"
}
:::

    
2.  Your output will be like the output shown below. Notice that "java-tomcat-6c144e52" is a Java application-id, copy your application id to a notepad as you will use it in the next steps.
    
:::code{showLineNumbers=true language=json}
{
    "java-tomcat-21fb1eb0": {
    "processId": 1033,
    "cmdline": "/usr/bin/java ... -Dcatalina.home=/usr/local/tomcat8 -Djava.io.tmpdir=/usr/local/tomcat8/temp org.apache.catalina.startup.Bootstrap start ",
    "applicationType": "java-tomcat",
    "webApp": "ROOT"
    }
}
:::
    

## 2. Analyze your Application

1.  Use the application ID of your application from the JSON output of the inventory command and then run the "app2container analyze --application-id <java-app-id>" command after replacing <java-app-id> with the application ID that you have noted down in the previous step.
    
:::code{showLineNumbers=true language=shell}
app2container analyze --application-id <java-app-id>
:::
    
2.  Output of the "Analyze" command provides information about tasks completed by app2container and recommendations for the next steps.
    
:::code{showLineNumbers=true language=shell}
# app2container analyze --application-id java-tomcat-21fb1eb0
✔ Created artifacts folder /root/app2container/java-tomcat-21fb1eb0
✔ Generated analysis data in /root/app2container/java-tomcat-21fb1eb0/analysis.json
👍 Analysis successful for application java-tomcat-21fb1eb0

💡 Next Steps:
1. View the application analysis file at /root/app2container/java-tomcat-21fb1eb0/analysis.json.
2. Edit the application analysis file as needed.
3. Start the containerization process using this command: app2container containerize --application-id java-tomcat-21fb1eb0
:::
    
3.  Locate the analysis.json file from command output and open the file for review.
    
-   (NOTE: Update your analysis.json file location from the output)
        
    :::code{showLineNumbers=true language=shell}
    cat /root/app2container/<java-app-id>/analysis.json
    :::
        
-   Once you open the file, you will see that analysis.json file has 2 sections: EDITABLE and NON-EDITABLE.
    
    -   EDITABLE section includes the containerParameters section where you can specify your preferred settings like ContainerBaseImage or Imagetag. These parameters will be used during containerization process later. In this exercise Ubuntu:18.04 is used for containerBaseImage. ![java-analysis](/static/discover-and-analyze/java-analysis-1.png)
        
    -   NON-EDITABLE section includes application level analysis information which app2container will use during containerization like OS data, ports in use, dependencies, software libraries etc..
        
        
    

::alert[This analysis is especially important when working with legacy applications or where there is not enough application documentation.]

![java-analysis](/static/discover-and-analyze/java-analysis-2.png)

In this workshop, you will not change any EDITABLE parameters, but in real-life scenarios you can update this section based on your needs. e.g. changing the containerBaseImage, or enabling DynamicLogging.

With this step, you complete the analysis and can continue with containerization of your application in the next section.
