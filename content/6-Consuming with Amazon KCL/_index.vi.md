---
title: "Tiêu thụ với Amazon KCL"
date: "`r Sys.Date()`"
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

Sau khi dữ liệu đã được lưu trữ trong Amazon Kinesis, bạn có thể tiêu thụ và xử lý dữ liệu bằng
[Kinesis Client Library](https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html) để
phân tích dữ liệu, lưu trữ, bảng điều khiển theo thời gian thực và nhiều hơn nữa. Mặc dù bạn có thể sử dụng các hàm API
Amazon Kinesis để xử lý
dữ liệu trực tiếp, KCL xử lý nhiều tác vụ phức tạp liên quan đến xử lý phân tán và cho phép bạn
tập trung vào logic xử lý bản ghi. Ví dụ, KCL có thể tự động cân bằng tải xử lý bản ghi trên nhiều
instance, cho phép người dùng checkpoint các bản ghi đã xử lý và xử lý lỗi instance. KCL đóng vai trò
là trung gian giữa logic xử lý bản ghi của bạn và Kinesis Data Streams. KCL thực hiện các tác vụ sau:

* Kết nối với luồng dữ liệu
* Liệt kê các shard trong luồng dữ liệu
* Sử dụng các lease để phối hợp các liên kết shard với các worker của nó
* Khởi tạo một bộ xử lý bản ghi cho mỗi shard mà nó quản lý
* Lấy dữ liệu bản ghi từ luồng dữ liệu
* Đẩy các bản ghi tới bộ xử lý bản ghi tương ứng
* Checkpoint các bản ghi đã xử lý
* Cân bằng các liên kết shard-worker (lease) khi số lượng instance worker thay đổi hoặc khi luồng dữ liệu được
* reshard (các shard bị chia nhỏ hoặc hợp nhất)

