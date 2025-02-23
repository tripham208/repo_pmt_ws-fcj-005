---
title: "Luồng dữ liệu và xác nhận đầu ra"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2 </b> "
---

Điều hướng đến dịch vụ Amazon Kinesis Analytics và chọn tab Studio. Chọn notebook **KDA-Studio-1**. Chọn **Run**.
Chọn **Open in Apache Zeppelin.**

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-001.png?featherlight=false&width=90pc)

Chọn Import note để nhập notebook [KDA-OpenSearch.zpln](/repo_pmt_ws-fcj-005/resources/4/KDA-OpenSearch.zpln).

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-002.png?featherlight=false&width=90pc)

Kiểm tra giá trị **OpenSearchSecretsManagerReference** trong phần tài nguyên của CloudFormation stack và ghi lại.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-003.png?featherlight=false&width=90pc)

Điều hướng đến Amazon Secret Manager và chọn tên bí mật bạn đã ghi lại ở bước trước.
Chọn **Retrieve secret value**. Ghi lại giá trị **username** và **password**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-004.png?featherlight=false&width=90pc)

Điều hướng đến Amazon OpenSearch Service. Chọn os-domain. Ghi lại giá trị **OpenSearch Dashboard URL** và **Domain
endpoint**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-005.png?featherlight=false&width=90pc)

Quay lại Zeppelin KDA-OpenSearch notebook và cuộn đến bước 3.
Thay thế các giá trị hosts, **username** và **password** bằng **Domain endpoint**, username và giá trị password ghi lại
ở bước trước. Đảm bảo hosts có định dạng **<Domain endpoint>:443**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-006.png?featherlight=false&width=90pc)

Thực thi hai ô đầu tiên trong notebook bằng cách nhấp vào **Run Icon** ở góc phải.

Bắt đầu thu thập dữ liệu vào Kinesis Data Stream
Mẹo: Kiểm tra và/hoặc bắt đầu chương trình khách hàng Producer ngay bây giờ. Đi
tới [Chạy chương trình khách hàng Producer](2-Produce-data-to-Kinesis-Data-Streams/) để tìm hiểu cách thực hiện điều
này.

Đợi vài giây và bạn sẽ thấy dữ liệu điền vào notebook.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-007.png?featherlight=false&width=90pc)

Bây giờ hãy xác nhận đầu ra trong OpenSearch

{{% notice note %}}
Đảm bảo bạn đã chạy các đoạn mã 3 và 4 trong notebook trước khi tiếp tục.
{{% /notice %}}
Mở một trình duyệt và nhập **OpenSearch Dashboard URL** bạn đã ghi lại ở bước trước. Cung cấp **username** và **password
** bạn đã ghi lại ở bước trước.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-008.png?featherlight=false&width=90pc)

Trong bảng bên trái, chọn **Query Workbench**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-009.png?featherlight=false&width=90pc)

Nhập “**select * from trip_statistics**” vào Query editor và chọn **Run**. Bạn có thể thấy các bản ghi được tổng hợp
trong bảng kết quả.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-010.png?featherlight=false&width=90pc)

Bây giờ hãy xây dựng một bảng điều khiển. (Bạn cũng có thể bỏ qua các bước dưới đây bằng cách nhập bảng điều khiển. Xem
phần Import index pattern và Dashboards ở cuối)

Chọn Stack Management dưới Management trong bảng bên trái.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-011.png?featherlight=false&width=90pc)

Chọn Create index pattern.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-012.png?featherlight=false&width=90pc)

Cung cấp trip_statistics làm **Index pattern name** và nhấp vào Next.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-013.png?featherlight=false&width=90pc)

Chọn **Create index pattern**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-014.png?featherlight=false&width=90pc)

Bạn sẽ thấy danh sách các trường và kiểu dữ liệu liên quan.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-015.png?featherlight=false&width=90pc)

Chọn Visualize từ bảng bên trái.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-016.png?featherlight=false&width=90pc)

Chọn Create visualization. Chọn loại hình trực quan hóa. Ví dụ, hãy chọn Gauge.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-017.png?featherlight=false&width=90pc)

Chọn chỉ mục trip_statistics.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-018.png?featherlight=false&width=90pc)

Tạo một hình trực quan hóa với định nghĩa dưới đây.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-019.png?featherlight=false&width=90pc)

Lặp lại các bước từ bước 21 và tạo một hình trực quan hóa khác bằng cách chọn loại hình trực quan hóa Data Tables.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-020.png?featherlight=false&width=90pc)

Nhập index pattern và Dashboards (Tùy chọn)
Đây là phần tùy chọn. Bạn có thể nhập index và pattern và OpenSearch Dashboard nếu bạn muốn bỏ qua phát triển bảng điều
khiển OpenSearch.

Hãy tải xuống

* [trip_statistics_index_pattern](/repo_pmt_ws-fcj-005/resources/4/trip_statistics_index_pattern.ndjson)

* [trip_stats_dasboard](/repo_pmt_ws-fcj-005/resources/4/trip_stats_dasboard.ndjson)

* [avg_trip_count_dashboard](/repo_pmt_ws-fcj-005/resources/4/avg_trip_count_dashboard.ndjson)

Chọn Stack Management trong bảng bên trái.

Chọn Saved object. Chọn nút import. Bạn có thể nhập trip_statistics_index_pattern.ndjson,
avg_trip_count_dashboard.ndjson và trip_stats_dasboard.ndjson theo đúng thứ tự.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-021.png?featherlight=false&width=90pc)

Điều này sẽ nhập các đối tượng này vào tài khoản của bạn.
