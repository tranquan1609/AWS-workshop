---
title: "Bản đề xuất"
date: 2026-06-22
weight: 2
chapter: false
pre: " <b> 2. </b> "
---



# Event Management (DACSWEBSK) trên AWS
## Giải pháp triển khai ứng dụng ASP.NET Core với các dịch vụ AWS được quản lý

### 1. Tóm tắt điều hành

**Event Management (DACSWEBSK)** là một ứng dụng web được phát triển trên nền tảng ASP.NET Core 8 MVC nhằm hỗ trợ quản lý sự kiện và học tập trực tuyến. Hệ thống cung cấp các chức năng như quản lý người tham gia, lịch trình, video, bài thu hoạch, chứng chỉ, gửi email thông báo và chatbot hỗ trợ người dùng.

Trong phiên bản hiện tại, ứng dụng được triển khai trên hạ tầng cục bộ với SQL Server, lưu trữ tệp trên máy chủ và sử dụng Gmail SMTP để gửi email. Mô hình này đáp ứng tốt nhu cầu phát triển ban đầu nhưng còn nhiều hạn chế về khả năng mở rộng, tính sẵn sàng và công tác vận hành.

Đề xuất này trình bày phương án triển khai hệ thống trên nền tảng AWS bằng cách sử dụng các dịch vụ managed như Amazon EC2, Amazon RDS, Amazon S3, Amazon SES, AWS Lambda và Application Load Balancer. Giải pháp hướng đến việc nâng cao khả năng mở rộng, bảo mật và độ tin cậy của hệ thống, đồng thời vẫn giữ nguyên kiến trúc ứng dụng và cơ sở dữ liệu hiện có.

### 2. Tuyên bố vấn đề

*Vấn đề hiện tại*

Trong môi trường phát triển ban đầu, DACSWEBSK phụ thuộc vào hạ tầng cục bộ:

- Cơ sở dữ liệu **SQL Server** cài đặt trên máy chủ riêng, khó sao lưu và mở rộng.
- Tệp hình ảnh, video, chứng chỉ lưu trên ổ đĩa máy chủ, dễ mất dữ liệu khi thay thế hoặc khởi tạo lại instance.
- Email gửi qua **Gmail SMTP**, không phù hợp cho môi trường production quy mô lớn.
- Các tác vụ nền chạy liên tục dưới dạng **.NET Hosted Services**, tiêu tốn tài nguyên EC2 ngay cả khi không có công việc xử lý.
- Thiếu các lớp bảo vệ và giám sát chuẩn cloud như WAF, audit trail, backup tự động và quản lý secret tập trung.

*Giải pháp*

Triển khai DACSWEBSK trên AWS theo mô hình **multi-tier**:

- **Amazon RDS for SQL Server Express** thay thế SQL Server cục bộ.
- **Amazon S3** lưu trữ object cho hình ảnh, video, chứng chỉ và tệp tải lên.
- **Amazon SES** thay thế Gmail SMTP.
- **Amazon EC2** host ứng dụng ASP.NET Core; **ALB** phân phối lưu lượng; **AWS WAF** bảo vệ Layer 7.
- **AWS Lambda** + **Amazon EventBridge** thay thế Hosted Services cho các tác vụ nền.
- **Amazon API Gateway** + **AWS Lambda** cung cấp Chatbot API truy vấn RDS.
- **AWS Secrets Manager**, **CloudWatch**, **CloudTrail** và **AWS Backup** hỗ trợ vận hành và bảo mật.

*Lợi ích kỳ vọng*

- Giảm gánh nặng quản trị hạ tầng nhờ dịch vụ managed.
- Tăng độ bền vững dữ liệu (RDS backup, S3 object storage).
- Tách tác vụ nền sang serverless, tối ưu chi phí và khả năng mở rộng.
- Có kiến trúc và tài liệu triển khai thực tế phục vụ báo cáo FCJ và phát triển tiếp theo.

### 3. Kiến trúc giải pháp

