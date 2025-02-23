---
title: "Create a Kinesis Data Stream"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1.1 </b> "
---

In this section, you will create a new Kinesis Data Stream that we will use to stream a data set of NYC taxi trips.

1. Using the AWS Console, navigate to the Amazon Kinesis services and press Get Started when prompted.

2. Select Create data stream to navigate to the Amazon Kinesis Data Stream service.

   ![Image](/repo_pmt_ws-fcj-005/images/1/1/11-001.png?featherlight=false&width=90pc)

3. When prompted, enter **<your-stream-name>** as your Kinesis Data Stream name.When prompted, enter input-stream as
   your Kinesis
   Data Stream name.When prompted, enter **<your-stream-name>** as your Kinesis Data Stream name.

   Keep 'On demand' selected as your capacity mode, and leave all else as default.

   ![Image](/repo_pmt_ws-fcj-005/images/1/1/11-002.png?featherlight=false&width=90pc)

4. When finally created, you should see a status of Active on the stream.


   ![Image](/repo_pmt_ws-fcj-005/images/1/1/11-003.png?featherlight=false&width=90pc)