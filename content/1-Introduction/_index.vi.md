---
title: "Giới thiệu"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

## Kinesis Data Streams là gì?

Amazon Kinesis Data Streams (KDS) là dịch vụ truyền dữ liệu thời gian thực có khả năng mở rộng và độ bền cao. KDS có thể
liên tục thu thập gigabyte dữ liệu mỗi giây từ hàng trăm nghìn nguồn như luồng nhấp chuột trên trang web, luồng sự kiện
cơ sở dữ liệu, giao dịch tài chính, luồng dữ liệu từ mạng xã hội, nhật ký CNTT và sự kiện theo dõi vị trí. Dữ liệu được
thu thập sẽ có sẵn trong vài mili giây để hỗ trợ các trường hợp sử dụng phân tích thời gian thực như bảng điều khiển
thời gian thực, phát hiện bất thường thời gian thực, định giá động, và nhiều hơn nữa.

## Thuật ngữ

Bạn có thể sử dụng Amazon Kinesis Data Streams để nhập lượng lớn dữ liệu theo thời gian thực, lưu trữ dữ liệu bền vững
và cung cấp dữ liệu cho việc tiêu thụ. Đơn vị dữ liệu được lưu trữ bởi Kinesis Data Streams là một bản ghi dữ liệu. Một
luồng dữ liệu đại diện cho một nhóm các bản ghi dữ liệu. Các bản ghi dữ liệu trong một luồng dữ liệu được phân phối vào
các mảnh.

Một mảnh có một chuỗi các bản ghi dữ liệu trong một luồng. Khi bạn tạo một luồng, bạn xác định số lượng mảnh cho luồng
đó. Tổng công suất của một luồng là tổng của công suất các mảnh. Bạn có thể tăng hoặc giảm số lượng mảnh trong một luồng
khi cần thiết. Tuy nhiên, bạn sẽ bị tính phí theo từng mảnh. Để biết thêm thông tin về công suất và giới hạn của một
mảnh, hãy xem Giới hạn Kinesis Data Streams.

Một nhà sản xuất đặt bản ghi dữ liệu vào các mảnh và một người tiêu thụ lấy bản ghi dữ liệu từ các mảnh.

## Kiến trúc của Kinesis Data Streams

Sơ đồ sau đây minh họa kiến trúc của Kinesis Data Streams. Các nhà sản xuất đặt các bản ghi vào luồng dữ liệu,
và người tiêu thụ xử lý dữ liệu theo thời gian thực. Người tiêu thụ (như ứng dụng tùy chỉnh chạy trên Amazon EC2 hoặc
một luồng chuyển phát Amazon Kinesis Data Firehose) có thể lưu trữ kết quả của họ bằng một dịch vụ AWS như Amazon
DynamoDB, Amazon Redshift hoặc Amazon S3.

![Image](/repo_pmt_ws-fcj-005/images/1/1-001.png?featherlight=false&width=90pc)

Kiến trúc Kinesis

## Gửi dữ liệu tới Amazon Kinesis Data Streams

Có nhiều cơ chế để gửi dữ liệu của bạn tới luồng. Bạn có thể xây dựng các nhà sản xuất cho Kinesis Data Streams bằng
cách sử dụng AWS SDK Data Streams API, Kinesis Producer Library (KPL) và Kinesis Agent.

### Amazon Kinesis Data Streams API

Amazon Kinesis Data Streams cung cấp hai API để đưa dữ liệu vào một luồng Amazon Kinesis: PutRecord và PutRecords.
PutRecord cho phép ghi một bản ghi dữ liệu duy nhất trong một lệnh API và PutRecords cho phép nhiều bản ghi trong một
lệnh API.

### Thư viện Kinesis Producer Library (KPL)

Amazon Kinesis Producer Library (KPL) là một thư viện dễ sử dụng và có thể cấu hình cao giúp bạn đặt dữ liệu vào luồng
dữ liệu Amazon Kinesis. KPL cung cấp một giao diện đơn giản, không đồng bộ và đáng tin cậy, cho phép bạn nhanh chóng đạt
được hiệu suất sản xuất cao với tài nguyên máy khách tối thiểu.

