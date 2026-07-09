---
title: "Lambda, EventBridge & Chatbot"
date: 2026-07-01
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Trong chương này, chúng ta chuyển các tác vụ nền (Background Jobs) của ứng dụng sang kiến trúc **Serverless** bằng cách sử dụng **AWS Lambda** kết hợp với **Amazon EventBridge**. Thay vì chạy liên tục trên máy chủ EC2 dưới dạng Hosted Services của .NET, các tác vụ được kích hoạt theo lịch và chỉ tiêu tốn tài nguyên khi thực thi.

Bên cạnh đó, workshop cũng triển khai một **Chatbot API** sử dụng **Amazon API Gateway** và **AWS Lambda**, cho phép giao diện web giao tiếp với cơ sở dữ liệu Amazon RDS thông qua kiến trúc serverless.

#### Chuyển Background Jobs sang Serverless

Trong phiên bản triển khai cục bộ, ứng dụng sử dụng ba **.NET Hosted Services** để tự động cập nhật trạng thái sự kiện, tạo chứng chỉ và gửi thông báo sau khi sự kiện kết thúc. Khi triển khai trên AWS, các tác vụ này được chuyển thành các **AWS Lambda Functions** viết bằng **.NET 8** và được kích hoạt tự động thông qua **Amazon EventBridge**.

| Lambda Function | Vai trò |
|-----------------|---------|
| `dacsweb-event-status-update` | Cập nhật trạng thái sự kiện theo thời gian |
| `dacsweb-auto-certificate` | Tự động tạo chứng chỉ cho người tham gia |
| `dacsweb-notification` | Gửi thông báo sau khi sự kiện kết thúc |
| `dacsweb-chatbot` | Xử lý yêu cầu từ Chatbot và truy vấn Amazon RDS |

Sau khi triển khai, kiểm tra danh sách Lambda Functions để đảm bảo tất cả các hàm đã được tạo thành công.

![Danh sách Lambda](/images/5-Workshop/5.7-Lambda-EventBridge/17-lambda-functions-list.png)

#### Cấu hình Amazon EventBridge

Tiếp theo, tạo các **EventBridge Rules** để kích hoạt các Lambda Functions theo lịch định sẵn.

| Rule | Mô tả | Trạng thái |
|------|-------|------------|
| `dacsweb-event-status-cron` | Cập nhật trạng thái sự kiện mỗi 5 phút | Enabled |
| `dacsweb-certificate-cron` | Tự động tạo chứng chỉ mỗi 15 phút | Enabled |
| `dacsweb-notification-cron` | Gửi thông báo sau khi sự kiện kết thúc mỗi 5 phút | Enabled |

Sau khi hoàn tất cấu hình, các Lambda Functions sẽ được kích hoạt tự động theo lịch mà không cần chạy liên tục trên máy chủ EC2.

![EventBridge rules](/images/5-Workshop/5.7-Lambda-EventBridge/19-eventbridge-scheduled-rules.png)

#### Triển khai Chatbot với API Gateway

Ngoài các tác vụ nền, workshop còn triển khai một API cho Chatbot bằng **Amazon API Gateway** và **AWS Lambda**.

Luồng xử lý của Chatbot như sau:

```text
Browser (Chatbot Widget)
        │
        ▼
Amazon API Gateway
        │
        ▼
AWS Lambda (dacsweb-chatbot)
        │
        ▼
Amazon RDS
```

Khi người dùng gửi câu hỏi từ giao diện web, yêu cầu được chuyển đến Amazon API Gateway, sau đó kích hoạt Lambda Function `dacsweb-chatbot`. Lambda xử lý nghiệp vụ, truy vấn dữ liệu từ Amazon RDS và trả kết quả về cho ứng dụng.

Kiểm tra Lambda Function thông qua **AWS Lambda Console**. Nếu kết quả trả về **HTTP 200** cùng với các tiêu đề CORS phù hợp, quá trình tích hợp giữa API Gateway, Lambda và giao diện web đã được cấu hình thành công.

![Lambda chatbot test thành công](/images/5-Workshop/5.7-Lambda-EventBridge/18-lambda-chatbot-test-success.png)

Ví dụ địa chỉ Chat API:

```text
https://tcunvi9s86.execute-api.ap-southeast-1.amazonaws.com/chat
```

#### Section summary

Trong chương này, bạn đã chuyển các tác vụ nền của ứng dụng từ **.NET Hosted Services** sang kiến trúc **Serverless** bằng cách sử dụng **AWS Lambda** và **Amazon EventBridge**. Cách triển khai này giúp giảm tải cho máy chủ EC2, tối ưu chi phí vận hành và chỉ sử dụng tài nguyên khi có yêu cầu xử lý. Đồng thời, Chatbot cũng được triển khai theo mô hình serverless với **Amazon API Gateway** đóng vai trò điểm tiếp nhận yêu cầu và **AWS Lambda** đảm nhiệm xử lý nghiệp vụ trước khi truy vấn dữ liệu từ Amazon RDS.
