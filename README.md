# csharp-kafka-events-workshop

This workshop/tutorial will guide you through the creation of an application that demonstrates Apache Kafka and Event Handling using the C# programming language.  

At the end of this tutorial you will have:  
1. An instance of OpenShift running in Developer Sandbox for Red Hat Openshift. This will be referred to as your "cluster".  
1. A trial instance of Red Hat's managed Kafka offering.  
1. An application that produces events and sends them to your Kafka instance. This application is written in C#.  
1. An instance of a PostgreSQL database running in your OpenShift cluster.  
1. An instance of a MariaDB database running in your OpenShift cluster.  
1. An application that reads from your Kafka instance and writes the events to an event store. This application is written in C#.  
1. An application that reads from your event store, performs a summary for a given date, and writes to your MariaDB database. This application is written in C#.  
1. An application that is a web page to control and view your system. This application is written in JavaScript and JQuery.  

## Note from the author  
Yes, this tutorial is lengthy and involves many moving parts. This is a result of my attempt to mimic a real world situation. This is not just a simple "Hello world" example; this tutorial will yield useable information for your own use of Kafka and/or an Event Store.

## Prerequisites  
The following prerequisites are necessary:  
1. An account in Developer Sandbox for Red Hat OpenShift (No problem; it's free)  
1. An instance of Red Hat OpenShift Stream for Apache Kafka (Again, it's free)  
1. The `oc` command-line tool for OpenShift  
1. The `rhoas` command-line tool for Red Hat service binding  

## High-level overivew 
Here's what you'll be doing:
1. Provisioning your OpenShift sandbox (i.e. your cluster)  
1. Creating your managed kafka instance  
1. Creating an app to produce events  
1. Creating an app to consume events and write to an event store  
1. Creating an app that summarizes a day's events and stores the results in a database
1. Creating a web app to view and control this system  


## 1. Provisioning your OpenShift sandbox (i.e your cluster)  
### 1.1 Get your sandbox
### 1.2 Log in to your sandbox from the command line
Open a terminal session on your local machine and log into your cluster from there. The instructions for doing that are in [this short article](https://developers.redhat.com/blog/2021/04/21/access-your-developer-sandbox-for-red-hat-openshift-from-the-command-line).


## 2. Creating your managed kafka instance  
Go to https://developers.redhat.com/products/red-hat-openshift-streams-for-apache-kafka/getting-started  

Click on "Try our Kafka service at no cost" (red) button. You'll be prompted to log in to your Red Hat Developer account.  

You'll arrive at the Red Hat cloud console and be able to start the process to create a Kafka instance for your own use.  

Click "Create Kafka instance" button.  

Supply a name and select a Cloud region and then click the "Create instance" button.  

You will return to the Red Hat cloud console and will see you instance with a status of "Creation pending". This status will soon change to "Creation in progress".  

After a few minutes the status will change to "Ready". If you don't see this, try refreshing your browser ocassionally until you see this status. At this point you have a Kafka instance for your own use.  

### 2.2 Log in  
Run this command to log into your Red Hat OpenShift Application Services account:  

`rhoas login`

In case you're interested in some of the behind-the-scenes action: This updates the rhoas CLI config file with the access token. The file is located at:

On MacOS: $HOME/Library/Application Support/rhoas/config.json  
On Linux: $XDG_CONFIG_HOME/rhoas/config.json, falls back to $HOME/.config/rhoas/config.json  
On Windows: C:\Users\<username>\AppData\Roaming\rhoas\config.json  

Once you've logged in, you can run the following command and see your Kafka instance listed. This proves that you are "connected" to it from your machine.

`rhoas kafka list`  

Here's an example:  
```console
❯ rhoas kafka list  
  ID                     NAME                      OWNER                      STATUS   CLOUD PROVIDER   REGION
 ---------------------- ------------------------- -------------------------- -------- ---------------- -----------  
  c81tikqn7js5nj3rbm1g   jnyilimb-kafka-instance   jnyilimb@redhat.com        ready    aws              us-east-1  
  c819fgn12sqgnlshpv3g   mpiech-object-detection   mpiech@redhat.com          ready    aws              us-east-1  
  c821l7an7js5nj3rfua0   vax                       rhn-engineering-dschenck   ready    aws              us-east-1  

```


### 2.3 Determine which kafka instance to use  
It's possible to have more than one Kafka instance available to you (in order words, `rhoas kafka list` shows multiple instances), so it's necessary to specify which one you want to use. This is done by using the command `rhoas kafka use`. Here's an example:  

`rhoas kafka use --name vax`  

```console
❯ rhoas kafka use --name vax
 Kafka instance "vax" has been set as the current instance.
```

### 2.4 Create a Kafka topic
Events are written to and retrieved from Kafka by subscribing to a specific "topic" within a specific kafka instance. At this point it is necessary to create a topic within the "vax" instance. Our event producer has the topic "us" hard-coded into it, so we need to create that topic. Use the following command to create the topic:  

`rhoas kafka topic create --name us`

```console
❯ rhoas kafka topic create --name us
Topic "us" created in Kafka instance "vax":
<<several lines removed for brevity>>
```

The command will return a lot of JSON with information about the topic. You can prove the existence further by using the following command:  

`rhoas kafka topic list`

```console
❯ rhoas kafka topic list
  NAME   PARTITIONS   RETENTION TIME (MS)   RETENTION SIZE (BYTES)
 ------ ------------ --------------------- ------------------------
  us              1   604800000             -1 (Unlimited)
```

### 2.5 Get the token needed to connect to your cluster
You will need an authentication token in order for your Kafka isntance to connect to your OpenShift cluster. Get that token by visiting the following web page:

https://console.redhat.com/openshift/token

![get token screen](./images/GetTokenScreen.png "Get token screen")

Click on the blue "Load token" button to review a token which can be copied to your machine's clipboard:

![token](./images/token.png)

Click the small "copy" icon on the right side of the API token to copy the token to your machine's clipboard.

With the token in your machine's clipboard, run the following command:

`rhoas cluster connect --token {{insert_your_token_here}}`

When prompted, choose "kafka" as the type of service.

Here's an example: 

```console
❯ rhoas cluster connect --token eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJhZDUyMjdhMy1iY2ZkLTRjZjAtYTdiNi0zOTk4MzVhMDg1NjYifQ.eyJpYXQiOjE2NDQ1MTE4NTYsImp0aSI6IjgwN2YwMTI3LWQ2NGMtNDMxYi1iZjA4LTQzMjQ1NmVmNmVmZiIsImlzcyI6Imh0dHBzOi8vc3NvLnJlZGhhdC5jb20vYXV0aC9yZWFsbXMvcmVkaGF0LWV4dGVybmFsIiwiYXVkIjoiaHR0cHM6Ly9zc28ucmVkaGF0LmNvbS9hdXRoL3JlYWxtcy9yZWRoYXQtZXh0ZXJuYWwiLCJzdWIiOiJmOjUyOGQ3NmZmLWY3MDgtNDNlZC04foofoofoofoofoofooNjpyaG4tZW5naW5lZXJpbmctZHNjaGVuY2siLCJ0eXAiOiJPZmZsaW5lIiwiYXpwIjoiY2xvdWQtc2VydmljZXMiLCJub25jZSI6IjdmZmE5MzUyLWVjZmQtNDcyZS1hOGRjLTQ3YThlNmQyYjFkNSIsInNlc3Npb25fc3RhdGUiOiJiZWRiZTRkZC0yMThiLTQ1NGEtOWQ5My1iNjlmMmRhMjkwMmYiLCJzY29wZSI6Im9wZW5pZCBvZmZsaW5lX2FjY2VzcyIsInNpZCI6ImJlZGJlNGRkLTIxOGItNDU0YS05ZDkzLWI2OWYyZGEyOTAyZiJ9._xRhrsMxuqt4PGoRmiDlEOCxEHpyaLRliP2UYQ_kk8k
? Select type of service  [Use arrows to move, type to filter]
> kafka
  service-registry
```

```console
This command will link your cluster with Cloud Services by creating custom resources and secrets.
In case of problems please execute "rhoas cluster status" to check if your cluster is properly configured

Connection Details:

Service Type:                   kafka
Service Name:                   vax
Kubernetes Namespace:           pipelines-tutorial
Service Account Secret:         rh-cloud-services-service-account
```

Like some of the previous commands, this command will update your config.json file associated with Service Binding. This is user later when you bind your Kafka instance to your service running in OpenShift.

At this point we're ready to "bind" our Kafka instance to our application. More specifically, the Service Binding tool will look for any DeploymentConfig objects in your current OpenShift project and will let you select the one with which you wish to bind your Kafka instance.

For this tutorial, we haven't created that DeploymentConfig yet. That's the next step.

## 3. Creating an app to produce events  
The first step in this entire suite of applications is to have a service in OpenShift that produces events into the proper topic in our Kafka instance.

### 3.1 View the source code — this is optional
If you wish to see the code that will be implemented, check out [this Github repository](https://github.com/donschenck/vac-seen-generator).

### 3.2 Hard-coded values
Several values are hard-coded inside the application. *Part* of the reason for this is to mimic the real-world implementation of a microservice.

If this were the actual vaccination-tracking system, you would expect very country to create their own reporting system. Therefore, hard-coding the country code, vaccination types, and other specific information makes total sense.

### 3.3 What we're doing
This application will create one to 40 vaccination events per day for the past 30 days.  

The type of shot (e.g. Pfizer or Moderna), the shot number (1st vaccination, 2nd, booster) and number of events for that day are all randomly generated.

### 3.4 How the Service Binding works
When this application starts, it has no knowledge of the Kafka instance to which it will produce events. In order to write to Kafka, this application needs to know the following:  
* URI of the bootstrap server
* Topic — hard-coded as "us"
* Security Protocol
* SaslMechanicm
* SaslUsername
* SaslPassword

Other than the topic, the information is supplied by the Service Binding mechanism. Here is how that mechanism works:

When you run the command `rhoas service bind`, the rhoas CLI will gather the needed information from your active Kafka cluster. It will then use the token in your config.json file to connect to your cluster and retrieve a list of DeploymentConfigs in your current project.

You are presented with that list, and you select a DeploymentConfig. The tool will then create (or add to, if already existing) a directory in the root directory of that DeploymentConfig's container, called "bindings". Inside that "bindings" directory, a directory is created for your Kafka instance, and files are added containing the needed information, i.e. URI, Security Protocol, etc.

When this happens, the existing pod in the container is destroyed and replaced with a new pod. This new pod has visibility to the new "bindings" directory (and sub-directories). The application can then read from these files, get the information needed, and begin sending events to Kafka.

### 3.5 Creating the app in OpenShift

## 4. Creating an app to consume events and write to an event store  
## 5. Creating an app that summarizes a day's events and stores the results in a database
## 6. Creating a web app to view and control this system  

