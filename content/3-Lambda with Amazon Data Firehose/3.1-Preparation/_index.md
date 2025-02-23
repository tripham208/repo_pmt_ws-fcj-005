---
title: "Preparation"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1 </b> "
---

## Create Glue Table

Create the external table that Kinesis Data Firehose will use as a schema for data format conversion

In this section you will create the external table that Kinesis Data Firehose will use as a schema for data format
conversion

Go to the Athena console, select Setting under Query editor. Select Manage
![Image](/repo_pmt_ws-fcj-005/images/3/1/31-001.png?featherlight=false&width=90pc)

Type the S3 location into the Query Result Location box. Check S3Bucket value in the resource section of CloudFormation
stack and append with the output folder /query_result/. Click on Save.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-002.png?featherlight=false&width=90pc)

Example: `s3://nyctaxitrips-s3bucket-initials/query_result/.`

Return to the Editor tab and select kinesislab from the Database dropdown

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-003.png?featherlight=false&width=90pc)

Paste the following sql statement in the query window and click on Run query.
Be sure to edit the <BUCKET-NAME> of the location (last line) with the name of S3 bucket. Obtain the S3Bucket value in
the resource section of CloudFormation stack. It starts with nyctaxitrips-*

```sql
CREATE
EXTERNAL TABLE
`nyctaxitrips`
(
`id` string,
`vendorId` int,
`pickupDate` string,
`dropoffDate` string,
`passengerCount` int,
`pickupLongitude` double,
`pickupLatitude` double,
`dropoffLongitude` double,
`dropoffLatitude` double,
`storeAndFwdFlag` string,
`gcDistance` double,
`tripDuration` int,
`googleDistance`int,
`googleDuration`int,
`source`string
)
PARTITIONED BY ( `year` string, `month` string, `day` string, `hour` string)
ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.parquet.MapredParquetOutputFormat'
LOCATION 's3://<<BUCKET-NAME>>/nyctaxitrips/'
```

You see “Query successful” message.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-004.png?featherlight=false&width=90pc)

## Create Amazon Data Firehose Delivery Stream

In this section, you will create a new Amazon Data Firehose Delivery Stream that use to stream NYC taxi trips data from
Kinesis Data Stream.

Using the AWS Console, navigate to the Amazon Kinesis services and press Get Started when prompted.

Select **Amazon Data Firehose** and click on **Create delivery stream**.

If your view is different, click Amazon Kinesis at the top left menu bar to return to the overview page.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-005.png?featherlight=false&width=90pc)

For Source, choose Amazon Kinesis Data Stream. Choose Amazon S3 as Destination. Click on Browse button and select
input-stream as Kinesis data stream.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-006.png?featherlight=false&width=90pc)

Enter a unique name for the Delivery stream name, eg, nyc-taxi-trips. Select data transformation Enabled. Select
NYCTaxiTrips-DataTransformation Lambda. The Lambda will add new column called source in the incoming data and populate
with NYCTAXI value.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-007.png?featherlight=false&width=90pc)

Select Record format conversion as Enabled and choose Output format as Apache Parquet. For AWS Glue region, choose the
region that you created the Athena table earlier. For AWS Glue database, choose the database kinesislab. For AWS Glue
table choose the table nyctaxitrips you created earlier

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-008.png?featherlight=false&width=90pc)

Click on the dropdown next to S3 bucket and select the bucket starts with nyctaxitrips-*.
For S3 prefix, copy and paste the following:


`nyctaxitrips/year=!{timestamp:YYYY}/month=!{timestamp:MM}/day=!{timestamp:dd}/hour=!{timestamp:HH}/`

For S3 error prefix copy and paste the following:


`nyctaxitripserror/!{firehose:error-output-type}/year=!{timestamp:YYYY}/month=!{timestamp:MM}/day=!{timestamp:dd}/hour=!{timestamp:HH}/`

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-009.png?featherlight=false&width=90pc)

Change Buffer interval to 60 second.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-010.png?featherlight=false&width=90pc)

Click on Create delivery stream.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-011.png?featherlight=false&width=90pc)

