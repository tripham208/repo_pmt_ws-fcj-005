---
title: "Người tiêu dùng Lambda cho Kinesis Data Stream"
date: "`r Sys.Date()`"
weight: 5
chapter: false
pre: " <b> 5.  </b> "
---

Trong bài lab này, chúng ta sẽ sử dụng một người tiêu dùng Lambda để tiêu thụ dữ liệu từ Kinesis Data Stream. Là một phần của bài lab, chúng ta sẽ
tạo hàm Lambda để xử lý các bản ghi từ Kinesis Data Stream.

![Image](/repo_pmt_ws-fcj-005/images/5/5-001.png?featherlight=false&width=90pc)

Hàm Lambda thực hiện một số việc sau:

1. Nó kiểm tra tin nhắn đến để tìm các bản ghi không sạch với các trường thiếu và lọc chúng ra.
2. Sau đó, nó gửi các bản ghi sạch đến DynamoDB.
3. Nếu nó nhận được lỗi giới hạn, nó xác định xem tất cả các bản ghi nhận được đã thất bại hay một số bản ghi đã thất bại.
4. Nếu tất cả các bản ghi thất bại, nó tạo ra một ngoại lệ, để dịch vụ Lambda có thể thử lại với cùng một payload (dịch vụ
   tiếp tục thử lại với cùng một payload cho đến khi nó nhận được thành công).
5. Nếu bất kỳ lần thử lại nào thành công, nó trả về thành công. Nếu không có lần thử lại nào thành công, nó tạo ra một
   ngoại lệ, để dịch vụ Lambda có thể thử lại với cùng một payload.

LƯU Ý: Trong trường hợp này, có thể có các bản ghi trùng lặp được gửi. Bạn có thể tăng số lần thử lại hoặc lưu các bản ghi
ở một nơi khác để xử lý sau và tiếp tục như các chiến lược thay thế để ngăn chặn các bản ghi trùng lặp.

## Chuẩn bị
Để phân tích dữ liệu từ Kinesis Data Stream, chúng ta sẽ cung cấp các dịch vụ sau

### Bảng DynamoDB

#### Các bước

Trong AWS Management Console, điều hướng đến DynamoDB bằng cách tìm kiếm nó trong thanh tìm kiếm phía trên cùng
Nhấp vào hộp màu cam có ghi Create table để mở trình hướng dẫn Create.

#### Chi tiết Bảng

* **Table Name** : kinesisAggs
* **Partition Key** : vendorId (Number)
* Để các cài đặt khác là mặc định và nhấp vào Create table.
Thay đổi loại của vendorId thành number từ mặc định là string
vendorId phân biệt chữ hoa và chữ thường!
Bảng DynamoDB

LƯU Ý: Đây là tên của bảng mà chúng ta sẽ thiết lập trong môi trường hàm Lambda.

![Image](/repo_pmt_ws-fcj-005/images/5/5-002.png?featherlight=false&width=90pc)

### Tạo Hàm Lambda

Đi tới bảng điều khiển Lambda, nhấp vào Create function.
Đặt tên hàm là kinesisLambdaConsumer01 chẳng hạn
Chọn Python3.9 làm Runtime.

![Image](/repo_pmt_ws-fcj-005/images/5/5-003.png?featherlight=false&width=90pc)
Chọn vai trò IAM, KinesisLambdaConsumerRole đã có các quyền cần thiết cho hàm 
Nhấp vào **Create Function** để tạo hàm
Tiếp theo chúng ta sẽ thêm mã và cài đặt cần thiết cho việc thiết lập.

Tải xuống [tệp zip này](/repo_pmt_ws-fcj-005/resources/5/lambda-deploy.zip) chứa Lambda consumer và các gói cần thiết vào máy tính của bạn

Nhấp vào **Upload from** .zip file UploadZipFile

Chọn tệp zip và nhấp vào **Save**


Chọn Configuration và Environment Variables để thêm tên bảng dynamoDB đã tạo trước đó và nhấp vào Save.

Key: dynamoDBTableName
Value: kinesisAggs

![Image](/repo_pmt_ws-fcj-005/images/5/5-004.png?featherlight=false&width=90pc)

Chọn Configuration và General Configuration, nhấp vào Edit và tăng thời gian chờ từ 3 giây mặc định lên 1 phút và nhấp vào Save.

![Image](/repo_pmt_ws-fcj-005/images/5/5-005.png?featherlight=false&width=90pc)

Nhấp vào AddTrigger, chọn Kinesis trong Select a trigger.
Chọn Kinesis stream đã tạo ở lab 0.
Chọn các thông tin sau:

i. batch size là 1000 (Mỗi batch chứa các bản ghi từ một shard/stream duy nhất, đây là số lượng bản ghi lớn nhất để đọc cùng một lúc)

ii. batch window là 120 (Thời gian tối đa để thu thập các bản ghi trước khi gọi hàm Lambda)

iii. starting position là LATEST (Bắt đầu đọc ngay sau bản ghi gần đây nhất trong shard, để bạn luôn đọc dữ liệu mới nhất trong shard)

iv. Tumbling window duration là 30 (các mục trong stream được nhóm theo cửa sổ này và gửi đến hàm Lambda xử lý. Hàm trả về một giá trị trạng thái được truyền đến cửa sổ tumbling tiếp theo.)


![Image](/repo_pmt_ws-fcj-005/images/5/5-006.png?featherlight=false&width=90pc)

![Image](/repo_pmt_ws-fcj-005/images/5/5-007.png?featherlight=false&width=90pc)


Dưới đây là mã cho hàm Lambda để bạn tham khảo, trong trường hợp bạn muốn thay đổi hàm để tùy chỉnh thêm

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
Hàm sẽ bắt đầu xử lý dữ liệu từ stream Kinesis, bạn có thể giám sát nhật ký và truy cập bảng dynamoDB đã tạo để kiểm tra các tổng hợp cho cửa sổ tumbling, cho giá trị tổng số mỗi cửa sổ.

{{% notice info %}}
Kiểm tra và/hoặc bắt đầu chương trình khách hàng Producer ngay bây giờ. Đi tới [Chạy chương trình khách hàng Producer](2-Produce-data-to-Kinesis-Data-Streams/) trong Lab 1 để tìm hiểu cách thực hiện điều này.
{{% /notice %}}
### Thiết lập quyền IAM

![Image](/repo_pmt_ws-fcj-005/images/5/5-008.png?featherlight=false&width=90pc)

## Giám sát
Nhấp vào phần Monitor của hàm Lambda, để xem các lần gọi gần đây của hàm.

![Image](/repo_pmt_ws-fcj-005/images/5/5-009.png?featherlight=false&width=90pc)
Bạn cũng có thể nhấp vào View logs trong CloudWatch để xem nhật ký chi tiết cho từng lần gọi.

Bạn có thể bật bất kỳ nhật ký bổ sung nào để gỡ lỗi thêm và kiểm tra các chi tiết trong nhật ký.

![Image](/repo_pmt_ws-fcj-005/images/5/5-010.png?featherlight=false&width=90pc)

## Kiểm tra DynamoDB 


![Image](/repo_pmt_ws-fcj-005/images/5/5-011.png?featherlight=false&width=90pc)
