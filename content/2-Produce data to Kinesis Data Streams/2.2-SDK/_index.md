---
title: "Using the Amazon SDK with Kinesis"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2.2 </b> "
---

In this section, we will dive deep into the concepts of
the [Kinesis Data Streams API on the Amazon SDK](https://docs.aws.amazon.com/kinesis/latest/APIReference/Welcome.html) .

In this section we'll complete the following steps:

1. Create a Kinesis Data Stream (if one does not already exist).
2. Learn about the concepts of writing to a Kinesis Data Stream using the Kinesis Data Streams API.
3. Write Data to a Kinesis Data Stream.
4. Optimize our code for low latency writes to the Kinesis Data Stream.
5. Ensure data is published in order per shard

## Ingesting records into a Kinesis Data Stream:

Once a stream is created, you can add data to the stream in the form of records. A record is a data structure that
contains the data to be processed in the form of a data blob.

You can add data to a stream using PutRecords
and [PutRecord](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecord.html) APIs. The PutRecords
operation sends multiple records to your stream per HTTP request, and the singular PutRecord operation sends records to
your stream one at a time. A separate HTTP request is required for each record.

![Image](/repo_pmt_ws-fcj-005/images/2/2/22-001.png?featherlight=false&width=90pc)

## Adding a Single Record with PutRecord

This section provides examples of using PutRecord API to ingest data to a Kinesis Data Stream.

1. Create a new python script on your Cloud9 Instance
    * File -> New File
2. Enter the below code and replace the **kdsname** if you have given a different name on the previous step (Create a
   Kinesis Data Stream). This code will generate random taxi trip data with some location information and continuously
   ingest data to a Kinesis Data Stream.

   ```python
   import datetime
   import json
   import random
   from datetime import timedelta
   
   import boto3
   
   kdsname = 'input-stream'
   region = 'us-east-1'
   i = 0
   clientkinesis = boto3.client('kinesis', region_name=region)
   
   
   def getlatlon():
      a = ["-73.98174286,40.71915817", "-73.98508453, 40.74716568", "-73.97333527,40.76407242",
           "-73.99310303,40.75263214",
           "-73.98229218,40.75133133", "-73.96527863,40.80104065", "-73.97010803,40.75979996", "-73.99373627,40.74176025",
           "-73.98544312,	40.73571014",
           "-73.97686005,40.68337631", "-73.9697876,40.75758362", "-73.99397278,40.74086761", "-74.00531769,40.72866058",
           "-73.99013519, 40.74885178",
           "-73.9595108, 40.76280975", "-73.99025726,	40.73703384", "-73.99495697,40.745121",
           "-73.93579865,40.70730972", "-73.99046326,40.75100708",
           "-73.9536438,40.77526093", "-73.98226166,40.75159073", "-73.98831177,40.72318649", "-73.97222137,40.67683029",
           "-73.98626709,40.73276901",
           "-73.97852325,	40.78910065", "-73.97612, 40.74908066", "-73.98240662,	40.73148727",
           "-73.98776245,40.75037384", "-73.97187042,40.75840378",
           "-73.87303925,	40.77410507", "-73.9921875,	40.73451996", "-73.98435974,40.74898529",
           "-73.98092651,40.74196243", "-74.00701904,40.72573853",
           "-74.00798798,	40.74022675", "-73.99419403,40.74555969", "-73.97737885,40.75883865",
           "-73.97051239,40.79664993", "-73.97693634,40.7599144",
           "-73.99306488,	40.73812866", "-74.00775146,40.74528885", "-73.98532867,40.74198914",
           "-73.99037933,40.76152802", "-73.98442078,40.74978638",
           "-73.99173737,	40.75437927", "-73.96742249,40.78820801", "-73.97813416,40.72935867",
           "-73.97171021,40.75943375", "-74.00737,40.7431221",
           "-73.99498749,	40.75517654", "-73.91600037,40.74634933", "-73.99924469,40.72764587",
           "-73.98488617,40.73621368", "-73.98627472,40.74737167"
           ]
      randomnum = random.randint(0, 53)
      b = a[randomnum]
      return b
   
   
   def getstore():
      taxi = ['Y', 'N']
      randomnum = random.randint(0, 1)
      return randomnum
   
   
   # @JsonPropertyOrder({"id", "vendorId", "pickupDate", "dropoffDate", "passengerCount", "pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude", "storeAndFwdFlag", "gcDistance", 
   # "tripDuration", "googleDistance", "googleDuration"})
   
   
   while True:
      i = int(i) + 1
      id = 'id' + str(random.randint(1665586, 8888888))
      vendorId = random.randint(1, 2)
      pickupDate = datetime.datetime.now().isoformat()
      dropoffDate = datetime.datetime.now() + timedelta(minutes=random.randint(30, 100))
      dropoffDate = dropoffDate.isoformat()
      passengerCount = random.randint(1, 9)
      location = getlatlon()
      location = location.split(",")
      pickupLongitude = location[0]
      pickupLatitude = location[1]
      location = getlatlon()
      location = location.split(",")
      dropoffLongitude = location[0]
      dropoffLatitude = location[1]
      storeAndFwdFlag = getstore()
      gcDistance = random.randint(1, 7)
      tripDuration = random.randint(8, 10000)
      googleDistance = gcDistance
      googleDuration = tripDuration
   
      new_dict = {}
      new_dict["id"] = id
      new_dict["vendorId"] = vendorId
      new_dict["pickupDate"] = pickupDate
      new_dict["dropoffDate"] = dropoffDate
      new_dict["passengerCount"] = passengerCount
      new_dict["pickupLongitude"] = pickupLongitude
      new_dict["pickupLatitude"] = pickupLatitude
      new_dict["dropoffLongitude"] = dropoffLongitude
      new_dict["dropoffLatitude"] = dropoffLatitude
      new_dict["storeAndFwdFlag"] = storeAndFwdFlag
      new_dict["gcDistance"] = gcDistance
      new_dict["tripDuration"] = tripDuration
      new_dict["googleDistance"] = googleDistance
      new_dict["googleDuration"] = googleDuration
   
      response = clientkinesis.put_record(StreamName=kdsname, Data=json.dumps(new_dict), PartitionKey=id)
      print("Total ingested:" + str(i) + ",ReqID:" + response['ResponseMetadata']['RequestId'] + ",HTTPStatusCode:" + str(
         response['ResponseMetadata']['HTTPStatusCode']))
   ```

3. Save the file in the root of your Cloud9 Instance and call it lab1.py

   ![Image](/repo_pmt_ws-fcj-005/images/2/2/22-002.png?featherlight=false&width=90pc)

### We will use this codebase to learn more about using Kinesis PutRecord API to ingest data to Kinesis Data Streams.

In this code we are using boto3 SDK which allows Python developers to write software that makes use of services like
Amazon Kinesis Data Streams, Amazon S3, Amazon EC2 etc.

`clientkinesis.put_record(StreamName=kdsname, Data=json.dumps(new_dict), PartitionKey=id)`

You can find the latest, most up to date, documentation at the doc site , including a list of services that are
supported in boto3. The PutRecord API writes a single data record into an Amazon Kinesis data stream. You can call
PutRecord to send data into the stream for real-time ingestion and subsequent processing, one record at a time. When you
are using PutRecord API, you must specify the name of the stream that captures, stores, and transports the data; a
partition key; and the data blob itself. The partition key is used by Kinesis Data Streams to distribute data across
shards. Kinesis Data Streams segregates the data records that belong to a stream into multiple shards, using the
partition key associated with each data record to determine the shard to which a given data record belongs. If ordering
is important, you must specify partitionKey in your request.

The payload you will send is in json format and the data blob is base64-encoded when the blob is serialized. When the
data blob (the payload before base64-encoding) is added to the partition key size, the total size must not exceed the
maximum record size (1 MiB).

Please note, After you write a record to a stream, you cannot modify that record or its order within the stream.

### Adding Multiple Records with PutRecords

The PutRecords operation sends multiple records to Kinesis Data Streams in a single request. By using PutRecords,
producers can achieve higher throughput when sending data to their Kinesis data streams. Each PutRecords request can
support up to 500 records and each record in the request can be as large as 1 MiB, up to a limit of 5 MiB for the entire
request, including partition keys. For more information about PutRecord and PutRecords operations, see PutRecord and
PutRecords .

### Things to note:

* Use PutRecords to achieve higher throughput and when ordering of records is not required. This is useful when you are
  looking to ingest application logs, service logs, click stream data etc to deliver data into a data lake.

* Use PutRecord when you require strict ordering of records within a shard. This is useful when you are looking to
  ingest ticker data or transactional data or data types where the order of events needs to be preserved.

### When to use PutRecord or PutRecords API instead of KPL

Using PutRecord or PutRecords is synchronous communication, so use the AWS SDK directly with PutRecord or PutRecords API
when an application cannot tolerate additional delay. Using KPL can incur an additional processing delay of up to
RecordMaxBufferedTime within the library (user-configurable). Larger values of RecordMaxBufferedTime result in higher
packing efficiencies and better performance. Applications that cannot tolerate this additional delay may need to use the
AWS SDK directly. For more information about using the AWS SDK with Kinesis Data Streams, see Developing Producers Using
the Amazon Kinesis Data Streams API with the AWS SDK for Java .

### Run the program

4. We need to install the boto3 library in your Cloud9 environment. Open a new terminal if one isn't already open and
   type the following to install boto3:

   `pip install boto3`

   This library is used within the python script and is necessary for execution.

5. Once you finish uploading the python script on your Cloud9 environment, run the program by entering the following
   command in Cloud9 terminal. The script will start ingesting records to your Kinesis Data Stream and print request ID
   and successful HTTP response code (200).

   `python lab1.py`

   ![Image](/repo_pmt_ws-fcj-005/images/2/2/22-003.png?featherlight=false&width=90pc)

{{% notice info %}}
If you receive an message like An error occurred (ResourceNotFoundException) when calling the PutRecord operation:
Stream input-stream under account 00000000000 not found, ensure your kdsname and region variables are set correctly in
lab1.py
{{% /notice %}}

6. Go to the Kinesis Data Streams management console . Select input-stream and click on Monitoring to monitor put record
   metrics.

   ![Image](/repo_pmt_ws-fcj-005/images/2/2/22-004.png?featherlight=false&width=90pc)


   {{% notice info %}}
   Please note that metrics can take up to 5 - 7 minutes to appear in CloudWatch Metrics after ingesting into Kinesis
   Data Streams
   {{% /notice %}}

   In this lab we have used PutRecord API to ingest data to Kinesis Data Streams. You can also use PutRecords API to
   send multiple records to Kinesis Data Streams in a single request. By using PutRecords, producers can achieve higher
   throughput when sending data to their Kinesis data stream. To learn more check this documentation

{{% notice warning %}}
Stop the python script by typing CTRL + C after you see results
{{% /notice %}}