Hệ thống sau triển khai được tổ chức theo các luồng chính:

![Kiến trúc hệ thống DACSWEBSK trên AWS](/AWS-workshop/images/2-Proposal/dacswebsk-architecture-diagram.png)

*Dịch vụ AWS sử dụng*

| STT | Dịch vụ | Vai trò |
|-----|---------|---------|
| 1 | **IAM** | User `dacswebsk-dev`, EC2 role `DacsWebEc2Role` |
| 2 | **Amazon VPC** | `dacswebsk-vpc`, subnet public/private, security groups |
| 3 | **Amazon RDS** | SQL Server Express `dacswebsk-db` |
| 4 | **Amazon S3** | Bucket ảnh / video / chứng chỉ / upload |
| 5 | **Amazon SES** | Gửi email từ ứng dụng |
| 6 | **Amazon EC2** | Host app `dacswebsk-app` + Elastic IP |
| 7 | **Elastic Load Balancing (ALB)** | `dacswebsk-alb` + Target Group |
| 8 | **AWS WAF** | Web ACL `dacswebsk-waf` (managed rules) |
| 9 | **AWS Lambda** | 4 functions (jobs + chatbot) |
| 10 | **Amazon EventBridge** | 3 scheduled cron rules |
| 11 | **Amazon API Gateway** | HTTP API cho chatbot |
| 12 | **AWS Secrets Manager** | Secret `dacswebskproduction` |
| 13 | **Amazon CloudWatch** | Alarm EC2 / RDS |
| 14 | **AWS CloudTrail** | Trail `dacswebsk-trail` |
| 15 | **AWS Backup** | Plan `dacswebsk-backup-plan` cho RDS |

*Thiết kế thành phần*

- **Tầng mạng:** VPC với public subnet (2 AZ) cho ALB/EC2, private subnet dự phòng mở rộng; Security Groups kiểm soát luồng ALB → EC2 → RDS.
- **Tầng ứng dụng:** EC2 chạy `dotnet DACSWEBSK.dll` cổng 80; ALB health check qua Target Group.
- **Tầng dữ liệu:** RDS lưu dữ liệu quan hệ và ASP.NET Identity; S3 lưu media và tệp tải lên qua `IFileStorageService`.
- **Tầng serverless:** Lambda xử lý cron jobs và chatbot; EventBridge lập lịch; API Gateway expose endpoint `/chat`.
- **Tầng vận hành:** Secrets Manager lưu connection string; Backup sao lưu RDS; CloudTrail audit; CloudWatch cảnh báo.

### 4. Triển khai kỹ thuật

*Các giai đoạn triển khai*

1. **Chuẩn bị:** Tạo IAM user, cấu hình AWS CLI, chọn Region `ap-southeast-1`.
2. **Hạ tầng mạng:** VPC, subnet, Internet Gateway, route table, Security Groups.
3. **Dữ liệu & lưu trữ:** RDS SQL Server, S3 buckets, SES identity.
4. **Ứng dụng & edge:** EC2, publish ASP.NET Core, ALB, WAF.
5. **Serverless:** Lambda functions, EventBridge rules, API Gateway chatbot.
6. **Vận hành:** Secrets Manager, Backup, CloudTrail, CloudWatch alarms.
7. **Kiểm thử & báo cáo:** Kiểm tra luồng người dùng, admin, upload, email, chatbot; chụp screenshot Console.
8. **Dọn dẹp:** Xóa tài nguyên theo thứ tự phụ thuộc khi kết thúc workshop.

*Yêu cầu kỹ thuật*

- Ứng dụng: **ASP.NET Core 8**, **Entity Framework Core**, **ASP.NET Identity**, SQL Server.
- Triển khai EC2: Linux/Windows tùy AMI đã chọn; chạy ứng dụng cổng 80.
- Tích hợp S3 qua `IFileStorageService` (S3 provider); cấu hình SES thay SMTP.
- Migration database: `dotnet ef database update` trên RDS.
- Lambda .NET 8: tách logic từ Hosted Services; chatbot truy vấn RDS qua VPC (nếu cấu hình).
- Bảo mật: không hardcode secret; dùng Secrets Manager và IAM least privilege.

