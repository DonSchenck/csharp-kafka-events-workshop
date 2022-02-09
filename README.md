# csharp-kafka-turtorial

This tutorial/workshop will guide you through the creation of an application that demonstrates Apache Kafka and Event Handling using the C# programming language.  

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
Yes, this tutorial is a bit lengthy, with many moving parts. This is a result of my attempt to mimic a real world situation. This is not just a simple "Hello world" example; this tutorial will yield useable information for your own use of Kafka and/or an Event Store.

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
## 2. Creating your managed kafka instance  
## 3. Creating an app to produce events  
## 4. Creating an app to consume events and write to an event store  
## 5. Creating an app that summarizes a day's events and stores the results in a database
## 6. Creating a web app to view and control this system  

