---
title: "Stream the data and validate output"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

Now, Lets test the flow and validate the output.

Start ingesting data into the Kinesis Data Stream

{{% notice note %}}
Tip : Check and/or start the [Producer client program now](2-Produce-data-to-Kinesis-Data-Streams/). You can use either the SDK or the KPL producer--they will
elicit the same result.
Once the Producer starts ingesting the data in Kinesis Stream, the Amazon Data Firehose delivery stream you created will
ingest the records, buffer it, transform it by adding a new column and deliver it to the S3 destination under the prefix
provided. The buffer is set to 1 minutes or 128 MB, whichever happens first. It may be 1 minutes before you see data in
S3.
{{% /notice %}}

Wait for a few minutes

Navigate to the Kinesis Console and click on the “nyc-taxi-trips” Data Firehose delivery stream

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-001.png?featherlight=false&width=90pc)

Click on the Monitoring tab and you should start to see some metrics from your Firehose delivery stream

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-002.png?featherlight=false&width=90pc)

Click on the Configuration tab. Scroll down to the Destination setting section. Then click on the S3 bucket link.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-003.png?featherlight=false&width=90pc)

If it has been 1 minutes, you should see some data in your S3 bucket. If not, please wait a bit longer. Navigate into
the nyctaxitrips folder and through the subfolders until you get to the data files in parquet format.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-004.png?featherlight=false&width=90pc)

Select one of the parquet files. Then under Actions, choose the Select Object Actions then Query with S3 Select.

Keep Apache Parquet as selected for Input settings. Choose CSV for Output Settings, then select Run SQL Query.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-005.png?featherlight=false&width=90pc)

Observe that you are able to see your taxi trip data in the parquet file in S3. Amazon Data Firehose has done its job.
Now navigate to the [Athena console](https://us-east-1.console.aws.amazon.com/athena/home?region=us-east-1).

Under Databases, select the kinesislab database. Then click on the vertical ellipsis (⋮) to the right of the
nyctaxitrips table and choose Load partitions.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-006.png?featherlight=false&width=90pc)

Now click on the vertical ... to the right of the nyctaxitrips table and choose Preview table.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-007.png?featherlight=false&width=90pc)

This will launch a simple SQL query, which when finished will look like:

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-008.png?featherlight=false&width=90pc)

If you do not see data, please re-run the “Load partitions”step.
Observe that you are able to query your taxi trip data via SQL using Athena.