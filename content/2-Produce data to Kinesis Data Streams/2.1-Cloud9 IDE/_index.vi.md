---
title: "Cấu hình AWS Cloud 9 IDE"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

Trong workshop này, chúng ta sẽ sử dụng [Cloud9](https://aws.amazon.com/vi/cloud9/), một môi trường phát triển tích hợp dựa trên đám mây (IDE) để đọc, chỉnh sửa và chạy mã của chúng ta cho một phần các workshop này.

Để sử dụng Cloud9, chúng ta cần hoàn thành các bước điều kiện tiên quyết sau:

* Trong bảng điều khiển quản lý AWS của bạn, điều hướng đến trang dịch vụ Cloud9 bằng cách nhập Cloud9 vào thanh tìm kiếm ở đầu bảng điều khiển.
* Bạn sẽ thấy một Instance Cloud9 (đã được tạo sẵn cho bạn) có tên là KinesisRealTimeStreaming-***, với *** là tên stack của bạn.
* Trên trang này, nhấp vào nút Open IDE để mở instance cloud9 của bạn cho workshop này.
  IDE có thể mất vài giây để tải--và khi nó mở, bạn sẽ thấy màn hình sau:

Chúng ta bây giờ sẽ tiến hành cấu hình Cloud9 cho các bài tập tiếp theo của chúng ta:



{{% notice info %}}
Java đã được cài đặt sẵn, và miễn là nó là > Java 1.8, các bài tập tiếp theo sẽ hoạt động! Bạn có thể xác minh điều này bằng cách gõ java -version trong terminal.
{{% /notice %}}

### Cài đặt Maven

Gõ lệnh sau trong một terminal mới để cài đặt Maven trên Cloud9 IDE của bạn:

`sudo yum install maven -y`

Sau khi maven đã được cài đặt, hãy đảm bảo chúng ta đang trỏ đến đúng phiên bản java.

### Trỏ đến Java 11

Trong terminal của bạn, gõ

```
sudo update-alternatives --config java
sudo update-alternatives --config javac
```

Đảm bảo hoặc gán lại phiên bản java đến `/usr/lib/jvm/java-11-amazon-corretto.x86_64/bin/java.`

Bạn có thể được nhắc nhập số này hai lần.
Bạn bây giờ có thể tiếp tục với bước tiếp theo trong workshop!
