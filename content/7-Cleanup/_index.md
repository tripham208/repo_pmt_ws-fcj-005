---
title: "Cleanup"
date: "`r Sys.Date()`"
weight: 7
chapter: false
pre: " <b> 7.  </b> "
---

Thanks for taking time to learn about Kinesis Data Streams, and how clients can interact with it!

To clean up the resources for this workshop:

1. [ ] Delete
   the [CloudFormation](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringText=&filteringStatus=active&viewNested=true)
   Template

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-001.png?featherlight=false&width=90pc)

2. [ ] Navigate to the [Amazon DynamoDB Console](https://console.aws.amazon.com/dynamodbv2/home?region=us-west-2#tables)
   and delete the lease table.

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-002.png?featherlight=false&width=90pc)
3. [ ] Delete the [Lambda](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions) Function

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-003.png?featherlight=false&width=90pc)
4. [ ] Delete the [Kinesis Stream](https://us-east-1.console.aws.amazon.com/kinesis/home?region=us-east-1#/dashboard)

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-004.png?featherlight=false&width=90pc)
5. [ ] Empty and Delete the [S3](https://us-east-1.console.aws.amazon.com/s3/home?region=us-east-1#) Buckets housing
   your data

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-005.png?featherlight=false&width=90pc)
6. [ ] Delete
   the [Amazon Managed Service for Apache Flink Notebook](https://us-east-1.console.aws.amazon.com/flink/home?region=us-east-1#/dashboard)

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-006.png?featherlight=false&width=90pc)
7. [ ] Delete the [Cloudwatch](https://us-east-1.console.aws.amazon.com/cloudwatch/home?region=us-east-1) log

   ![Image](/repo_pmt_ws-fcj-005/images/7/7-007.png?featherlight=false&width=90pc)