### 5. Lộ trình & mốc triển khai

| Giai đoạn | Nội dung chính |
|-----------|----------------|
|  1–2  | Làm quen FCJ, AWS Console/CLI, EC2 cơ bản |
|  3–4  | Nghiên cứu kiến trúc DACSWEBSK, lập đề xuất và thiết kế triển khai AWS |
|  5–6  | Triển khai VPC, RDS, S3, SES, EC2, ALB, WAF |
|  7–8  | Lambda, EventBridge, API Gateway chatbot; tích hợp ứng dụng |
|  9–10 | Secrets Manager, Backup, CloudTrail, CloudWatch; kiểm thử end-to-end |
|  11   | Hoàn thiện workshop, báo cáo, dọn dẹp tài nguyên (tùy chọn giữ lại demo) |

Chi tiết triển khai từng bước được mô tả tại [Chương 5 — Workshop](../5-workshop/).

### 6. Ước tính ngân sách

Chi phí phụ thuộc thời gian chạy và cấu hình instance. Với môi trường workshop (EC2 `t3.micro`, RDS SQL Server Express `db.t3.micro`, ALB, WAF, Lambda theo lịch), chi phí ước tính khoảng **30–80 USD/tháng** khi hệ thống chạy liên tục.

| Hạng mục | Ghi chú |
|----------|---------|
| Amazon EC2 `t3.micro` | Compute host ứng dụng |
| Amazon RDS SQL Server Express | Chi phí RDS SQL Server cao hơn MySQL/PostgreSQL |
| Application Load Balancer | Phí cố định + LCU |
| AWS WAF | Phí theo Web ACL và rule |
| Amazon S3, Lambda, SES, CloudWatch | Thường thấp ở quy mô workshop |
| Elastic IP (gắn EC2) | Miễn phí khi đang sử dụng |

**Khuyến nghị:** Dùng [AWS Pricing Calculator](https://calculator.aws/) để ước tính chính xác; tắt hoặc xóa ALB/RDS/EC2 khi không dùng; ưu tiên instance nhỏ và dọn dẹp sau workshop (xem [5.9 — Dọn dẹp](../5-workshop/5.9-cleanup/)).

### 7. Đánh giá rủi ro

*Ma trận rủi ro*

| Rủi ro | Mức ảnh hưởng | Chiến lược giảm thiểu |
|--------|---------------|------------------------|
| Chi phí vượt dự kiến | Trung bình | Dùng instance nhỏ, xóa tài nguyên khi không dùng, theo dõi Billing |
| WAF chặn form upload | Trung bình | Override BODY rules sang Count hoặc tạm disassociate WAF khi test |
| RDS không kết nối được | Cao | Kiểm tra Security Group port 1433, connection string qua Secrets Manager |
| SES Sandbox | Thấp | Verify email; request Production Access khi triển khai thật |
| Mất dữ liệu | Cao | AWS Backup cho RDS; snapshot trước khi xóa |

*Kế hoạch dự phòng*

- Giữ snapshot RDS trước khi thay đổi lớn hoặc dọn dẹp.
- Document lại cấu hình Security Group và endpoint để khôi phục nhanh.
- Có thể quay về môi trường local để phát triển tính năng nếu AWS tạm ngưng.

### 8. Kết quả kỳ vọng

*Cải tiến kỹ thuật*

- Ứng dụng DACSWEBSK chạy trên AWS với luồng **WAF → ALB → EC2 → RDS**.
- Lưu trữ tệp trên S3; email qua SES; background jobs qua Lambda/EventBridge.
- Chatbot serverless qua API Gateway.
- Quan sát và bảo mật: CloudWatch, CloudTrail, Secrets Manager, Backup.

