---
title: "Launch Resources"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 1.2 </b> "
---

As discussed, we will be launching several resources for this workshop on your behalf in order to simplify examples.

If you are walking through this workshop on your own, download
this [CF Template](/repo_pmt_ws-fcj-005/resources/kinesis-immersion-day-cfn.yaml) to get started.
This CloudFormation template will launch the following resources (corresponding to the architecture below)

* A Virtual Private Cloud (VPC) with Corresponding security groups for your Cloud9 Instance.
* A Cloud9 Instance to run the Kinesis Client Library and explore code
* Two S3 buckets to store your source Taxi Trip dataset, and accept the output of your curated data in the Kinesis Data
  Firehose lab
* A Kinesis Data Analytics Studio application and an associated Glue Database
* A Lambda function to process data from Kinesis Data Streams

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-001.png?featherlight=false&width=90pc)

### Upload Artifact

* Create [S3](https://us-east-1.console.aws.amazon.com/s3/buckets?region=us-east-1&bucketType=general)  bucket

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-002.png?featherlight=false&width=90pc)
* Upload jar

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-003.png?featherlight=false&width=90pc)

### Create IAM role for CloudFormation

* Create [IAM role](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/roles)

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-004.png?featherlight=false&width=90pc)

### CloudFormation

* This can be achieved by either searching CloudFormation at the top of the page, or finding it in the ▼Services button
  at the top left.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-005.png?featherlight=false&width=90pc)

* On the CloudFormation page, let's click on Create Stack with new resources, and upload our CloudFormation .yml file.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-006.png?featherlight=false&width=90pc)

* On the page that opens, select the **Upload a Template File** button, and Choose the file to upload.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-007.png?featherlight=false&width=90pc)

* Upload the file and click on **Next**.

  Give the workshop template a name, like **kinesis-ws** so that it is easy to remember, and update the Parameter
  called **input-stream** if you created a stream with a different name. Click on **Next** again.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-008.png?featherlight=false&width=90pc)

* Choose your IAM role. The third page allows for adding Tags. Add these if you'd like, and click on **Next**.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-009.png?featherlight=false&width=90pc)

* At the bottom of the last page, check the boxes allowing for the creation of IAM resources and auto-expansion of the
  CloudFormation template. Then, **Create Stack!**

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-010.png?featherlight=false&width=90pc)
