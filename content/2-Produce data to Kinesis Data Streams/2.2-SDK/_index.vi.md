---
title: "Sử dụng Amazon SDK với Kinesis"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2.2 </b> "
---

Trong phần này, chúng ta sẽ tìm hiểu sâu về các khái niệm của
[Kinesis Data Streams API trên Amazon SDK](https://docs.aws.amazon.com/kinesis/latest/APIReference/Welcome.html).

Chúng ta sẽ hoàn thành các bước sau:

1. Tạo một Kinesis Data Stream (nếu chưa có).
2. Tìm hiểu về các khái niệm viết vào Kinesis Data Stream bằng cách sử dụng Kinesis Data Streams API.
3. Ghi dữ liệu vào Kinesis Data Stream.
4. Tối ưu hóa mã của chúng ta để viết dữ liệu vào Kinesis Data Stream với độ trễ thấp.
5. Đảm bảo dữ liệu được xuất bản theo thứ tự mỗi shard.

## Thu thập bản ghi vào Kinesis Data Stream:

Sau khi luồng được tạo, bạn có thể thêm dữ liệu vào luồng dưới dạng các bản ghi. Một bản ghi là một cấu trúc dữ liệu
chứa dữ liệu cần xử lý dưới dạng một blob dữ liệu.

Bạn có thể thêm dữ liệu vào một luồng bằng cách sử dụng PutRecords
và [PutRecord](https://docs.aws.amazon.com/kinesis/latest/APIReference/API_PutRecord.html) APIs. Hoạt động PutRecords
gửi nhiều bản ghi tới luồng của bạn mỗi yêu cầu HTTP, và hoạt động PutRecord đơn lẻ gửi các bản ghi tới
luồng của bạn một lần một bản ghi. Một yêu cầu HTTP riêng biệt được yêu cầu cho mỗi bản ghi.

![Image](/repo_pmt_ws-fcj-005/images/2/2/22-001.png?featherlight=false&width=90pc)

## Thêm một bản ghi đơn lẻ bằng PutRecord

Phần này cung cấp các ví dụ sử dụng PutRecord API để thu thập dữ liệu vào Kinesis Data Stream.

1. Tạo một tập lệnh Python mới trên Instance Cloud9 của bạn
    * File -> New File
2. Nhập mã bên dưới và thay thế **kdsname** nếu bạn đã đặt tên khác ở bước trước (Tạo một Kinesis Data Stream). Mã này sẽ tạo dữ liệu chuyến đi taxi ngẫu nhiên với một số thông tin vị trí và liên tục thu thập dữ liệu vào Kinesis Data Stream.

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


3. Lưu tệp vào thư mục gốc của Instance Cloud9 của bạn và đặt tên là lab1.py

   ![Image](/repo_pmt_ws-fcj-005/images/2/2/22-002.png?featherlight=false&width=90pc)

### Chúng ta sẽ sử dụng mã này để tìm hiểu thêm về việc sử dụng Kinesis PutRecord API để thu thập dữ liệu vào Kinesis Data Streams.

Trong mã này, chúng ta sử dụng boto3 SDK cho phép các nhà phát triển Python viết phần mềm sử dụng các dịch vụ như
Amazon Kinesis Data Streams, Amazon S3, Amazon EC2, v.v.

`clientkinesis.put_record(StreamName=kdsname, Data=json.dumps(new_dict), PartitionKey=id)`

Bạn có thể tìm thấy tài liệu mới nhất, cập nhật nhất, tại trang tài liệu, bao gồm danh sách các dịch vụ được
hỗ trợ trong boto3. API PutRecord ghi một bản ghi dữ liệu đơn lẻ vào một luồng dữ liệu Amazon Kinesis. Bạn có thể gọi
PutRecord để gửi dữ liệu vào luồng cho việc thu thập theo thời gian thực và xử lý tiếp theo, từng bản ghi một lần. Khi bạn
sử dụng API PutRecord, bạn phải chỉ định tên của luồng để thu thập, lưu trữ và vận chuyển dữ liệu; một
khóa phân vùng; và blob dữ liệu chính nó. Khóa phân vùng được sử dụng bởi Kinesis Data Streams để phân phối dữ liệu qua
các shard. Kinesis Data Streams phân tách các bản ghi dữ liệu thuộc về một luồng thành nhiều shard, sử dụng
khóa phân vùng liên kết với mỗi bản ghi dữ liệu để xác định shard mà một bản ghi dữ liệu nhất định thuộc về. Nếu thứ tự là quan trọng, bạn phải chỉ định partitionKey trong yêu cầu của mình.

Payload bạn sẽ gửi là ở định dạng json và blob dữ liệu được mã hóa base64 khi blob được tuần tự hóa. Khi
blob dữ liệu (payload trước khi mã hóa base64) được thêm vào kích thước khóa phân vùng, tổng kích thước không được vượt quá
kích thước bản ghi tối đa (1 MiB).

Xin lưu ý, sau khi bạn ghi một bản ghi vào luồng, bạn không thể sửa đổi bản ghi đó hoặc thứ tự của nó trong luồng.

### Thêm nhiều bản ghi với PutRecords

Hoạt động PutRecords gửi nhiều bản ghi tới Kinesis Data Streams trong một yêu cầu duy nhất. Bằng cách sử dụng PutRecords,
nhà sản xuất có thể đạt được thông lượng cao hơn khi gửi dữ liệu tới luồng dữ liệu Kinesis của họ. Mỗi yêu cầu PutRecords có thể
hỗ trợ lên tới 500 bản ghi và mỗi bản ghi trong yêu cầu có thể lớn tới 1 MiB, lên tới giới hạn 5 MiB cho toàn bộ
yêu cầu, bao gồm cả khóa phân vùng. Để biết thêm thông tin về hoạt động PutRecord và PutRecords, hãy xem PutRecord và
PutRecords.

### Những điều cần lưu ý:

* Sử dụng PutRecords để đạt được thông lượng cao hơn và khi không cần thứ tự của các bản ghi. Điều này hữu ích khi bạn
  muốn thu thập nhật ký ứng dụng, nhật ký dịch vụ, dữ liệu click stream, v.v., để đưa dữ liệu vào một hồ dữ liệu.

* Sử dụng PutRecord khi bạn yêu cầu thứ tự nghiêm ngặt của các bản ghi trong một shard. Điều này hữu ích khi bạn muốn
  thu thập dữ liệu ticker hoặc dữ liệu giao dịch hoặc các loại dữ liệu mà thứ tự của các sự kiện cần được bảo quản.

### Khi nào nên sử dụng PutRecord hoặc PutRecords API thay vì KPL

Sử dụng PutRecord hoặc PutRecords là giao tiếp đồng bộ, vì vậy hãy sử dụng AWS SDK trực tiếp với PutRecord hoặc PutRecords API
khi một ứng dụng không thể chịu được sự chậm trễ bổ sung. Sử dụng KPL có thể gây ra sự chậm trễ xử lý bổ sung lên tới
RecordMaxBufferedTime trong thư viện (có thể cấu hình người dùng). Các giá trị lớn hơn của RecordMaxBufferedTime dẫn đến
hiệu quả đóng gói cao hơn và hiệu suất tốt hơn. Các ứng dụng không thể chịu được sự chậm trễ bổ sung này có thể cần sử dụng
AWS SDK trực tiếp. Để biết thêm thông tin về việc sử dụng AWS SDK với Kinesis Data Streams, hãy xem Phát triển nhà sản xuất sử dụng
Amazon Kinesis Data Streams API với AWS SDK cho Java.

### Chạy chương trình

4. Chúng ta cần cài đặt thư viện boto3 trong môi trường Cloud9 của bạn. Mở một terminal mới nếu chưa có sẵn và
   gõ lệnh sau để cài đặt boto3:

   `pip install boto3`

   Thư viện này được sử dụng trong tập lệnh python và cần thiết để thực thi.

5. Sau khi bạn hoàn tất tải lên tập lệnh python trên môi trường Cloud9 của mình, chạy chương trình bằng cách nhập lệnh sau
   trong terminal Cloud9. Tập lệnh sẽ bắt đầu thu thập bản ghi vào Kinesis Data Stream của bạn và in ra request ID
   và mã phản hồi HTTP thành công (200).

   `python lab1.py`

   ![Image](/repo_pmt_ws-fcj-005/images/2/2/22-003.png?featherlight=false&width=90pc)

{{% notice info %}}
Nếu bạn nhận được thông báo như An error occurred (ResourceNotFoundException) khi gọi hoạt động PutRecord:
Stream input-stream dưới tài khoản 00000000000 không tìm thấy, hãy đảm bảo các biến kdsname và region của bạn được đặt chính xác trong
lab1.py.
{{% /notice %}}

6. Truy cập bảng điều khiển quản lý Kinesis Data Streams. Chọn input-stream và nhấp vào Monitoring để theo dõi số liệu bản ghi đặt vào.

   ![Image](/repo_pmt_ws-fcj-005/images/2/2/22-004.png?featherlight=false&width=90pc)


   {{% notice info %}}
   Xin lưu ý rằng số liệu có thể mất tới 5 - 7 phút để xuất hiện trong CloudWatch Metrics sau khi thu thập vào Kinesis
   Data Streams.
   {{% /notice %}}

   Trong lab này, chúng ta đã sử dụng PutRecord API để thu thập dữ liệu vào Kinesis Data Streams. Bạn cũng có thể sử dụng PutRecords API để
   gửi nhiều bản ghi tới Kinesis Data Streams trong một yêu cầu duy nhất. Bằng cách sử dụng PutRecords, nhà sản xuất có thể đạt được thông lượng cao hơn
   khi gửi dữ liệu tới luồng dữ liệu Kinesis của họ. Để tìm hiểu thêm, hãy kiểm tra tài liệu này.

{{% notice warning %}}
Dừng tập lệnh python bằng cách gõ CTRL + C sau khi bạn thấy kết quả.
{{% /notice %}}