Được khuyến nghị sử dụng phiên bản 2 của KCL. Bạn có thể sử
dụng [KCL 2.x](https://docs.aws.amazon.com/streams/latest/dev/developing-consumers-with-kcl-v2.html) để xây dựng các ứng
dụng consumer tùy chỉnh của mình. Nó hỗ trợ các consumer không phải EFO và làm cho việc chuyển đổi dễ dàng hơn. Nếu bạn
có khách hàng đang sử dụng KCL 1.x, họ có thể làm theo hướng dẫn về cách di chuyển từ KCL 1.x sang KCL 2.x, xem
Migrating Consumers from KCL 1.x to KCL 2.x.

## Các khái niệm KCL

* **Ứng dụng tiêu thụ KCL** – một ứng dụng được xây dựng tùy chỉnh bằng KCL và được thiết kế để đọc và xử lý các bản ghi
  từ
  các luồng dữ liệu. Instance ứng dụng tiêu thụ - Các ứng dụng tiêu thụ KCL thường được phân tán, với một hoặc nhiều
  instance ứng dụng chạy đồng thời để phối hợp xử lý lỗi và cân bằng tải động xử lý bản ghi dữ liệu.
* **Worker** – một lớp cao cấp mà một instance ứng dụng tiêu thụ KCL sử dụng để bắt đầu xử lý dữ liệu.
* **Lease** – dữ liệu xác định liên kết giữa một worker và một shard. Các ứng dụng tiêu thụ KCL phân tán sử dụng các
  lease để chia sẻ xử lý bản ghi dữ liệu giữa một đội ngũ worker. Tại bất kỳ thời điểm nào, mỗi shard của dữ liệu được
  liên kết với một worker cụ thể bởi một lease được xác định bởi biến leaseKey. Theo mặc định, một worker có thể giữ một
  hoặc nhiều lease (tuỳ thuộc vào giá trị của biến maxLeasesForWorker) cùng một lúc.
* **Bảng lease** - một bảng Amazon DynamoDB duy nhất được sử dụng để theo dõi các shard trong luồng dữ liệu KDS đang
  được lease và xử lý bởi các worker của ứng dụng tiêu thụ KCL. Bảng lease phải được đồng bộ (trong một worker và trên
  tất cả các worker) với thông tin shard mới nhất từ luồng dữ liệu trong khi ứng dụng tiêu thụ KCL đang chạy. Để biết
  thêm thông tin, hãy
  xem [Sử dụng Bảng Lease để Theo dõi các Shard được Xử lý bởi Ứng dụng Tiêu thụ KCL](https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html#shared-throughput-kcl-consumers-leasetable).
* **Bộ xử lý bản ghi** – logic xác định cách ứng dụng tiêu thụ KCL của bạn xử lý dữ liệu mà nó nhận được từ các luồng dữ
  liệu. Tại thời điểm chạy, một instance ứng dụng tiêu thụ KCL khởi tạo một worker, và worker này khởi tạo một bộ xử lý
  bản ghi cho mỗi shard mà nó giữ một lease.

## Xử lý luồng bằng ứng dụng KCL

Phòng thí nghiệm này cho bạn thấy cách sử dụng các consumer tùy chỉnh - cụ thể là các consumer fan-out được cải thiện -
sử dụng
KCL. [Các consumer fan-out được cải thiện](https://docs.aws.amazon.com/streams/latest/dev/enhanced-consumers.html) có
thông lượng dành riêng 2 MB/s và sử dụng mô hình đẩy thay vì kéo để lấy dữ liệu. Các bản ghi được đẩy tới consumer từ
các shard của Kinesis Data Streams sử dụng HTTP/2 Server Push, cũng giảm độ trễ cho việc xử lý bản ghi. Nếu bạn có nhiều
hơn một instance của một consumer, mỗi instance có một ống fan-out 2 MB/s tới mỗi shard độc lập với bất kỳ consumer nào
khác. Bạn có thể sử dụng các consumer fan-out được cải thiện với AWS SDK hoặc KCL.

KCL là một thư viện Java nhưng cũng hỗ trợ các ngôn ngữ khác thông qua MultiLangDaemon. MultiLangDaemon sử dụng STDIN và
STDOUT để giao tiếp với bộ xử lý bản ghi, được triển khai bằng ngôn ngữ mong muốn, trong khi phần còn lại của ứng dụng
KCL là Java. Trong Phòng thí nghiệm này, chúng tôi sử dụng phiên bản Java nhưng bạn có thể tìm thấy một mẫu Python trên
GitHub. Bắt đầu từ Phiên bản 2.3 KCL hỗ trợ xử lý nhiều luồng trong cùng một ứng dụng.

KCL tạo một bảng Amazon [DynamoDB](https://aws.amazon.com/vi/dynamodb/) để theo dõi tiến trình của consumer. Ví dụ, nếu
luồng của bạn có bốn shard và bạn có một instance producer, instance của bạn sẽ chạy một bộ xử lý bản ghi riêng cho mỗi
shard. Nếu consumer mở rộng thành hai instance, KCL sẽ cân bằng lại bộ xử lý bản ghi và chạy hai bộ xử lý bản ghi trên
mỗi instance. Để biết thêm thông tin, hãy xem Sử
dụng [Thư viện Client Kinesis](https://docs.aws.amazon.com/streams/latest/dev/shared-throughput-kcl-consumers.html#shared-throughput-kcl-consumers-leasetable).

### Chuẩn bị

Nếu bạn chưa làm vậy, hãy tải xuống các ví dụ về Thư viện Client Kinesis
tại [đây](/repo_pmt_ws-fcj-005/resources/6/kinesis-immersionday-kcl-main.zip) và giải nén chúng trên máy tính của bạn.

Sau khi giải nén, hãy chú ý quay lại instance Cloud9 mà chúng ta đã làm việc trước đó.

* Trên Cloud9 IDE, ở góc trên bên trái, nhấp vào File -> Upload Local Files... -> Select Folder -> Locate the unzipped
  root of the examples.
* Thư mục gốc nên được gọi là kinesis-immersionday-kcl-main.
* Chọn Upload. Bạn sẽ thấy dự án ở phía bên trái của Cloud9 IDE.

![Image](/repo_pmt_ws-fcj-005/images/6/6-002.png?featherlight=false&width=90pc)

### Chạy KCL

Sau khi mã đã được tải lên thành công, hãy mở thư mục sau: kcl-app

Ở đây bạn sẽ tìm thấy mã nguồn cho ứng dụng KCL của mình. Để kiểm tra mã, mở tệp
`src/main/java/com/amazon/kinesis/immersionday/App.java.`

Ở dòng 138, bạn sẽ tìm thấy phương thức processRecords triển khai logic xử lý bản ghi.

![Image](/repo_pmt_ws-fcj-005/images/6/6-003.png?featherlight=false&width=90pc)

Trong mẫu của chúng tôi, chúng tôi chỉ ghi lại khóa phân vùng và dữ liệu cho mỗi bản ghi vào STD OUT.

Để xây dựng ứng dụng, hãy điều hướng đến thư mục kcl-app và chạy các lệnh sau:

```
sudo yum install maven -y
mvn clean compile assembly\:single
```

![Image](/repo_pmt_ws-fcj-005/images/6/6-004.png?featherlight=false&width=90pc)
Sau khi xây dựng xong, bạn cần đặt các thông số cấu hình cho KCL. Trong mẫu của chúng tôi, chúng tôi đang sử dụng
biến môi trường cho việc này:

```
export STREAM_NAME=<your stream name>
export AWS_REGION=us-east-1
export APPLICATION_NAME=ImmersiondayKCLConsumer
```

Điều chỉnh các thông số export này theo nhu cầu
Bây giờ bạn có thể chạy ứng dụng của mình bằng cách thực thi tệp JAR đã xây dựng trước đó:

`java -jar target/kcl-app-1.0-SNAPSHOT-jar-with-dependencies.jar`

![Image](/repo_pmt_ws-fcj-005/images/6/6-005.png?featherlight=false&width=90pc)

Bạn có thể thấy trong nhật ký rằng KCL đã tạo một LeaseTable với khả năng đọc và ghi là 10. Sau đó, KCL
đã tạo các lease và Enhanced Fan-Out Consumer với tên được định nghĩa trước đó. Bạn cũng thấy một nhật ký "Sleeping ..."
vì hiện tại không có bản ghi nào được xử lý.

Sau khi bạn khởi động lại việc nhập dữ liệu (được mô tả trong [Lab 1](2-Produce-data-to-Kinesis-Data-Streams/)), bạn sẽ
thấy các bản ghi được ghi lại:

![Image](/repo_pmt_ws-fcj-005/images/6/6-006.png?featherlight=false&width=90pc)

### Kết quả KCL

Chuyển sang [Bảng điều khiển DynamoDB](https://us-west-2.console.aws.amazon.com/dynamodbv2/home?region=us-west-2#tables)
bạn sẽ thấy một bảng có tên là ImmersiondayKCLConsumer, đây là bảng lease cho
ứng dụng KCL.

![Image](/repo_pmt_ws-fcj-005/images/6/6-007.png?featherlight=false&width=90pc)

Khi bạn chạy KCL, mặc định nó sẽ xuất bản các số liệu
đến [Amazon Cloudwatch](https://us-west-2.signin.aws.amazon.com/oauth?client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fcloudwatch&code_challenge=5j7GJeOepk0Wai04gTKLJq7wtDwBmj3yFS3kaZngz8Y&code_challenge_method=SHA-256&redirect_uri=https%3A%2F%2Fus-west-2.console.aws.amazon.com%2Fcloudwatch%2Fhome%3Fregion%3Dus-west-2%26state%3DhashArgs%2523metricsV2%253Agraph%253D~%2528%2529%26ca-oauth-flow-id%3DHqZt%26isauthcode%3Dtrue&response_type=code).
Điều hướng đến Cloudwatch Metrics và
chọn namespace ImmersiondayKCLConsumer. Bạn sẽ thấy các số liệu KCL-Application, Worker và Shard. Để biết thêm
thông tin về việc giám sát KCL, hãy tham khảo tài liệu.

Nếu bạn thấy lỗi "The security token included in the request is expired", hãy dừng ứng dụng bằng cách nhấn
CTRL+C và khởi động lại.
