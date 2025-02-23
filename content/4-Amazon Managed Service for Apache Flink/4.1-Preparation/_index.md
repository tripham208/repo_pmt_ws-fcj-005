---
title: "(Optional) Setup Kinesis Data Analytics Studio"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 4.1 </b> "
---

{{% notice info %}}
This is an optional section. CFN has already provisioned a Kinesis Analytics notebook called KDA-Studio-1. You can skip
this section if you want to re-use the provisioned notebook. You can continue this section in case you want to learn how
to Setup Kinesis Data Analytics Studio from AWS console.
Navigate to the Amazon Kinesis Analytics services and select Studio tab. Select **Create studio notebook**.
{{% /notice %}}

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-001.png?featherlight=false&width=90pc)

Choose **Create with custom settings**. Provide studio notebook name.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-002.png?featherlight=false&width=90pc)

Choose the option Choose from **IAM roles that Kinesis Data Analytics can assume** and select **kinesis-analytics-KDA-
*** role from the drop down. Choose kinesislab database from drop down for AWS Glue database.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-003.png?featherlight=false&width=90pc)

Choose **Add custom connector**.

Download to your local machine the
linked [Elasticsearch connector](https://mvnrepository.com/artifact/org.apache.flink/flink-sql-connector-elasticsearch7)
jar matching your Kinesis Data Analytics Studio Flink version--in our example it is v1.13.2 .
Upload this file to an Amazon S3 Bucket with permissions to your Kinesis Data Analytics Studio application.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-004.png?featherlight=false&width=90pc)
Select the S3 bucket and path you have uploaded the sql connector jar file. Check CFN output section to find
S3BucketForConnector value. This will be S3 bucket. Provide file name **flink-sql-connector-elasticsearch7_2.11-1.**13.2
in Path of S3 object.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-005.png?featherlight=false&width=90pc)

Choose **Next**.

Choose **Create studio notebook**.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-006.png?featherlight=false&width=90pc)

