---
title: "Lambda Consumer for Kinesis Data Stream"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 5.  </b> "
---

In this lab we will use a Lambda consumer to consume data from the Kinesis Data Stream. As part of the lab we will
create the Lambda function to process records from the Kinesis Data Stream

![Image](/repo_pmt_ws-fcj-005/images/5/5-001.png?featherlight=false&width=90pc)

The Lambda function does a few things:

1. It inspects the incoming message for unclean records with missing fields and filters them out.
2. It then sends the clean records to DynamoDB.
3. If it receives a throttling error, it determines if all the records received failed or if some records failed.
4. If all records failed, it raises an exception, so the Lambda service can retry with the same payload (the service
   keeps retrying with the same payload until it receives a success).
5. If any of the retries are successful, it returns a success. If none of the retries are successful, it raises an
   exception, so the Lambda service can retry with the same payload.

NOTE : In this case, there could be duplicate records sent. You can increase the number of retries or save the records
somewhere to process later and move on as alternate strategies to prevent duplicates.

## Preparation
To analyze the data from the Kinesis Data Stream we will now provision the following services

### Dynamo DB Table

#### Steps

In the AWS Management Console, navigate to DynamoDB by searching for it in the top search bar
Click the orange box which reads Create table to open the Create wizard.

#### Table Details

* **Table Name** : kinesisAggs
* **Partition Key** : vendorId (Number)
* Leave the rest as default and click Create table.
Change the type of vendorId to number from the default of string
vendorId is case sensitive!

NOTE: This is the same name of the table we will set up in the Lambda Function environment.

![Image](/repo_pmt_ws-fcj-005/images/5/5-002.png?featherlight=false&width=90pc)

### Create Lambda Function

Go to the Lambda console, click on Create function.
Give the function a name kinesisLambdaConsumer01 for example
Choose Python3.9 as the Runtime

![Image](/repo_pmt_ws-fcj-005/images/5/5-003.png?featherlight=false&width=90pc)
Choose the IAM role, KinesisLambdaConsumerRole which has the permissions needed for the function already granted.
Click on **Create Function** to create the function
Next we will add the necessary code, settings for the set up.

Download this [zip file](/repo_pmt_ws-fcj-005/resources/5/lambda-deploy.zip) zip file which has the Lambda consumer and the required packages into it to your machine

Click on **Upload from** .zip file UploadZipFile

Select the zip file and click on **Save**


Select Configuration and Environment Variables to add the dynamoDB table name created earlier and click on Save.

Key: dynamoDBTableName
Value: kinesisAggs

![Image](/repo_pmt_ws-fcj-005/images/5/5-004.png?featherlight=false&width=90pc)

Select Configuration and General Configuration, click on Edit and increase the timeout from the default 3 secs to 1 minute and click on Save.

![Image](/repo_pmt_ws-fcj-005/images/5/5-005.png?featherlight=false&width=90pc)

Click on AddTrigger select Kinesis in Select a trigger.
Select the Kinesis stream created in lab 0.
Select the following: 

i. batch size as 1000 (Each batch contains records from a single shard/stream, this is largest number of records to read at once )

ii. batch window as 120 (Max time to gather records before invoking the Lambda function)

iii. starting position as LATEST (Start reading just after the most recent record in the shard, so that you always read the most recent data in the shard)

iv. Tumbling window duration as 30 (items in the stream are grouped by this window and sent to the processing Lambda function. The function returns a state value that is passed to the next tumbling window.)


![Image](/repo_pmt_ws-fcj-005/images/5/5-006.png?featherlight=false&width=90pc)

![Image](/repo_pmt_ws-fcj-005/images/5/5-007.png?featherlight=false&width=90pc)


Below is the code for the Lambda function for your reference, in case you want to change the function for further customizations

```python
from __future__ import print_function
from aws_kinesis_agg.deaggregator import deaggregate_records, iter_deaggregate_records
from decimal import Decimal
import base64
import json
import boto3
import os

def lambda_handler(event, context):
    my_region = os.environ['AWS_REGION']
    dynamoDBTableName = os.environ['dynamoDBTableName']
    dynamodb = boto3.resource('dynamodb', region_name=my_region)
    table = dynamodb.Table(dynamoDBTableName)
    raw_kinesis_records = event['Records']
    if event['isFinalInvokeForWindow']:

            item = {
                'windowEnd': event["window"]["end"],
                'windowStart': event["window"]["start"],
                'passenger': event["state"]["passengerCount"],
                'vendorId': event["state"]["vendorId"]
            }
            # Store to dynamoDB Table
            ddb_data = json.loads(json.dumps(item), parse_float=Decimal)
            #print ( "ddb_data", ddb_data)
            response = table.put_item(
                Item=ddb_data
            )
            print ('response' ,response)
        else:
            print('Aggregate invoke')

        #Check for early terminations
        if event['isWindowTerminatedEarly']:
            print('Window terminated early')
        #Aggregation logic

        if (str(event["state"])) == "{}":
            stateJson = {"state":{"passengerCount":0}}
            event.update(stateJson)

        state = event['state']
        # Deaggregate all records in one call
        user_records = deaggregate_records(raw_kinesis_records)
        
        # Iterate through de-aggregated records
        for record in user_records:
            payload=base64.b64decode(record["kinesis"]["data"]).decode("UTF-8")
            #print("Decoded payload: " + str(payload))
            formattedPayload = json.loads(payload)
            #p = str(payload)
            if "passengerCount" in formattedPayload:
            value = formattedPayload["passengerCount"]
            state['passengerCount'] += value
            state['vendorId'] = formattedPayload['vendorId']

        return {'state': state}
```
The function will start processing the data from the Kinesis stream, you can monitor the logs and access the dynamoDB table created to check the aggregations for the tumbling window, for the total amount value per window.

{{% notice info %}}
Check and/or start the Producer client program now. Go to [Run producer client program](2-Produce-data-to-Kinesis-Data-Streams/) in Lab 1 to learn how to do this.
{{% /notice %}}
### Setup IAM permission

![Image](/repo_pmt_ws-fcj-005/images/5/5-008.png?featherlight=false&width=90pc)

## Monitoring
Click on the Monitor section of the Lambda function, to look at the recent invocations of the function.

![Image](/repo_pmt_ws-fcj-005/images/5/5-009.png?featherlight=false&width=90pc)
You can also click on View logs in CloudWatch to see the detailed logs for each invocation.

You can enable any additional logging for further debugging and check in the logs for the details.

![Image](/repo_pmt_ws-fcj-005/images/5/5-010.png?featherlight=false&width=90pc)

## Check DynamoDB 


![Image](/repo_pmt_ws-fcj-005/images/5/5-011.png?featherlight=false&width=90pc)