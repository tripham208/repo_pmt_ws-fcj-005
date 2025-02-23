---
title: "Consuming with Amazon KCL"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: " <b> 6.  </b> "
---

After data has been stored in Amazon Kinesis, you can consume and process data with
the [Kinesis Client Library](https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html) for
data
analysis, archival, real-time dashboards, and much more. While you can use Amazon Kinesis API functions to process
stream data directly, the KCL takes care of many complex tasks associated with distributed processing and allows you to
focus on the record processing logic. For example, the KCL can automatically load balance record processing across many
instances, allow the user to checkpoint records that are already processed, and handle instance failures. The KCL acts
as an intermediary between your record processing logic and Kinesis Data Streams. The KCL performs the following tasks:

* Connects to the data stream
* Enumerates the shards within the data stream
* Uses leases to coordinate shard associations with its workers
* Instantiates a record processor for every shard it manages
* Pulls data records from the data stream
* Pushes the records to the corresponding record processor
* Checkpoints processed records
* Balances shard-worker associations (leases) when the worker instance count changes or when the data stream is
* resharded (shards are split or merged)

It is recommended to use version 2 of KCL. You can
use [KCL 2.x](https://docs.aws.amazon.com/streams/latest/dev/developing-consumers-with-kcl-v2.html) to build your custom
consumer applications. It supports
non-EFO consumers and makes the switching easier. If you have customers making use of KCL 1.x, they can follow the
instructions on how to migrate from KCL 1.x to KCL 2.x, see Migrating Consumers from KCL 1.x to KCL 2.x

## KCL Concepts

* **KCL consumer application** – an application that is custom-built using KCL and designed to read and process records
  from
  data streams. Consumer application instance - KCL consumer applications are typically distributed, with one or more
  application instances running simultaneously in order to coordinate on failures and dynamically load balance data
  record processing.
* **Worker** – a high level class that a KCL consumer application instance uses to start processing data.
* **Lease** – data that defines the binding between a worker and a shard. Distributed KCL consumer applications use
  leases
  to partition data record processing across a fleet of workers. At any given time, each shard of data records is bound
  to a particular worker by a lease identified by the leaseKey variable. By default, a worker can hold one or more
  leases (subject to the value of the maxLeasesForWorker variable) at the same time.
* **Lease table** - a unique Amazon DynamoDB table that is used to keep track of the shards in a KDS data stream that
  are
  being leased and processed by the workers of the KCL consumer application. The lease table must remain in sync (within
  a worker and across all workers) with the latest shard information from the data stream while the KCL consumer
  application is running. For more information, see [Using a Lease Table to Track the Shards Processed by the KCL
  Consumer Application](https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html#shared-throughput-kcl-consumers-leasetable) .
* **Record processor** – the logic that defines how your KCL consumer application processes the data that it gets from
  the
  data streams. At runtime, a KCL consumer application instance instantiates a worker, and this worker instantiates one
  record processor for every shard to which it holds a lease.

## Stream processing using KCL application

This lab shows you how to use custom consumers—specifically, enhanced fan-out consumers—using the KCL. [Enhanced fan-out
consumers](https://docs.aws.amazon.com/streams/latest/dev/enhanced-consumers.html) have a dedicated throughput of 2 MB/s and use a push model instead of pull to get data. Records are pushed to
the consumer from the Kinesis Data Streams shards using HTTP/2 Server Push, which also reduces the latency for record
processing. If you have more than one instance of a consumer, each instance has a 2 MB/s fan-out pipe to each shard
independent from any other consumers. You can use enhanced fan-out consumers with the AWS SDK or the KCL.

The KCL is a Java library but also supports other languages via a MultiLangDaemon. The MultiLangDaemon uses STDIN and
STDOUT to communicate with the record processor, which is implemented in the desired language, while the rest of the KCL
application is Java. In this Lab we use the Java Version but you can find a Python sample on GitHub . Starting with
Version 2.3 KCL supports processing multiple streams in the same application.

The KCL creates an Amazon [DynamoDB](https://aws.amazon.com/vi/dynamodb/) table to keep track of consumer progress. For example, if your stream has four shards
and you have one producer instance, your instance runs a separate record processor for each shard. If the consumer
scales to two instances, the KCL rebalances the record processor and runs two record processors on each instance. For
more information, see Using the [Kinesis Client Library](https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html#shared-throughput-kcl-consumers-leasetable) .

### Preparation
If you haven't already done so, download the Kinesis Client Library examples found [here](/repo_pmt_ws-fcj-005/resources/6/kinesis-immersionday-kcl-main.zip) and unzip them on your local
machine.

Once unzipped, direct your attention back to the Cloud9 instance we were working with previously.

* On the Cloud9 IDE, in the top left, click File -> Upload Local Files... -> Select Folder -> Locate the unzipped root of the examples.
* The folder root should be called kinesis-immersionday-kcl-main.
* Select Upload. You will then be able to see the project in the left hand side of your Cloud9 IDE.

![Image](/repo_pmt_ws-fcj-005/images/6/6-002.png?featherlight=false&width=90pc)

### Running the KCL

Once the code has successfully been uploaded, let’s open up the following directory: kcl-app

Here you will find the source code for your KCL application. To inspect the code open the file
`src/main/java/com/amazon/kinesis/immersionday/App.java.`

In line 138 you will find the processRecords method which implements the logic for processing records.


![Image](/repo_pmt_ws-fcj-005/images/6/6-003.png?featherlight=false&width=90pc)

In our sample we simply log the partition key and data for each record to STD OUT.

To build the application navigate to the kcl-app directory and run the following commands:

```
sudo yum install maven -y
mvn clean compile assembly\:single
```

![Image](/repo_pmt_ws-fcj-005/images/6/6-004.png?featherlight=false&width=90pc)
After the build has finished you need to set the configuration parameters for KCL. In our sample we are using
environment variables for this:
```
export STREAM_NAME=<your stream name>
export AWS_REGION=us-east-1
export APPLICATION_NAME=ImmersiondayKCLConsumer
```

Adjust these export parameters as needed
Now you can run your application by executing the previously build JAR file:

`java -jar target/kcl-app-1.0-SNAPSHOT-jar-with-dependencies.jar`

![Image](/repo_pmt_ws-fcj-005/images/6/6-005.png?featherlight=false&width=90pc)

You can see in the logs that the KCL created a LeaseTable with a read and write capacity with 10. After this, KCL
created leases and the Enhanced Fan-Out Consumer with the previously defined name. You see also a log Sleeping ... as
there are currently no records processed.

After you restart data ingestion (described in [Lab 1](2-Produce-data-to-Kinesis-Data-Streams/)), you will see the logged records:

![Image](/repo_pmt_ws-fcj-005/images/6/6-006.png?featherlight=false&width=90pc)
### KCL Output

Switching to the [DynamoDB Console](https://us-west-2.console.aws.amazon.com/dynamodbv2/home?region=us-west-2#tables) you will see a table named ImmersiondayKCLConsumer which is the lease table for the
KCL application.

![Image](/repo_pmt_ws-fcj-005/images/6/6-007.png?featherlight=false&width=90pc)

When you are running KCL, it will by default publish metrics to [Amazon Cloudwatch](https://us-west-2.signin.aws.amazon.com/oauth?client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fcloudwatch&code_challenge=5j7GJeOepk0Wai04gTKLJq7wtDwBmj3yFS3kaZngz8Y&code_challenge_method=SHA-256&redirect_uri=https%3A%2F%2Fus-west-2.console.aws.amazon.com%2Fcloudwatch%2Fhome%3Fregion%3Dus-west-2%26state%3DhashArgs%2523metricsV2%253Agraph%253D~%2528%2529%26ca-oauth-flow-id%3DHqZt%26isauthcode%3Dtrue&response_type=code). Navigate To Cloudwatch Metrics and
select the ImmersiondayKCLConsumer namespace. You will see KCL-Application, Worker and Shard metrics. For more
information on monitoring KCL refer to the documentation .


If you are seeing the error "The security token included in the request is expired" stop the application by pressing
CTRL+C and start it again.