---
title: "Tổng quan workshop"
date: 2026-07-01
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Giới thiệu dự án

Workshop này sử dụng **Event Management (DACSWEBSK)** làm ứng dụng mẫu để minh họa quá trình triển khai một hệ thống **ASP.NET Core MVC** trên nền tảng AWS. Ứng dụng hỗ trợ quản lý sự kiện, người tham gia, lịch trình, video, bài thu hoạch, chứng chỉ và gửi thông báo đến người dùng.

Trong phiên bản ban đầu, ứng dụng sử dụng SQL Server cài đặt cục bộ, lưu trữ tệp trên máy chủ và gửi email thông qua Gmail SMTP. Những thành phần này phù hợp trong giai đoạn phát triển, nhưng còn nhiều hạn chế về khả năng mở rộng, tính sẵn sàng và bảo mật.

Thông qua workshop này, các thành phần trên được chuyển sang dịch vụ managed của AWS như **Amazon RDS**, **Amazon S3**, **Amazon SES** và **AWS Lambda**, giúp hệ thống vận hành ổn định hơn, dễ mở rộng và tuân theo các thực hành tốt của kiến trúc đám mây.

#### Mục tiêu workshop

- Triển khai một ứng dụng ASP.NET Core MVC trên hạ tầng AWS theo mô hình **nhiều tầng (Multi-tier Architecture)**.
- Tích hợp các dịch vụ cốt lõi của AWS như **Amazon EC2**, **Amazon RDS**, **Amazon S3**, **Amazon SES**, **AWS Lambda** và **Amazon EventBridge** vào một hệ thống hoàn chỉnh.
- Thực hành cấu hình mạng với **Amazon VPC**, Public Subnet, Private Subnet, **NAT Gateway** và **Security Group**.
- Xây dựng quy trình giám sát, bảo mật và sao lưu bằng **Amazon CloudWatch**, **AWS CloudTrail**, **AWS Backup** và **AWS Secrets Manager**.
- Lưu lại toàn bộ quá trình triển khai thông qua ảnh chụp màn hình **AWS Management Console** để phục vụ báo cáo **First Cloud Journey (FCAJ)**.
- Tối ưu chi phí triển khai bằng các cấu hình phù hợp môi trường workshop, ví dụ **EC2 t3.micro** và **Amazon RDS SQL Server Express**.

#### Những gì sẽ được xây dựng

Trong workshop này, chúng ta sẽ từng bước xây dựng một kiến trúc hoàn chỉnh cho hệ thống Event Management trên AWS. Kiến trúc bao gồm các thành phần về mạng, tính toán, lưu trữ, cơ sở dữ liệu, bảo mật và giám sát như sau:

| Tầng | Thành phần |
|------|------------|
| Network | VPC `dacswebsk-vpc`, subnet public (2 AZ), subnet private, IGW, security groups |
| Data | RDS SQL Server Express `dacswebsk-db` |
| App | EC2 `dacswebsk-app` chạy ASP.NET Core cổng 80 |
| Edge | ALB + Target Group (healthy) + WAF |
| Storage | S3 bucket cho sự kiện, video, chứng chỉ, upload |
| Email | SES identity đã verified |
| Serverless | Lambda jobs + EventBridge cron + API Gateway chatbot |
| Ops | Secrets Manager, CloudWatch, CloudTrail, AWS Backup |

#### Luồng xử lý của hệ thống

Sau khi triển khai hoàn tất, luồng hoạt động của ứng dụng diễn ra như sau:

1. Người dùng truy cập ứng dụng thông qua tên miền được quản lý bởi **Amazon Route 53**. Các yêu cầu HTTP/HTTPS được kiểm tra bởi **AWS WAF** trước khi chuyển đến **Application Load Balancer (ALB)**.
2. **ALB** phân phối lưu lượng truy cập đến máy chủ **EC2** đang chạy ứng dụng ASP.NET Core MVC.
3. Ứng dụng xử lý yêu cầu từ người dùng và lưu trữ dữ liệu nghiệp vụ trên **Amazon RDS for SQL Server**.
4. Các tệp như hình ảnh, video và chứng chỉ được lưu trên **Amazon S3** thay vì ổ đĩa cục bộ.
5. Các tác vụ nền được thực hiện bởi **AWS Lambda**, kích hoạt tự động thông qua **Amazon EventBridge** theo lịch hoặc theo sự kiện.
6. Khi cần gửi email hoặc thông báo, ứng dụng sử dụng **Amazon SES** để gửi thư điện tử đến người dùng.
