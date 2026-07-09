---
title: "Amazon S3 & SES"
date: 2026-07-01
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

Tiếp theo chúng ta thay thế hai thành phần quan trọng của hệ thống đang triển khai cục bộ bằng các dịch vụ được quản lý trên AWS. Cụ thể, **Amazon Simple Storage Service (Amazon S3)** được sử dụng để lưu trữ các tệp như hình ảnh, video và chứng chỉ, trong khi **Amazon Simple Email Service (Amazon SES)** đảm nhiệm chức năng gửi email thay cho Gmail SMTP.

Việc sử dụng các dịch vụ managed giúp ứng dụng dễ dàng mở rộng, tăng độ tin cậy và giảm sự phụ thuộc vào tài nguyên của máy chủ EC2.

#### Amazon S3 — Lưu trữ đối tượng

Tạo các S3 Bucket riêng cho từng loại dữ liệu. Tất cả các Bucket được cấu hình ở chế độ **Private** và bật **Block Public Access** nhằm đảm bảo chỉ ứng dụng hoặc các dịch vụ được cấp quyền mới có thể truy cập.

| Bucket | Mục đích |
|--------|----------|
| `dacswebsk-images-quan` | Lưu trữ hình ảnh sự kiện |
| `dacswebsk-videos-quan` | Lưu trữ video học tập |
| `dacswebsk-certificates-quan` | Lưu trữ chứng chỉ được tạo từ hệ thống |
| `dacswebsk-uploads-quan` | Lưu trữ bài tập và các tệp minh chứng |

Sau khi tạo xong các Bucket, kiểm tra lại danh sách để đảm bảo tất cả đã được khởi tạo thành công.

![Danh sách S3 buckets](/images/5-Workshop/5.5-S3-SES/09-s3-buckets-list.png)

#### Kiểm tra chức năng tải tệp

Sau khi hoàn tất cấu hình Amazon S3, tạo một sự kiện mới trên giao diện quản trị và tải lên hình ảnh minh họa. Nếu quá trình cấu hình chính xác, tệp sẽ được lưu trong Bucket tương ứng dưới thư mục (prefix) `events/`.

![Object sự kiện trên S3](/images/5-Workshop/5.5-S3-SES/10-s3-events-objects-uploaded.png)

Ứng dụng ASP.NET Core sử dụng **`IFileStorageService`** với **S3 Provider** để quản lý việc tải lên và lưu trữ tệp. Nhờ đó, dữ liệu không còn được lưu trên ổ đĩa của EC2 mà được lưu trữ dưới dạng object trên Amazon S3, giúp tăng tính bền vững, khả năng mở rộng và giảm rủi ro mất dữ liệu khi thay thế hoặc khởi tạo lại máy chủ.

#### Amazon SES — Gửi email

Tiếp theo, cấu hình **Amazon Simple Email Service (Amazon SES)** để thay thế dịch vụ Gmail SMTP.

Thực hiện các bước sau:

1. Truy cập **Amazon SES → Verified identities**.
2. Xác minh địa chỉ email dùng để gửi thư (ví dụ: `testmailxn@gmail.com`).
3. Đảm bảo trạng thái của địa chỉ email là **Verified**.
4. Gửi email kiểm tra từ AWS Management Console hoặc thực hiện chức năng gửi email trực tiếp từ ứng dụng.

![SES identity Verified](/images/5-Workshop/5.5-S3-SES/11-ses-identity-verified.png)

> **Lưu ý:** Đối với các tài khoản AWS mới, Amazon SES thường hoạt động ở chế độ **Sandbox**. Trong chế độ này, hệ thống chỉ có thể gửi email đến các địa chỉ đã được xác minh. Khi triển khai thực tế, bạn cần gửi yêu cầu **Production Access** để loại bỏ giới hạn này.

#### Section summary

Ở phần này chúng ta đã tích hợp thành công **Amazon S3** và **Amazon SES** vào hệ thống Event Management. Amazon S3 thay thế cơ chế lưu trữ tệp cục bộ trong thư mục `wwwroot`, giúp dữ liệu được lưu trữ bền vững và dễ dàng mở rộng. Đồng thời, Amazon SES thay thế Gmail SMTP để xử lý việc gửi email từ ứng dụng. Hai dịch vụ này giúp loại bỏ các thành phần phụ thuộc vào môi trường cục bộ, đưa hệ thống tiến gần hơn đến một kiến trúc cloud-native trên AWS.
