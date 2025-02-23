---
title: "Chuẩn bị"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1 </b> "
---

## Tạo Bảng Glue

Tạo bảng ngoại mà Kinesis Data Firehose sẽ sử dụng làm schema cho chuyển đổi định dạng dữ liệu

Trong phần này, bạn sẽ tạo bảng ngoại mà Kinesis Data Firehose sẽ sử dụng làm schema cho chuyển đổi định dạng dữ liệu.

Đi tới bảng điều khiển Athena, chọn Setting dưới Query editor. Chọn Manage.
![Image](/repo_pmt_ws-fcj-005/images/3/1/31-001.png?featherlight=false&width=90pc)

Nhập vị trí S3 vào ô Query Result Location. Kiểm tra giá trị S3Bucket trong phần tài nguyên của CloudFormation stack và
thêm thư mục output /query_result/. Nhấp vào Save.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-002.png?featherlight=false&width=90pc)

Ví dụ: `s3://nyctaxitrips-s3bucket-initials/query_result/.`

Quay lại tab Editor và chọn kinesislab từ danh sách Database.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-003.png?featherlight=false&width=90pc)

Dán câu lệnh SQL sau vào cửa sổ truy vấn và nhấp vào Run query.
Hãy nhớ chỉnh sửa <BUCKET-NAME> của vị trí (dòng cuối cùng) với tên của S3 bucket. Lấy giá trị S3Bucket trong phần tài
nguyên của CloudFormation stack. Nó bắt đầu với nyctaxitrips-*

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

Bạn sẽ thấy thông báo "Query successful".

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-004.png?featherlight=false&width=90pc)

## Tạo Amazon Data Firehose Delivery Stream

Trong phần này, bạn sẽ tạo một Amazon Data Firehose Delivery Stream mới để sử dụng để truyền dữ liệu chuyến đi taxi ở
NYC từ Kinesis Data Stream.

Sử dụng AWS Console, điều hướng đến dịch vụ Amazon Kinesis và nhấn Get Started khi được nhắc nhở.

Chọn **Amazon Data Firehose** và nhấp vào **Create delivery stream**.

Nếu giao diện của bạn khác, nhấp vào Amazon Kinesis ở thanh menu phía trên bên trái để quay lại trang tổng quan.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-005.png?featherlight=false&width=90pc)

Đối với Source, chọn Amazon Kinesis Data Stream. Chọn Amazon S3 làm Destination. Nhấp vào nút Browse và chọn
input-stream làm Kinesis data stream.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-006.png?featherlight=false&width=90pc)

Nhập một tên duy nhất cho Delivery stream name, ví dụ, nyc-taxi-trips. Chọn data transformation Enabled. Chọn
NYCTaxiTrips-DataTransformation Lambda. Lambda sẽ thêm cột mới có tên source trong dữ liệu đến và điền giá trị NYCTAXI.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-007.png?featherlight=false&width=90pc)

Chọn Record format conversion as Enabled và chọn Output format là Apache Parquet. Đối với AWS Glue region, chọn khu vực
mà bạn đã tạo bảng Athena trước đó. Đối với AWS Glue database, chọn cơ sở dữ liệu kinesislab. Đối với AWS Glue table,
chọn bảng nyctaxitrips bạn đã tạo trước đó.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-008.png?featherlight=false&width=90pc)

Nhấp vào danh sách thả xuống bên cạnh S3 bucket và chọn bucket bắt đầu với nyctaxitrips-*.
Đối với S3 prefix, sao chép và dán nội dung sau:

`nyctaxitrips/year=!{timestamp:YYYY}/month=!{timestamp:MM}/day=!{timestamp:dd}/hour=!{timestamp:HH}/`

Đối với S3 error prefix sao chép và dán nội dung sau:

`nyctaxitripserror/!{firehose:error-output-type}/year=!{timestamp:YYYY}/month=!{timestamp:MM}/day=!{timestamp:dd}/hour=!{timestamp:HH}/`

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-009.png?featherlight=false&width=90pc)

Thay đổi Buffer interval thành 60 giây.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-010.png?featherlight=false&width=90pc)

Nhấp vào Create delivery stream.

![Image](/repo_pmt_ws-fcj-005/images/3/1/31-011.png?featherlight=false&width=90pc)

