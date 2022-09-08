# csharp-kafka-events-workshop

## What is this?
This activity encompasses what is called the "Vac-Seen System", a small system that allows the recording and tracking of COVID-19 vaccinations.

This is entirely fictional but does demonstrate the following technologies and concepts:
* Microservices
* Apache Kafka
* Command Query Responsibility Segregation, or CQRS
* Blazor, running in Kubernetes

This system is associated with, and specifically created for, the [Red Hat OpenShift Sandbox](https://developers.redhat.com/developer-sandbox).

At the end of this tutorial you will have an instance of a small system that has the following partss:

* A "vac-seen-generator" REST API microservice that accepts a date and produces Kafka events that are stored in the Kafka instance "vaccinations".  
* A "vac-seen-todb" Kubernetes job that watches the Kafka instance and picks up any new events. Those events are then written to persistence in a [Martin Event Store](https://martendb.io/).  
* A "vac-seen-rollup" REST API microservice that accepts a date and creates a summary for that date. It does this by reading all of the vaccination events in the Marten Event Store for the given date and calculating a total number of vaccinations for that date. That date and count are then written to a MariaDB database table "vaccination_summaries".  
* A "vac-seen-getter" REST API microservice that accepts a date and a number of days (e.g. "9/2/2022" and 30) and, beginning at that date, reads every summary row from the "vaccinations_summaries" table, backwards, the number of days. The retrieved database rows are used to create a JSON array, which is returned to the caller.
* A web front end that drives the processes. From the front end the user can generate random events, perform a rollup operation for a date, and retrieve the records to be displayed in a bar chart.

You can see this in action by viewing this short video (less than 90 seconds in length).


## Need help?
If you need help or get stuck, email devsandbox@redhat.com.
If you find a defect, [create an Issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue) in this repository.

## Prerequisites
The following **three** prerequisites are necessary:
1. An account in [OpenShift Sandbox](https://developers.redhat.com/developer-sandbox) (No problem; it's free). This is not actually *necessary*, since you can use this tutorial with any OpenShift cluster.
1. The `oc` command-line tool for OpenShift. There are instructions later in this article for the installation of `oc`.
1. Your machine will need access to a command line; Bash or PowerShell, either is fine.

## All Operating Systems Welcome
You can use this activity regardless of whether your PC runs Windows, Linux, or macOS.
This workshop/activity/tutorial will guide you through the creation of an application that demonstrates Apache Kafka and Event Handling using the C# programming language.   

By following the instructions of this tutorial, you will:
1. Deploy a simple website to your cluster that allows you to control and view your work.
1. Deploy a MariaDB database to your cluster.
1. Deploy a PostgreSQL database to your cluster.
1. Deploy a microservice that produced events and sends them to your Kafka instance.
1. Create an instance of Red Hat's Managed Kafka offering.
1. Deploy a Kubernetes Job that reads events from your Kafka instance and stores the events in an event store.
1. Deploy a microservice that reads from your event store, performs a summary for a given date, and writes that daily summary to your MariaDB database.
1. Deploy a microservice thet reads from the summary database and returns a JSON array.

## Note from the author  
Yes, this tutorial is lengthy and involves many moving parts: Three microservices, two databases, a Kubernetes Job, a Kafka instance, and a website. This is a result of an attempt to mimic a real world situation. This is not just a simple "Hello world" example; this tutorial will yield useable information for your own use of Kafka and/or an Event Store.

## Overview
This tutorial/activity is implemented by following these eight parts, in order:

1. vac-seen-web: https://github.com/donschenck/vac-seen-web
2. vac-seen-db: https://github.com/donschenck/vac-seen-db
3. vac-seen-event-store: https://github.com/donschenck/vac-seen-event-store
4. vac-seen-generator: https://github.com/donschenck/vac-seen-generator
5. vac-seen-managed-kafka: https://github.com/donschenck/vac-seen-managed-kafka
6. vac-seen-todb: https://github.com/donschenck/vac-seen-todb
7. vac-seen-rollup: https://github.com/donschenck/vac-seen-rollup*
8. vac-seen-getter: https://github.com/donschenck/vac-seen-getter

