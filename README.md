# HadoopMonitor

Table of Content

1. Overview

        1.1. Introduction
        
        1.2. Advantage
        
        1.3. Scope of monitoring
        
        1.4. Quick Installation
        
2. Tool technical overview

        2.1. Tool Directory layout
        
        2.2. Execution Process
        
        2.3. Configuration and History Management overview
        
        2.4. Toubleshooting steps
        
        2.5. Incident Management Tool Integration Process
        
        2.6. HA Setup overview
        
3. Property file overview and details of property monitored

4. Support and managebility


## 1. Overview

### 1.1. Introduction

This tool is build to monitor a CDH based Hadoop cluster. It is a completely Bash based tool with no additional tool requirements. There is considerable challange in monitoring a Secured (Kerberised) Hadoop Cluster and thus there are limitation to what the tools can monitor. This simple Bash base tool can resolve monitoring challenge and its inbuild support to integrate with your Incident Management framework can help you achive a Enterprise Grade monitoring setup with no and minimum cost.

Note the tool has all available feature of fail tolerance and uses minimum resource and ideal to be placed on your clusters edge node.

### 1.2. Advantage

Below are the feature of the tool
        a. Monitor Secured/Non Secure CDH Cluster.
        b. Monitor CDH Services and Sanity Check separately. Thus both can be separatly schedules as per requirement.
        c. Bare minimum setup required other than already basic configuration available in any CDH Cluster environment.
        d. Does Synthetic monitoring for Hive and simulates User experience of this major CDH Service.
        e. Generate Alert Mail and also has the ability to integrate with your exeisting Incident Management Setup.

### 1.3. Scope of monitoring

At this point of time the tool can be used to monitor any CDH Cluster (Secure and Non Secure Cluster). The tool relies heavily on Cloudera Manager API to monitor the Cluster thus dependency on CDH Clsuter. The tool can monitor a CDH Environment with SSL Secured CM API which is protected with User Authntication.

Since tool is Unix Bash based it is only limited to Unix environment and it has been tested on Linux cluster. Note the tool require CDH Client configuration to be available on the host as few of the parameter needs client utility to be executed locally, it is ideal to be placed on any/many of the edge node of your cluster.

### 1.4. Quick Installation

This probably is the best part of the tool. The installation require minimum effort, but before you install please make sure the below is available in your environment.

        a. Create a SSH keys to identify trusted computers, without involving passwords. The user with which the tool execute should be able to able to login to any Cluster Host without password prompt.
        b. Utility that should be present are quite basic core Linux utility mostly comes with Default Linux build. Eg: curl, netstat.
        c. SMTP Setup is required for the tool to send Alerts for issue detection.
        
The installation require the tools directory to be placed on any location on the host where the tool need to be executed. This is all it takes to install the tool. But please do go though the Tool technical overview section to understand how the tool should be setup for monitoring and finally initiate the actual monitiong.    



## 2. Tool technical overview


### 2.1. Tool Directory layout

Once you have installed the tool the tool home will have the following content 

[DIRECTORY] **tmp** - Temporary work area. Files inside it can be deleted after execution but not during execution.

[DIRECTORY] **log** - Log directory. 

[DIRECTORY] **datastore** - Keytab file location. In case your cluster is secure and you will have to place the tool users Keytab file here, name should be [User Name].keytab

[DIRECTORY] **history** - Contains report file for Incident Management integration. 

[FILE] **check_hadoop.prop** - Main property file. This need to be filled for the tool to be operational.

[FILE] **scheduler** - Main executor script. This is to be executed from the primary monitor node.

[FILE] **scheduler_failover** - Backup executor script. This is to be executed from the failover monitor node. This monitor will be kicked of if the primary node is non responsive.            

[FILE] **passwd_util** - Password encryption utility. Tool to encrypt the password for the monitor user to be placed in property file.

[FILE] **check_sanity** - Sanity check script. 

[FILE] **check_service** - Service check script.     

[FILE] **sanity.mail.body** - Email Templete for Sanity Monitoring.

[FILE] **service.mail.body** - Email Templete for Service Monitoring.



### 2.2. Execution Process

The tool does two basic operation. 


***Sanity Check***

This is basic sanity check which does a basic sanity check of the Cluster. Below are the checks performed. 
        *a. HDFS Sanity Check:*
        HDFS usage percentage. HDFS Corrup Block. Under Replicated Block.

        *b. HDFS Usage Check:*
        HDFS Temp Usage. Service Users Home. Normal User Home usage monitoring.

        *c. Hive Service Synthetic Monitoring:*
        Hive service health status monitoring by response of runnning real query and validating reponse and time for the same.
        Hive Server2 simaltanious connection count.

        *d. YARN Service Monitoring:*
        Yarn Cluster load monitoring by amount of Jobs in ACCEPTED state. Suspected Job Hung (Job that does not progress over a period of time is suspected as hung). Long running (Jobs running for more than a period of time).

        *e. Hue Process Monitoring:*
        The percentage of Physical Memory used by the Hue process.

        *f. Worker Node Monitoring:*
        Cluster worker node health monitoring. Tool relies on CM to detect an unhealthy node.

To setup your environment for Sanity Check you should first setup the configuration file correctly and check your environnment have all the basic requirements meet, once you are done you should just execute the following command to execute the sanity check.

[SCRIPT HOME]/scheduler sanity

***NOTE:*** Its imporatant to understand that just executing the monitor will not allow you to continuously monitor the environment and we should setup some kind of repeated execution mechanism via your Enterprise Scheduler, e.g. ControlM. As a sample setup the below example will help you execute the monitor in a periodically basis in a ***once every 3 hours*** using Unix default scheduler Crontab.

* */3 * * * [SCRIPT HOME]/scheduler sanity >> [SCRIPT HOME]/log/sanity.cron.log 2>> [SCRIPT HOME]/log/sanity.cron.log

***Service Check***

This checks the service health for all services choosen to be monitor. Note the services that you want to be monitored need to be explicitly be specified in the property failing to do so you will have no service being monitored.

To setup your environment for Sanity Check you should first setup the configuration file correctly and check your environnment have all the basic requirements meet, once you are done you should just execute the following command to execute the sanity check.

[SCRIPT HOME]/scheduler service

***NOTE:*** Its imporatant to understand that just executing the monitor will not allow you to continuously monitor the environment and we should setup some kind of repeated execution mechanism via your Enterprise Scheduler, e.g. ControlM. As a sample setup the below example will help you execute the monitor in a periodically basis in a ***once every 5 minute*** using Unix default scheduler Crontab.

*/5 * * * * [SCRIPT HOME]/scheduler service >> [SCRIPT HOME]/log/service.cron.log 2>> [SCRIPT HOME]/log/service.cron.log



### 2.3. Configuration and History Management overview
        


### 2.4. Toubleshooting steps


        
### 2.5. Incident Management Tool Integration Process


        
### 2.6. HA Setup overview




## 3. Property file overview and details of property monitored



## 4. Support and managebility

If you are reading this README file then you are probably about to use the my tools to help you monitor your Hadoop Cluster. Good choice. This tool is made for you. Moreover this tool is free and always will be thats my promise.

Now it is hard to believe that you will get 24/7 Support thats too much to ask for. But in case you face any issue and want my intervention and you cannot debug the hundreeds lines of core Bash Script your self, please do not hassitate to write to me. Its a guarentee you will get an answer but it is not a guarentee you will have it in a SLA.

Reach Me: sanmuk21@gmail.com

Best of luck. Happy Monitoring your Hadoop Cluster.
