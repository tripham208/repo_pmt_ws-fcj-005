---
title: "Introduction"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

## What is Kinesis Data Streams?

Amazon Kinesis Data Streams (KDS) is a massively scalable and durable real-time data streaming service. KDS can
continuously capture gigabytes of data per second from hundreds of thousands of sources such as website click streams,
database event streams, financial transactions, social media feeds, IT logs, and location-tracking events. The data
collected is available in milliseconds to enable real-time analytics use cases such as real-time dashboards, real-time
anomaly detection, dynamic pricing, and more.

## Terminology

You can use Amazon Kinesis Data Streams to ingest large amounts of data in real-time, to durably store the data, and
make the data available for consumption. The unit of data stored by Kinesis Data Streams is a data record. A data stream
represents a group of data records. The data records in a data stream are distributed into shards.

A shard has a sequence of data records in a stream. When you create a stream, you specify the number of shards for the
stream. The total capacity of a stream is the sum of the capacities of its shards. You can increase or decrease the
number of shards in a stream as needed. However, you are charged on a per-shard basis. For information about the
capacities and limits of a shard, see Kinesis Data Streams Limits.

A producer puts data records into shards and a consumer gets data records from shards.

## Kinesis Data Streams High-Level Architecture

The following diagram illustrates the high-level architecture of Kinesis Data Streams. Producers put records into the
data stream, and the consumers process the data in real time. Consumers (such as a custom application running on Amazon
EC2 or an Amazon Kinesis Data Firehose delivery stream) can store their results using an AWS service such as Amazon
DynamoDB, Amazon Redshift, or Amazon S3.

![Image](/repo_pmt_ws-fcj-005/images/1/1-001.png?featherlight=false&width=90pc)

Kinesis high-level architecture

## Sending Data to Amazon Kinesis Data Streams

There are several mechanisms to send your data to the stream. You can build producers for Kinesis Data Streams using the
AWS SDK Data Streams API, Kinesis Producer Library (KPL), and Kinesis Agent.

### Amazon Kinesis Data Streams API

Amazon Kinesis Data Streams provides two APIs for putting data into an Amazon Kinesis Stream: PutRecord and PutRecords.
PutRecord allows a single data record within an API call and PutRecords allow multiple records within API call.

### Amazon Kinesis Producer Library (KPL)

Amazon Kinesis Producer Library (KPL) is an easy to use and highly configurable library that helps you put data into an
Amazon Kinesis data stream. Amazon Kinesis Producer Library (KPL) presents a simple, asynchronous, and reliable
interface that enables you to quickly achieve high producer throughput with minimal client resources.

### Amazon Kinesis Agent

Amazon Kinesis Agent is a pre-built Java application that offers an easy way to collect and send data to your Amazon
Kinesis stream. You can install the agent on Linux-based server environments such as web servers, log servers, and
database servers. The agent monitors certain files and continuously sends data to your stream.

## Processing Data from Amazon Kinesis Data Streams

You can quickly build applications using the AWS SDK, the Kinesis Client Library (KCL), connectors, and agents. You can
also easily process data with built-in integrations to AWS Lambda, Amazon Kinesis Data Analytics, Amazon Kinesis Data
Firehose and AWS Glue Schema Registry.

### Amazon Data Firehose

Amazon Data Firehose is the easiest way to reliably transform and load streaming data into data stores and analytics
tools. You can use a Kinesis data stream as a source for a Kinesis data firehose.

### Amazon Managed Service for Apache Flink

Amazon Managed Service for Apache Flink is the easiest way to transform and analyze streaming data in real time with
Apache Flink. Apache Flink is an open-source framework and engine for processing data streams. You can use Kinesis Data
Analytics to easily transform and analyze streaming data in real time.

Amazon Managed Service for Apache Flink integrates with Amazon MSK, Amazon Kinesis Data Streams, Amazon OpenSearch
Service, Amazon DynamoDB streams, Amazon Simple Storage Service (Amazon S3), custom integrations, and more using
built-in connectors. You can use a Kinesis data stream as a source and a destination for a Kinesis data analytics
application.

In addition to this, you can use Amazon Managed Service for Apache Flink Studio to interact with streaming data using
SQL, Python and Scala.

### AWS Lambda

You can subscribe Lambda functions to automatically read records off your Kinesis data stream. AWS Lambda is typically
used for record-by-record (also known as event-based) stream processing.

### Amazon Kinesis Client Library (KCL)

Amazon Kinesis Client Library (KCL) is a pre-built library that helps you easily build Amazon Kinesis applications for
reading and processing data from an Amazon Kinesis data stream. KCL handles complex issues such as adapting to changes
in stream volume, load-balancing streaming data, coordinating distributed services, and processing data with
fault-tolerance. KCL enables you to focus on business logic while building Amazon Kinesis applications. Starting with
KCL 2.0, you can utilize a low latency HTTP/2 streaming API and enhanced fan-out to retrieve data from a stream.

The current version of this library provides connectors to Amazon DynamoDB, Amazon Redshift, Amazon S3, and Amazon
OpenSearch Service. The library also includes sample connectors of each type, plus Apache Ant build files for running
the samples.

