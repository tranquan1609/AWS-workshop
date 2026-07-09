---
title: "Workshop"
date: 2026-07-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# **Event Management (DACSWEBSK)** trên AWS — Nền tảng quản lý sự kiện & học tập trực tuyến

#### Tổng quan

**Event Management (DACSWEBSK)** là ứng dụng ASP.NET Core 8 MVC dùng để tổ chức sự kiện và học tập trực tuyến. Trong workshop này, hệ thống được migrate từ demo monolithic chạy local lên kiến trúc kiểu production trên **AWS (ap-southeast-1 / Singapore)**.

Mục tiêu là minh họa đầy đủ lộ trình lên Cloud: compute, database, storage, email, serverless jobs, load balancing, bảo mật ứng dụng web, secrets, backup và monitoring.

#### Kiến trúc đã triển khai

```
Internet
  → AWS WAF (dacswebsk-waf)
  → Application Load Balancer (dacswebsk-alb)
  → Target Group (dacswebsk-tg) → EC2 (dacswebsk-app)
  → Amazon RDS SQL Server (dacswebsk-db)

Chatbot:
  Browser → API Gateway → Lambda (dacsweb-chatbot) → RDS

Background jobs:
  EventBridge → Lambda × 3 (cập nhật trạng thái / chứng chỉ / thông báo)

Storage & email:
  Amazon S3 (ảnh, video, chứng chỉ, upload)
  Amazon SES (email giao dịch)

Ops & security:
  Secrets Manager, CloudWatch Alarms, CloudTrail, AWS Backup
```



#### Các dịch vụ AWS đã dùng


| #   | Dịch vụ                    | Tài nguyên / vai trò                                    |
| --- | -------------------------- | ------------------------------------------------------- |
| 1   | **IAM**                    | User `dacswebsk-dev`, EC2 role `DacsWebEc2Role`         |
| 2   | **Amazon VPC**             | `dacswebsk-vpc`, subnet public/private, security groups |
| 3   | **Amazon RDS**             | SQL Server Express `dacswebsk-db`                       |
| 4   | **Amazon S3**              | Bucket ảnh / video / chứng chỉ / upload                 |
| 5   | **Amazon SES**             | Identity email đã Verified                              |
| 6   | **Amazon EC2**             | Host app `dacswebsk-app` + Elastic IP                   |
| 7   | **Elastic Load Balancing** | ALB `dacswebsk-alb` + Target Group                      |
| 8   | **AWS WAF**                | Protection pack `dacswebsk-waf` (18 rules)              |
| 9   | **AWS Lambda**             | 4 function (jobs + chatbot)                             |
| 10  | **Amazon EventBridge**     | 3 scheduled cron rules                                  |
| 11  | **Amazon API Gateway**     | HTTP API cho chatbot                                    |
| 12  | **AWS Secrets Manager**    | Secret `dacswebskproduction`                            |
| 13  | **Amazon CloudWatch**      | Alarm cho EC2 / RDS                                     |
| 14  | **AWS CloudTrail**         | Trail `dacswebsk-trail`                                 |
| 15  | **AWS Backup**             | Plan `dacswebsk-backup-plan` cho RDS                    |




#### Nội dung

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [Chuẩn bị — IAM & CLI](5.2-Prerequisite/)
3. [VPC & mạng](5.3-VPC-Networking/)
4. [Amazon RDS (SQL Server)](5.4-RDS/)
5. [Amazon S3 & SES](5.5-S3-SES/)
6. [EC2, ALB & WAF](5.6-EC2-ALB-WAF/)
7. [Lambda, EventBridge & Chatbot](5.7-Lambda-EventBridge/)
8. [Monitoring & bảo mật vận hành](5.8-Monitoring-Security/)
9. [Dọn dẹp tài nguyên](5.9-Cleanup/)

