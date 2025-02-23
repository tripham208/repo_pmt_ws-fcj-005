---
title: "Ghi Dữ liệu vào Kinesis Data Stream sử dụng Amazon Managed Service for Apache Flink Studio Notebook"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 2.4 </b> "
---

## Tổng quan

Trong phần này, chúng ta sẽ tìm hiểu các khái niệm về
[Studio Notebooks for Managed Service for Apache Flink](https://docs.aws.amazon.com/managed-flink/latest/java/how-notebook.html).

Studio Notebooks for Managed Service for Apache Flink cho phép bạn truy vấn các luồng dữ liệu một cách tương tác theo thời gian thực và dễ dàng xây dựng và chạy các ứng dụng xử lý luồng sử dụng SQL, Python và Scala tiêu chuẩn. Chỉ với vài lần nhấp chuột trong bảng điều khiển quản lý AWS, bạn có thể khởi chạy một notebook không cần máy chủ để truy vấn các luồng dữ liệu và nhận kết quả trong vài giây.

Một notebook là một môi trường phát triển dựa trên web. Với notebook, bạn có trải nghiệm phát triển tương tác đơn giản kết hợp với các khả năng tiên tiến do Apache Flink cung cấp. Studio notebooks sử dụng các notebook được hỗ trợ bởi Apache Zeppelin, và sử dụng Apache Flink làm công cụ xử lý luồng. Studio notebooks kết hợp mượt mà các công nghệ này để làm cho phân tích dữ liệu luồng tiên tiến dễ dàng tiếp cận với các nhà phát triển ở mọi trình độ kỹ năng.

Apache Zeppelin cung cấp cho các Studio notebooks của bạn một bộ công cụ phân tích hoàn chỉnh, bao gồm:

* Hình ảnh hóa dữ liệu

* Xuất dữ liệu ra file

* Kiểm soát định dạng đầu ra để phân tích dễ dàng hơn

Với một notebook, bạn mô hình hóa các truy vấn bằng cách sử dụng Apache Flink Table API & SQL trong SQL, Python, hoặc Scala, hoặc DataStream API trong Scala. Với vài lần nhấp chuột, bạn có thể thăng cấp notebook Studio thành một ứng dụng xử lý luồng Managed Service for Apache Flink không tương tác, chạy liên tục cho khối lượng công việc sản xuất của bạn.

Trong bài lab này, chúng ta sẽ sử dụng Zeppelin Notebook để đọc dữ liệu Taxi Ride từ S3 và chèn vào Kinesis Stream.

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-001.png?featherlight=false&width=90pc)

Trong Notebook của chúng ta, có các đoạn mã khác nhau để thực hiện các bước sau:

1. Tạo một bảng trong bộ nhớ cho dữ liệu đến từ S3 bucket.
2. Truy vấn dữ liệu từ S3.
3. Tạo một bảng trong bộ nhớ cho luồng kinesis đích.
4. Bắt đầu chèn dữ liệu từ S3 vào Kinesis Stream bằng cách sử dụng các bảng trong bộ nhớ.
5. Truy vấn bảng đích của luồng Kinesis để kiểm tra dữ liệu được chèn đúng cách.

## Chuẩn bị

Nếu bạn chưa chạy bài tập 1, hoặc bạn đã dọn dẹp, hãy tạo một Kinesis Data Stream và chạy mẫu CloudFormation để tạo các tài nguyên cần thiết cho bài lab này.

Tải lên tệp dữ liệu vào S3 bucket.

a. Để bắt đầu, hãy tải xuống [Tệp dữ liệu Taxi Ride](/repo_pmt_ws-fcj-005/resources/taxi-trips.csv) mà chúng ta sẽ sử dụng để tải vào Kinesis Stream.

b. Tải lên tệp dữ liệu đã tải xuống trước đó vào S3 bucket được tạo ra thông qua script CloudFormation thực thi trong phần bắt đầu. Bạn có thể tìm thấy bucket này trong tab Outputs của CloudFormation được gọi là `TaxiTripDataSet`.

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-002.png?featherlight=false&width=90pc)

## Chạy Studio Notebook

Từ bảng điều khiển Kinesis Console, tab Analytics Applications - Studio, hãy đảm bảo notebook đang chạy.

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-003.png?featherlight=false&width=90pc)

Hãy tải xuống tệp Zeppelin [Notebook](/repo_pmt_ws-fcj-005/resources/s3_to_kinesis.ipynb) mà chúng ta sẽ sử dụng để đọc và tải dữ liệu vào Kinesis Stream.

Nhập tệp Zeppelin Notebook đã tải xuống trước đó

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-004.png?featherlight=false&width=90pc)

Thực thi Notebook

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-005.png?featherlight=false&width=90pc)

Chạy từng đoạn mã từng bước để tải dữ liệu từ S3 vào Kinesis Stream

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-006.png?featherlight=false&width=90pc)

![Image](/repo_pmt_ws-fcj-005/images/2/4/24-007.png?featherlight=false&width=90pc)