### Agent Amazon Kinesis

Agent Amazon Kinesis là một ứng dụng Java dựng sẵn cung cấp cách dễ dàng để thu thập và gửi dữ liệu đến luồng Amazon
Kinesis của bạn. Bạn có thể cài đặt agent trên các môi trường máy chủ dựa trên Linux như máy chủ web, máy chủ nhật ký và
máy chủ cơ sở dữ liệu. Agent giám sát các tệp nhất định và liên tục gửi dữ liệu đến luồng của bạn.

## Xử lý dữ liệu từ Amazon Kinesis Data Streams

Bạn có thể nhanh chóng xây dựng các ứng dụng bằng cách sử dụng AWS SDK, Thư viện Kinesis Client Library (KCL), các kết
nối, và agent. Bạn cũng có thể dễ dàng xử lý dữ liệu với các tích hợp sẵn có cho AWS Lambda, Amazon Kinesis Data
Analytics, Amazon Kinesis Data Firehose và AWS Glue Schema Registry.

### Amazon Data Firehose

Amazon Data Firehose là cách dễ dàng nhất để chuyển đổi và tải dữ liệu thời gian thực một cách đáng tin cậy vào các kho
dữ liệu và công cụ phân tích. Bạn có thể sử dụng luồng dữ liệu Kinesis làm nguồn cho một luồng chuyển phát Kinesis Data
Firehose.

### Dịch vụ Quản lý Amazon cho Apache Flink

Dịch vụ Quản lý Amazon cho Apache Flink là cách dễ nhất để chuyển đổi và phân tích dữ liệu thời gian thực với Apache
Flink. Apache Flink là một khung và công cụ xử lý luồng dữ liệu mã nguồn mở. Bạn có thể sử dụng Kinesis Data Analytics
để dễ dàng chuyển đổi và phân tích dữ liệu thời gian thực.

Dịch vụ Quản lý Amazon cho Apache Flink tích hợp với Amazon MSK, Amazon Kinesis Data Streams, Dịch vụ Amazon OpenSearch,
luồng dữ liệu Amazon DynamoDB, Dịch vụ Lưu trữ Đơn giản Amazon (Amazon S3), tích hợp tùy chỉnh và nhiều hơn nữa sử dụng
các kết nối sẵn có. Bạn có thể sử dụng luồng dữ liệu Kinesis làm nguồn và đích cho một ứng dụng phân tích dữ liệu
Kinesis.

Ngoài ra, bạn có thể sử dụng Amazon Managed Service for Apache Flink Studio để tương tác với dữ liệu thời gian thực bằng
SQL, Python và Scala.

### AWS Lambda

Bạn có thể đăng ký các hàm Lambda để tự động đọc các bản ghi khỏi luồng dữ liệu Kinesis của bạn. AWS Lambda thường được
sử dụng cho xử lý luồng dữ liệu theo từng bản ghi (còn gọi là xử lý sự kiện).

### Thư viện Kinesis Client Library (KCL)

Thư viện Kinesis Client Library (KCL) là một thư viện dựng sẵn giúp bạn dễ dàng xây dựng các ứng dụng Amazon Kinesis để
đọc và xử lý dữ liệu từ một luồng dữ liệu Amazon Kinesis. KCL xử lý các vấn đề phức tạp như thích ứng với thay đổi trong
khối lượng luồng, cân bằng tải dữ liệu luồng, phối hợp các dịch vụ phân tán và xử lý dữ liệu với độ tin cậy cao. KCL cho
phép bạn tập trung vào logic kinh doanh khi xây dựng các ứng dụng Amazon Kinesis. Bắt đầu với KCL 2.0, bạn có thể sử
dụng API luồng HTTP/2 với độ trễ thấp và tăng cường fan-out để truy xuất dữ liệu từ một luồng.

Phiên bản hiện tại của thư viện này cung cấp các kết nối tới Amazon DynamoDB, Amazon Redshift, Amazon S3 và Amazon
OpenSearch Service. Thư viện cũng bao gồm các kết nối mẫu của mỗi loại, cùng với các tệp xây dựng Apache Ant để chạy các
mẫu.
