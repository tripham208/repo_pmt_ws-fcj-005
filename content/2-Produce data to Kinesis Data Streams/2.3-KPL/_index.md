---
title: "Using the Kinesis Producer Library"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 2.3 </b> "
---

## The Kinesis Producer Library (KPL)

The Kinesis Producer Library enhances the data ingestion capabilities covered in the AWS SDK Kinesis API section of this
workshop, allowing for developers to achieve higher and more optimal write throughput with retries and error handling,
utilizing shards within the data stream to their maximum capacity per second.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-001.png?featherlight=false&width=90pc)

## What can the Kinesis Producer Library Do?

Write to one or more Kinesis Data Streams with automatic and configurable retries.
Collects records and utilizes PutRecords API to write multiple records to multiple shards per request.
Aggregate records to increase payload size and throughput.*
Integrate with the Kinesis Client Library (KCL) to de-aggregate batch records on the consuming side.
Integrates with Amazon CloudWatch metrics to provide visibility into the KPL Performance.
The KPL provides a layer of abstraction over the AWS SDKs for Kinesis.

## Shard Utilization

An area of emphasis to note is the Kinesis Producer Library's capability to aggregate records to increase payload size
and throughput for each individual shard that it is producing to.

For a given Kinesis Data Stream, an application can write 1MiB of data per second to each shard. In most cases, an
individual record will be significantly smaller than this 1MiB limit, on the order of 10s of kilobytes. The Kinesis
Producer Library will buffer these records to optimize throughput and wait a configured amount of time or amount of data
until the throughput is as close to 1MiB as possible.

{{% notice note %}}
It is not recommended to use the Kinesis Producer Library if your application has low latency requirements. This is
because the KPL can incur additional processing delay configurable by the RecordMaxBufferedTime
{{% /notice %}}

# A Simple Producer

If you haven't already done so, download the Kinesis Producer Library examples
found [here](/repo_pmt_ws-fcj-005/resources/kinesis-producer-library-examples-master.zip)  and unzip them on your local
machine.
Once unzipped, direct your attention back to the Cloud9 instance we were working with previously.

* On the Cloud9 IDE, in the top left, click File -> Upload Local Files... -> Select Folder -> Locate the unzipped root
  of the examples.
* The folder root should be called `kinesis-producer-library-examples-master`.
* Select Upload. You will then be able to see the project in the left hand side of your Cloud9 IDE.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-002.png?featherlight=false&width=90pc)

We will use this codebase to learn more about the Kinesis Producer Library.
Once the code has successfully been uploaded, let's open up the following directory: `src/main/java/`

Here, you will find files A, B, C and D, highlighting different ways we can use the Kinesis Producer Library. Open up A,
or A_SimpleProducer.java.

Within this file, you will find a bare-bones Kinesis Producer Library publisher that writes Taxi Trips to our Kinesis
Data Stream. Compared to the Amazon SDK version of this code, the Kinesis Producer Library greatly simplifies the retry,
batching and shard optimization of writes to a Kinesis Data Stream.

`A_Simple_Producer.java`
Towards the top of the file, modify both the streamName and the region variables to match your stream name and region.

At the beginning of the main function, we are reading in a csv of taxi trip data from the data/ directory. This data
will be published to Kinesis Data Streams after we leverage the Kinesis Producer Library to aggregate records and
produce efficiently.

Let's take a look at the Kinesis Producer Configuration--

```javascript
KinesisProducerConfiguration
config = new KinesisProducerConfiguration()
    .setRecordMaxBufferedTime(3000)
    .setMaxConnections(1)
    .setRequestTimeout(60000)
    .setRegion(region);
```

In this code sample, we are setting a few configuration properties. Expand each to learn more:

{{%expand "RecordMaxBufferedTime" %}}
RecordMaxBufferedTime: RecordMaxBufferedTime controls the amount of time (milliseconds) a record may spend being
buffered before it gets produced to one or more Kinesis Data Streams. Records could be produced sooner than this time
depending on other settings we will cover. Buffered time can also include retries and failures to produce to the stream.

Setting RecordMaxBufferedTime too low can negatively impact throughput.
The default value for RecordMaxBufferedTime is 100 milliseconds.
{{% /expand%}}

{{%expand "MaxConnections" %}}
MaxConnections: The maximum amount of connections to open to the backend. HTTP requests to Kinesis Data Streams will be
sent in parallel over multiple connections.

Setting this value too high may impact latency and consume additional resources in the producing application without
increasing throughput.
The default value for MaxConnections is 24.
{{% /expand%}}

{{%expand "RequestTimeout" %}}
RequestTimeout: The maximum total time (milliseconds) between when an HTTP request is sent and receiving all responses
from sent messages. If the request waiting time goes over this value, the request will be considered timed out.

A timed-out record may still have been successfully published to Kinesis Data Streams, and retrying can lead to
duplicates. Therefore, by setting this value too low, a data stream increases the likelihood of duplicates.
The default value for RequestTimeout is 6000 milliseconds.
{{% /expand%}}

### Publishing records to your Kinesis Data Stream

Once we have set the configuration and assigned it to the Kinesis Producer object:

`final KinesisProducer kinesis = new KinesisProducer(config);`

we can begin publishing data. Call the .addUserRecord() function with a blob of data. A User Record is distinguished
from a Kinesis Data Stream record--

The term record refers a Kinesis Producer Library record, or a blob of data that has meaning to a user. A clickstream
event, a financial transaction, etc.

A Kinesis Data Stream record is an instance of the a Record data structure, defined by the Kinesis Data Streams API,
containing a partition key, sequence number and a blob of data.

### Run the program

In your Cloud9 instance, navigate to Window -> New Terminal to open a new terminal window.

Ensure you are within the root folder of the project before executing this command: on the same directory level as the
pom.xml file.

`cd kinesis-producer-library-examples-master/`

We will now execute the program by typing the following in the terminal window:

### Maven Build command:

`mvn clean compile package`

This will compile and package a jar containing all code within the project we are working with. The jar will be
generated and placed in the `target/` folder of your project workspace.

Ensure you are within the root folder of the project before executing this command: on the same directory level as the
`pom.xml` file.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-003.png?featherlight=false&width=90pc)

### Execute the program

`java -cp target/amazon-kinesis-replay-1.0-SNAPSHOT.jar A_SimpleProducer`

This should be executed in the same directory level as the pom.xml file.
Once you see the output stating that it has begun producing, we will wait approximately 5 minutes for CloudWatch to
refresh.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-004.png?featherlight=false&width=90pc)
After 5 minutes...

Navigate to the AWS Management Console and open up your Kinesis Data Stream's Monitoring tab. Take note of what you see
in the Incoming data and Put Records metrics areas.

If you are seeing records incoming in both of these monitoring panes, congratulations! You have successfully published
data into the Kinesis Data Stream using the Kinesis Producer library--an error handling resilient producer that will
simplify your production process.

You may need to hit REFRESH on the page in a few minutes to see metrics appear.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-005.png?featherlight=false&width=90pc)

{{% notice warning %}}
Stop the production of data in this window by typing the following in your terminal:
CTRL + C
{{% /notice %}}

You may want to keep the data flowing if you plan on completing any of the next labs. Just remember to stop the
production when you are done!