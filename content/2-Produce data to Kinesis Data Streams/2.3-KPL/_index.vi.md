---
title: "Sử dụng Thư viện Kinesis Producer"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 2.3 </b> "
---

## Thư viện Kinesis Producer (KPL)

Thư viện Kinesis Producer nâng cao khả năng thu thập dữ liệu đã được đề cập trong phần AWS SDK Kinesis API của workshop
này, cho phép các nhà phát triển đạt được thông lượng ghi cao hơn và tối ưu hơn với các lần thử lại và xử lý lỗi, sử
dụng các shard trong luồng dữ liệu đến khả năng tối đa mỗi giây.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-001.png?featherlight=false&width=90pc)

## Thư viện Kinesis Producer có thể làm gì?

Ghi vào một hoặc nhiều Kinesis Data Streams với các lần thử lại tự động và cấu hình.
Thu thập các bản ghi và sử dụng PutRecords API để ghi nhiều bản ghi vào nhiều shard mỗi yêu cầu.
Tổng hợp các bản ghi để tăng kích thước payload và thông lượng.*
Tích hợp với Kinesis Client Library (KCL) để tách lô các bản ghi ở phía tiêu thụ.
Tích hợp với Amazon CloudWatch metrics để cung cấp khả năng nhìn thấy vào hiệu suất của KPL.
KPL cung cấp một lớp trừu tượng trên các AWS SDK cho Kinesis.

## Sử dụng Shard

Một khía cạnh quan trọng cần lưu ý là khả năng của Thư viện Kinesis Producer để tổng hợp các bản ghi để tăng kích thước
payload và thông lượng cho từng shard riêng lẻ mà nó sản xuất.

Đối với một Kinesis Data Stream cụ thể, một ứng dụng có thể ghi 1MiB dữ liệu mỗi giây vào mỗi shard. Trong hầu hết các
trường hợp, một bản ghi riêng lẻ sẽ nhỏ hơn nhiều so với giới hạn 1MiB này, vào khoảng 10s kilobytes. Thư viện Kinesis
Producer sẽ đệm các bản ghi này để tối ưu thông lượng và chờ đợi một khoảng thời gian cấu hình hoặc lượng dữ liệu cho
đến khi thông lượng gần như 1MiB nhất có thể.

{{% notice note %}}
Không nên sử dụng Thư viện Kinesis Producer nếu ứng dụng của bạn yêu cầu độ trễ thấp. Điều này là do KPL có thể gây ra
sự chậm trễ xử lý bổ sung có thể cấu hình bởi RecordMaxBufferedTime.
{{% /notice %}}

# Một Nhà sản xuất Đơn giản

Nếu bạn chưa làm vậy, hãy tải xuống các ví dụ về Thư viện Kinesis Producer được tìm
thấy [tại đây](/repo_pmt_ws-fcj-005/resources/kinesis-producer-library-examples-master.zip) và giải nén chúng trên máy
của bạn.
Sau khi giải nén, hãy quay lại instance Cloud9 mà chúng ta đã làm việc trước đó.

* Trên Cloud9 IDE, ở góc trên bên trái, nhấp vào File -> Upload Local Files... -> Select Folder -> Locate the unzipped
  root of the examples.
* Thư mục gốc nên được gọi là `kinesis-producer-library-examples-master`.
* Chọn Upload. Bạn sau đó sẽ có thể thấy dự án ở phía bên trái của Cloud9 IDE của bạn.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-002.png?featherlight=false&width=90pc)

Chúng ta sẽ sử dụng mã này để tìm hiểu thêm về Thư viện Kinesis Producer.
Sau khi mã đã được tải lên thành công, hãy mở thư mục sau: `src/main/java/`

Ở đây, bạn sẽ tìm thấy các tệp A, B, C và D, làm nổi bật các cách khác nhau chúng ta có thể sử dụng Thư viện Kinesis
Producer. Mở A, hoặc A_SimpleProducer.java.

Trong tệp này, bạn sẽ tìm thấy một nhà xuất bản Thư viện Kinesis Producer cơ bản viết các chuyến đi Taxi vào Kinesis
Data Stream của chúng ta. So với phiên bản Amazon SDK của mã này, Thư viện Kinesis Producer đơn giản hóa đáng kể việc
thử lại, đóng gói và tối ưu hóa shard của các lần ghi vào Kinesis Data Stream.

`A_Simple_Producer.java`
Ở đầu tệp, sửa đổi cả biến streamName và region để khớp với tên luồng và khu vực của bạn.

Ở đầu hàm chính, chúng ta đang đọc dữ liệu chuyến đi taxi từ thư mục dữ liệu. Dữ liệu này sẽ được xuất bản lên Kinesis
Data Streams sau khi chúng ta tận dụng Thư viện Kinesis Producer để tổng hợp các bản ghi và sản xuất hiệu quả.

Hãy xem cấu hình Thư viện Kinesis Producer--

```javascript
KinesisProducerConfiguration config = new KinesisProducerConfiguration()
                                            .setRecordMaxBufferedTime(3000)
                                            .setMaxConnections(1)
                                            .setRequestTimeout(60000)
                                            .setRegion(region);
```

Trong mẫu mã này, chúng ta đang đặt một số thuộc tính cấu hình. Mở rộng từng cái để tìm hiểu thêm:

