---
title: "Lambda with Amazon Data Firehose"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3.  </b> "
---

In this section, we create a Kinesis Data Stream and integrate with Amazon Kinesis Data Firehose delivery stream to
write to a S3 bucket. We also create a Lambda function for transformation the data out the like adding source of the
data in the incoming events and then sends the transformed data to our Firehose Delivery Stream.


![Image](/repo_pmt_ws-fcj-005/images/3/3-001.png?featherlight=false&width=90pc)

Amazon Kinesis Data Firehose is the easiest way to load streaming data into data stores and analytics tools. It can
capture, transform, and load streaming data into Amazon S3, Amazon Redshift, Amazon Elasticsearch Service, and Splunk,
enabling near real-time analytics with existing business intelligence tools and dashboards you’re already using today.
It is a fully managed service that automatically scales to match the throughput of your data and requires no ongoing
administration. It can also batch, compress, and encrypt the data before loading it, minimizing the amount of storage
used at the destination and increasing security.

We configure our Kinesis Firehose Delivery stream to write data to S3 using the parquet file format. Parquet files have
a defined schema, so as part of creating the resources for this lab we will define the schema for our taxi trips dataset
using Glue/Athena (which also makes it easy to query the data once Firehouse delivers it to S3).