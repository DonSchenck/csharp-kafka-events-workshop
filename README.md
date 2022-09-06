# csharp-kafka-events-workshop

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

