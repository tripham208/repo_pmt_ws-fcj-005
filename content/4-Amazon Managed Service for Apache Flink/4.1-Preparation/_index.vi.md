---
title: "(Tùy chọn) Thiết lập Kinesis Data Analytics Studio"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 4.1 </b> "
---

{{% notice info %}}
Đây là một phần tùy chọn. CFN đã cung cấp sẵn một Kinesis Analytics notebook có tên là KDA-Studio-1. Bạn có thể bỏ qua
phần này nếu bạn muốn tái sử dụng notebook đã được cung cấp. Bạn có thể tiếp tục phần này trong trường hợp bạn muốn học cách
thiết lập Kinesis Data Analytics Studio từ bảng điều khiển AWS.
Điều hướng đến dịch vụ Amazon Kinesis Analytics và chọn tab Studio. Chọn **Create studio notebook**.
{{% /notice %}}

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-001.png?featherlight=false&width=90pc)

Chọn **Create with custom settings**. Cung cấp tên notebook studio.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-002.png?featherlight=false&width=90pc)

Chọn tùy chọn Choose from **IAM roles that Kinesis Data Analytics can assume** và chọn **kinesis-analytics-KDA-***
role từ danh sách thả xuống. Chọn cơ sở dữ liệu kinesislab từ danh sách thả xuống cho AWS Glue database.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-003.png?featherlight=false&width=90pc)

Chọn **Add custom connector**.

Tải về máy tính của bạn
liên kết [Elasticsearch connector](https://mvnrepository.com/artifact/org.apache.flink/flink-sql-connector-elasticsearch7)
jar phù hợp với phiên bản Kinesis Data Analytics Studio Flink của bạn--trong ví dụ của chúng tôi là v1.13.2.
Tải tệp này lên một Amazon S3 Bucket với quyền cho ứng dụng Kinesis Data Analytics Studio của bạn.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-004.png?featherlight=false&width=90pc)
Chọn S3 bucket và đường dẫn bạn đã tải lên tệp sql connector jar. Kiểm tra phần đầu ra CFN để tìm
giá trị S3BucketForConnector. Đây sẽ là S3 bucket. Cung cấp tên tệp **flink-sql-connector-elasticsearch7_2.11-1.**13.2
trong Đường dẫn đối tượng S3.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-005.png?featherlight=false&width=90pc)

Chọn **Next**.

Chọn **Create studio notebook**.

![Image](/repo_pmt_ws-fcj-005/images/4/1/41-006.png?featherlight=false&width=90pc)
