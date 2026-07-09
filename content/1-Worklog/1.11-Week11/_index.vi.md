---
title: "Worklog Tuần 11"
date: 2026-06-29
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

**Tuần 11:** 29/06/2026 – 03/07/2026

### Mục tiêu tuần 11

- Bắt đầu triển khai dự án **Event Management (DACSWEBSK)** lên AWS theo kiến trúc multi-tier đã đề xuất.
- Chuẩn bị môi trường IAM, AWS CLI và tích hợp mã nguồn ASP.NET Core với các dịch vụ AWS.
- Triển khai tầng mạng (VPC), cơ sở dữ liệu (RDS), lưu trữ (S3) và email (SES).
- Đưa ứng dụng lên EC2, cấu hình ALB và WAF; triển khai các tác vụ nền serverless bằng Lambda, EventBridge và API Gateway.

### Các nhiệm vụ trong tuần

| Thứ | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu kiến trúc triển khai DACSWEBSK trên AWS và hoàn thiện bản đề xuất (Proposal).<br>- Tạo IAM User `dacswebsk-dev`, cấu hình AWS CLI region `ap-southeast-1`, xác minh bằng `aws sts get-caller-identity`.<br>- Chuẩn bị mã nguồn: thêm AWS SDK (S3, SES), lớp `IFileStorageService` hỗ trợ Local/S3, file `appsettings.json`.<br>- Chạy thử ứng dụng local trước khi migrate lên Cloud. | 29/06/2026 | 29/06/2026 | [5.1 — Tổng quan](../../5-Workshop/5.1-Workshop-overview/)<br>[5.2 — Chuẩn bị IAM & CLI](../../5-Workshop/5.2-Prerequisite/)<br>[2 — Bản đề xuất](../../2-Proposal/) |
| 3 | - Tạo VPC `dacswebsk-vpc`, subnet public/private, Internet Gateway, Security Groups.<br>- Triển khai Amazon RDS SQL Server Express `dacswebsk-db`, cấu hình Security Group cổng 1433.<br>- Cập nhật connection string, chạy `dotnet ef database update` để migrate schema lên RDS.<br>- Kiểm tra đăng ký tài khoản và tạo sự kiện qua ứng dụng kết nối RDS. | 30/06/2026 | 30/06/2026 | [5.3 — VPC & mạng](../../5-Workshop/5.3-VPC-Networking/)<br>[5.4 — Amazon RDS](../../5-Workshop/5.4-RDS/) |
| 4 | - Tạo các S3 bucket cho ảnh sự kiện, video, chứng chỉ và file upload; bật Block Public Access.<br>- Cấu hình `Storage:Provider = S3` trong ứng dụng, kiểm tra upload ảnh sự kiện lên S3.<br>- Xác minh identity email trên Amazon SES, cấu hình SMTP SES thay Gmail.<br>- Kiểm tra gửi email xác nhận đăng ký / thông báo qua SES. | 01/07/2026 | 01/07/2026 | [5.5 — S3 & SES](../../5-Workshop/5.5-S3-SES/) |
| 5 | - Tạo EC2 instance `dacswebsk-app`, gắn IAM Role `DacsWebEc2Role`, publish và chạy `DACSWEBSK.dll` cổng 80.<br>- Cấu hình Application Load Balancer `dacswebsk-alb`, Target Group health check.<br>- Gắn AWS WAF `dacswebsk-waf` trước ALB; kiểm tra truy cập ứng dụng qua ALB.<br>- Xác minh luồng WAF → ALB → EC2 → RDS hoạt động ổn định. | 02/07/2026 | 02/07/2026 | [5.6 — EC2, ALB & WAF](../../5-Workshop/5.6-EC2-ALB-WAF/) |
| 6 | - Triển khai 4 Lambda function: `dacsweb-event-status`, `dacsweb-auto-certificate`, `dacsweb-notification`, `dacsweb-chatbot`.<br>- Tạo 3 EventBridge cron rules thay thế .NET Hosted Services (cập nhật trạng thái, cấp chứng chỉ, gửi email).<br>- Cấu hình API Gateway HTTP API endpoint `POST /chat` cho chatbot, kết nối Lambda truy vấn RDS.<br>- Kiểm tra chatbot từ giao diện web và xác minh job nền chạy theo lịch. | 03/07/2026 | 03/07/2026 | [5.7 — Lambda & EventBridge](../../5-Workshop/5.7-Lambda-EventBridge/) |

### Kết quả đạt được tuần 11

- Hoàn thiện bản đề xuất và lộ trình triển khai DACSWEBSK trên AWS, xác định rõ 15 dịch vụ AWS sử dụng trong workshop.
- Thiết lập thành công môi trường phát triển với IAM User `dacswebsk-dev` và AWS CLI; tích hợp AWS SDK vào mã nguồn ASP.NET Core.
- Triển khai VPC, RDS SQL Server Express và migrate database thành công; ứng dụng đọc/ghi dữ liệu trên RDS thay SQL Server local.
- Chuyển lưu trữ file sang Amazon S3 và gửi email qua Amazon SES; xác minh upload ảnh và nhận email thông báo.
- Đưa ứng dụng lên EC2, cấu hình ALB và WAF; hệ thống truy cập được qua `dacswebsk-alb` với luồng bảo vệ Layer 7.
- Triển khai Lambda, EventBridge và API Gateway; tách tác vụ nền và chatbot sang serverless, endpoint chat hoạt động qua API Gateway.
