---
title: "Lambda với Amazon Data Firehose"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong phần này, chúng ta sẽ tạo một Kinesis Data Stream và tích hợp với Amazon Kinesis Data Firehose delivery stream để
ghi vào một S3 bucket. Chúng ta cũng sẽ tạo một hàm Lambda để chuyển đổi dữ liệu như thêm nguồn của
dữ liệu trong các sự kiện đến và sau đó gửi dữ liệu đã chuyển đổi đến Firehose Delivery Stream của chúng ta.

![Image](/repo_pmt_ws-fcj-005/images/3/3-001.png?featherlight=false&width=90pc)

Amazon Kinesis Data Firehose là cách dễ nhất để tải dữ liệu streaming vào các kho dữ liệu và công cụ phân tích. Nó có
thể thu thập, chuyển đổi và tải dữ liệu streaming vào Amazon S3, Amazon Redshift, Amazon Elasticsearch Service và
Splunk,
cho phép phân tích gần như theo thời gian thực với các công cụ và bảng điều khiển trí tuệ kinh doanh hiện tại mà bạn
đang sử dụng.
Nó là một dịch vụ được quản lý hoàn toàn tự động mở rộng để phù hợp với thông lượng của dữ liệu của bạn và không yêu cầu
quản trị liên tục.
Nó cũng có thể gói, nén và mã hóa dữ liệu trước khi tải, tối thiểu hóa lượng lưu trữ được sử dụng tại điểm đích và tăng
cường bảo mật.

Chúng ta cấu hình Kinesis Firehose Delivery stream của mình để ghi dữ liệu vào S3 bằng định dạng tệp parquet. Các tệp
Parquet có
một schema được định nghĩa, vì vậy như một phần của việc tạo các tài nguyên cho bài lab này, chúng ta sẽ định nghĩa
schema cho tập dữ liệu chuyến đi taxi của mình
sử dụng Glue/Athena (cũng làm cho việc truy vấn dữ liệu dễ dàng hơn khi Firehouse chuyển nó đến S3).