{{%expand "RecordMaxBufferedTime" %}}
RecordMaxBufferedTime: RecordMaxBufferedTime kiểm soát thời gian (mili giây) một bản ghi có thể trải qua trước khi được
sản xuất vào một hoặc nhiều Kinesis Data Streams. Các bản ghi có thể được sản xuất sớm hơn thời gian này tùy thuộc vào
các cài đặt khác mà chúng ta sẽ đề cập. Thời gian đệm cũng có thể bao gồm các lần thử lại và thất bại trong việc sản
xuất vào luồng.

Đặt RecordMaxBufferedTime quá thấp có thể ảnh hưởng xấu đến thông lượng.
Giá trị mặc định cho RecordMaxBufferedTime là 100 mili giây.
{{% /expand%}}

{{%expand "MaxConnections" %}}
MaxConnections: Số lượng kết nối tối đa để mở tới backend. Các yêu cầu HTTP đến Kinesis Data Streams sẽ được gửi song
song qua nhiều kết nối.

Đặt giá trị này quá cao có thể ảnh hưởng đến độ trễ và tiêu tốn thêm tài nguyên trong ứng dụng sản xuất mà không tăng
thông lượng.
Giá trị mặc định cho MaxConnections là 24.
{{% /expand%}}

{{%expand "RequestTimeout" %}}
RequestTimeout: Thời gian tối đa (mili giây) giữa khi một yêu cầu HTTP được gửi và nhận tất cả các phản hồi từ các tin
nhắn đã gửi. Nếu thời gian chờ yêu cầu vượt quá giá trị này, yêu cầu sẽ được coi là hết thời gian chờ.

Một bản ghi hết thời gian chờ có thể vẫn đã được xuất bản thành công lên Kinesis Data Streams, và việc thử lại có thể
dẫn đến các bản ghi trùng lặp. Vì vậy, bằng cách đặt giá trị này quá thấp, một luồng dữ liệu tăng khả năng các bản ghi
trùng lặp.
Giá trị mặc định cho RequestTimeout là 6000 mili giây.
{{% /expand%}}

### Xuất bản các bản ghi vào Kinesis Data Stream của bạn

Sau khi chúng ta đã đặt cấu hình và gán nó cho đối tượng Kinesis Producer:

`final KinesisProducer kinesis = new KinesisProducer(config);`

chúng ta có thể bắt đầu xuất bản dữ liệu. Gọi hàm .addUserRecord() với một blob dữ liệu. Một Bản ghi Người dùng được
phân biệt với một bản ghi Kinesis Data Stream--

Thuật ngữ bản ghi đề cập đến một bản ghi Thư viện Kinesis Producer, hoặc một blob dữ liệu có ý nghĩa đối với người dùng.
Một sự kiện clickstream, một giao dịch tài chính, v.v.

Một bản ghi Kinesis Data Stream là một instance của cấu trúc dữ liệu Bản ghi, được định nghĩa bởi Kinesis Data Streams
API, chứa một khóa phân vùng, số thứ tự và một blob dữ liệu.

### Chạy chương trình

Trong instance Cloud9 của bạn, điều hướng đến Window -> New Terminal để mở một cửa sổ terminal mới.

Đảm bảo bạn đang trong thư mục gốc của dự án trước khi thực hiện lệnh này: cùng cấp thư mục với tệp pom.xml.

`cd kinesis-producer-library-examples-master/`

Chúng ta bây giờ sẽ thực thi chương trình bằng cách gõ lệnh sau trong cửa sổ terminal:

### Lệnh Build Maven:

`mvn clean compile package`

Điều này sẽ biên dịch và đóng gói một jar chứa tất cả mã trong dự án chúng ta đang làm việc. Jar sẽ được tạo ra và đặt
trong thư mục `target/` của không gian làm việc dự án của bạn.

Đảm bảo bạn đang trong thư mục gốc của dự án trước khi thực hiện lệnh này: cùng cấp thư mục với tệp `pom.xml`.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-003.png?featherlight=false&width=90pc)

### Thực thi chương trình

`java -cp target/amazon-kinesis-replay-1.0-SNAPSHOT.jar A_SimpleProducer`

Điều này nên được thực thi cùng cấp thư mục với tệp pom.xml.
Khi bạn thấy đầu ra cho biết rằng nó đã bắt đầu sản xuất, chúng ta sẽ đợi khoảng 5 phút để CloudWatch làm mới.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-004.png?featherlight=false&width=90pc)
Sau 5 phút...

Điều hướng đến bảng điều khiển AWS Management Console và mở tab Monitoring của Kinesis Data Stream của bạn. Lưu ý những
gì bạn thấy trong các khu vực Incoming data và Put Records metrics.

Nếu bạn thấy các bản ghi đang đến trong cả hai bảng điều khiển giám sát này, xin chúc mừng! Bạn đã xuất bản dữ liệu
thành công vào Kinesis Data Stream bằng cách sử dụng thư viện Kinesis Producer - một nhà sản xuất bền vững trong xử lý
lỗi sẽ đơn giản hóa quy trình sản xuất của bạn.

Bạn có thể cần nhấn REFRESH trên trang sau vài phút để thấy các số liệu xuất hiện.

![Image](/repo_pmt_ws-fcj-005/images/2/3/23-005.png?featherlight=false&width=90pc)

{{% notice warning %}}
Dừng sản xuất dữ liệu trong cửa sổ này bằng cách gõ lệnh sau trong terminal của bạn:
CTRL + C
{{% /notice %}}

Bạn có thể muốn giữ cho dữ liệu tiếp tục chảy nếu bạn dự định hoàn thành bất kỳ phòng thí nghiệm tiếp theo nào. Chỉ cần
nhớ dừng sản xuất khi bạn đã hoàn thành!
