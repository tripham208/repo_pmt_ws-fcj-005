---
title: "Khởi chạy tài nguyên"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 1.2 </b> "
---

Như đã thảo luận, chúng tôi sẽ khởi chạy một số tài nguyên cho workshop này thay bạn để đơn giản hóa các ví dụ.

Nếu bạn tự mình thực hiện workshop này, hãy tải xuống [Mẫu CF](repo_pmt_ws-fcj-005/resources/kinesis-immersion-day-cfn.yaml) này để bắt đầu.
Mẫu CloudFormation này sẽ khởi chạy các tài nguyên sau (tương ứng với kiến trúc dưới đây)

* Một Virtual Private Cloud (VPC) với các nhóm bảo mật tương ứng cho Instance Cloud9 của bạn.
* Một Instance Cloud9 để chạy Kinesis Client Library và khám phá mã
* Hai S3 bucket để lưu trữ dữ liệu gốc của bạn và chấp nhận đầu ra của dữ liệu đã được quản lý trong phòng lab Kinesis Data Firehose
* Một ứng dụng Kinesis Data Analytics Studio và một Glue Database liên quan
* Một hàm Lambda để xử lý dữ liệu từ Kinesis Data Streams

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-001.png?featherlight=false&width=90pc)

### Tải lên các tệp Artifact

* Tạo [S3](https://us-east-1.console.aws.amazon.com/s3/buckets?region=us-east-1&bucketType=general) bucket

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-002.png?featherlight=false&width=90pc)
* Tải lên tệp jar

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-003.png?featherlight=false&width=90pc)

### Tạo vai trò IAM cho CloudFormation

* Tạo [vai trò IAM](https://us-east-1.console.aws.amazon.com/iam/home?region=us-east-1#/roles)

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-004.png?featherlight=false&width=90pc)

### CloudFormation

* Điều này có thể đạt được bằng cách tìm kiếm CloudFormation ở đầu trang hoặc tìm nó trong nút ▼Services ở góc trên bên trái.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-005.png?featherlight=false&width=90pc)

* Trên trang CloudFormation, hãy nhấp vào Create Stack with new resources, và tải lên tệp CloudFormation .yml của chúng ta.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-006.png?featherlight=false&width=90pc)

* Trên trang mở ra, chọn nút **Upload a Template File**, và Chọn tệp để tải lên.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-007.png?featherlight=false&width=90pc)

* Tải lên tệp và nhấp vào **Next**.

  Đặt tên cho mẫu workshop, như **kinesis-ws** để dễ nhớ và cập nhật Tham số **input-stream** nếu bạn đã tạo một luồng với tên khác. Nhấp vào **Next** một lần nữa.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-008.png?featherlight=false&width=90pc)

* Chọn vai trò IAM của bạn. Trang thứ ba cho phép thêm Tags. Thêm các thẻ này nếu bạn muốn và nhấp vào **Next**.

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-009.png?featherlight=false&width=90pc)

* Ở cuối trang cuối cùng, chọn các hộp cho phép tạo các tài nguyên IAM và mở rộng tự động mẫu CloudFormation. Sau đó, **Create Stack!**

  ![Image](/repo_pmt_ws-fcj-005/images/1/2/12-010.png?featherlight=false&width=90pc)
