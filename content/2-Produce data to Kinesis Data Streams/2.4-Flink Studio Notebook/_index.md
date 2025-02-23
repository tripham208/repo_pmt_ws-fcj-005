---
title: "Write Data to a Kinesis Data Stream using Amazon Managed Service for Apache Flink Studio Notebook"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 2.4 </b> "
---

## Overview

In this section, we will dive into the concepts of
the [Studio Notebooks for Managed Service for Apache Flink](https://docs.aws.amazon.com/managed-flink/latest/java/how-notebook.html) .

Studio notebooks for Managed Service for Apache Flink allows you to interactively query data streams in real time, and
easily build and run stream processing applications using standard SQL, Python, and Scala. With a few clicks in the AWS
Management console, you can launch a serverless notebook to query data streams and get results in seconds.

A notebook is a web-based development environment. With notebooks, you get a simple interactive development experience
combined with the advanced capabilities provided by Apache Flink. Studio notebooks uses notebooks powered by Apache
Zeppelin, and uses Apache Flink as the stream processing engine. Studio notebooks seamlessly combines these technologies
to make advanced analytics on data streams accessible to developers of all skill sets.

Apache Zeppelin provides your Studio notebooks with a complete suite of analytics tools, including the following:

* Data Visualization

* Exporting data to files

* Controlling the output format for easier analysis

With a notebook, you model queries using the Apache Flink Table API & SQL in SQL, Python, or Scala, or DataStream API in
Scala. With a few clicks, you can then promote the Studio notebook to a continuously-running, non-interactive, Managed
Service for Apache Flink stream-processing application for your production workloads.

In this lab, we will use Zeppelin Notebook to read Taxi Ride data from S3 and insert into Kinesis Stream.

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-001.png?featherlight=false&width=90pc)

In our Notebook, we have different paragraphs (code) to perform following:

1. Create an in-memory table for incoming data from S3 bucket.
2. Query data from S3.
3. Create an in-memory table for target kinesis stream.
4. Start data insertion from S3 into Kinesis Stream using in-memory tables.
5. Query Kinesis stream target table to check data is inserted correctly.

## Preparation

If you haven't run exercise 1 yet, or you cleaned it up, go and create a Kinesis Data Stream and run the CloudFormation
template that creates the resources necessary for this lab.

Upload data file into S3 bucket.

a. To get started, let's download the [Taxi Ride Data file](/repo_pmt_ws-fcj-005/resources/taxi-trips.csv) which we'll
use to load into Kinesis Stream.

b. Upload the previously downloaded data file to the S3 bucket which was created through CloudFormation script executed
in the getting started. You can find this bucket in the Outputs tab of CloudFormation called `TaxiTripDataSet`.

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-002.png?featherlight=false&width=90pc)

## Run Studio Notebook

From the Kinesis Console, Analytics Applications - Studio tab, make sure notebook is running.

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-003.png?featherlight=false&width=90pc)

Let's download the Zeppelin [Notebook](/repo_pmt_ws-fcj-005/resources/s3_to_kinesis.ipynb)  file which we'll use to read
and load data into Kinesis Stream.

Import the Zeppelin Notebook previously downloaded

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-004.png?featherlight=false&width=90pc)

Execute Notebook

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-005.png?featherlight=false&width=90pc)

Run each paragraph step by step to load data from S3 to Kinesis Stream

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-006.png?featherlight=false&width=90pc)

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-007.png?featherlight=false&width=90pc)
