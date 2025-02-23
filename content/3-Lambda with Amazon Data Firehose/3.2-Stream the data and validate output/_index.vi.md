---
title: "Luồng dữ liệu và xác nhận đầu ra"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

Bây giờ, hãy kiểm tra luồng và xác nhận đầu ra.

Bắt đầu thu thập dữ liệu vào Kinesis Data Stream

{{% notice note %}}
Mẹo: Kiểm tra và/hoặc bắt đầu [chương trình khách hàng Producer ngay bây giờ](2-Produce-data-to-Kinesis-Data-Streams/). Bạn có thể sử dụng SDK hoặc nhà sản xuất KPL - cả hai sẽ cho
kết quả tương tự.
Khi Producer bắt đầu thu thập dữ liệu vào Kinesis Stream, luồng cung cấp Amazon Data Firehose bạn tạo sẽ
thu thập các bản ghi, đệm nó, chuyển đổi bằng cách thêm cột mới và cung cấp nó đến điểm đích S3 theo tiền tố
được cung cấp. Bộ đệm được đặt thành 1 phút hoặc 128 MB, tùy điều kiện nào đến trước. Có thể mất 1 phút trước khi bạn thấy dữ liệu trong S3.
{{% /notice %}}

Đợi một vài phút

Điều hướng đến bảng điều khiển Kinesis và nhấp vào luồng cung cấp dữ liệu “nyc-taxi-trips” Data Firehose

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-001.png?featherlight=false&width=90pc)

Nhấp vào tab Monitoring và bạn sẽ bắt đầu thấy một số số liệu từ luồng cung cấp dữ liệu Firehose của bạn

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-002.png?featherlight=false&width=90pc)

Nhấp vào tab Configuration. Cuộn xuống phần cài đặt Destination. Sau đó nhấp vào liên kết S3 bucket.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-003.png?featherlight=false&width=90pc)

Nếu đã là 1 phút, bạn sẽ thấy một số dữ liệu trong S3 bucket của bạn. Nếu không, vui lòng đợi thêm một chút. Điều hướng vào
thư mục nyctaxitrips và qua các thư mục con cho đến khi bạn đến các tệp dữ liệu ở định dạng parquet.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-004.png?featherlight=false&width=90pc)

Chọn một trong các tệp parquet. Sau đó, dưới mục Actions, chọn Select Object Actions sau đó Query with S3 Select.

Giữ Apache Parquet được chọn cho Input settings. Chọn CSV cho Output Settings, sau đó chọn Run SQL Query.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-005.png?featherlight=false&width=90pc)

Quan sát rằng bạn có thể thấy dữ liệu chuyến đi taxi của mình trong tệp parquet trong S3. Amazon Data Firehose đã hoàn thành công việc của mình.
Bây giờ điều hướng đến [bảng điều khiển Athena](https://us-east-1.console.aws.amazon.com/athena/home?region=us-east-1).

Dưới Databases, chọn cơ sở dữ liệu kinesislab. Sau đó nhấp vào dấu chấm đứng (⋮) bên phải của bảng
nyctaxitrips và chọn Load partitions.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-006.png?featherlight=false&width=90pc)

Bây giờ nhấp vào ... đứng bên phải của bảng nyctaxitrips và chọn Preview table.

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-007.png?featherlight=false&width=90pc)

Điều này sẽ khởi chạy một truy vấn SQL đơn giản, khi hoàn thành sẽ trông như sau:

![Image](/repo_pmt_ws-fcj-005/images/3/2/32-008.png?featherlight=false&width=90pc)

Nếu bạn không thấy dữ liệu, vui lòng thực hiện lại bước “Load partitions”.
Quan sát rằng bạn có thể truy vấn dữ liệu chuyến đi taxi của mình qua SQL bằng cách sử dụng Athena.
