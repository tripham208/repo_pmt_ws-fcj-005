---
title: "Làm sạch, Tổng hợp và Làm giàu Sự kiện với Amazon Managed Service for Apache Flink"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4.  </b> "
---

Trong phần này, bạn sẽ học cách kết nối Amazon Managed Service for Apache Flink Studio với luồng hiện tại của mình
và làm sạch, tổng hợp, và làm giàu các sự kiện đến.

![Image](/repo_pmt_ws-fcj-005/images/4/4-001.png?featherlight=false&width=90pc)

Chúng ta sử dụng một kịch bản để phân tích dữ liệu telemetry của một đội xe taxi ở New York City gần như theo thời gian
thực để tối ưu hóa hoạt động của đội xe.
Trong kịch bản này, mỗi chiếc taxi trong đội xe đều thu thập thông tin về các chuyến đi đã hoàn thành. Thông tin được
theo dõi bao gồm vị trí đón và thả khách, số lượng hành khách. Thông tin này được nhập vào một luồng dữ liệu Kinesis
dưới dạng một JSON blob đơn giản. Từ đó, dữ liệu được xử lý bởi một ứng dụng Flink, được triển khai vào Kinesis Data
Analytics for SQL trong Managed Service for Apache Flink Studio. Ứng dụng này xác định các khu vực hiện đang yêu cầu số
lượng chuyến đi taxi cao. Các thông tin chi tiết được suy ra cuối cùng được lưu trữ trong Amazon OpenSearch, nơi chúng
có thể được truy cập và trực quan hóa bằng cách sử dụng [OpenSearch](https://aws.amazon.com/vi/what-is/elk-stack/)
Dashboard.

![Image](/repo_pmt_ws-fcj-005/images/4/4-002.png?featherlight=false&width=90pc)
