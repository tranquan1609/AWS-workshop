---
title: "EC2, ALB & WAF"
date: 2026-07-01
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Chúng ta triển khai ứng dụng **Event Management (DACSWEBSK)** trên **Amazon Elastic Compute Cloud (Amazon EC2)**. Sau khi ứng dụng được triển khai thành công, **Application Load Balancer (ALB)** được cấu hình để phân phối lưu lượng truy cập và cung cấp một điểm truy cập ổn định cho người dùng. Cuối cùng, **AWS Web Application Firewall (AWS WAF)** được tích hợp nhằm bảo vệ ứng dụng khỏi các mối đe dọa phổ biến ở tầng ứng dụng (Layer 7).

#### Amazon EC2 — Triển khai ứng dụng

Khởi tạo một EC2 Instance với các thông số sau:

| Mục | Giá trị |
|-----|---------|
| Type | `t3.micro` |
| VPC / Subnet | `dacswebsk-vpc` / Public Subnet `ap-southeast-1a` |
| Security Group | `dacswebsk-ec2-sg` |
| IAM Role | `DacsWebEc2Role` |
| Elastic IP | `18.140.214.160` |

Sau khi tạo thành công EC2, publish ứng dụng ASP.NET Core và khởi chạy trên cổng **80** bằng lệnh:

```bash
sudo nohup dotnet DACSWEBSK.dll --urls http://0.0.0.0:80 > app.log 2>&1 &
```

Nếu ứng dụng khởi động thành công, trạng thái của EC2 sẽ là **Running** và có thể truy cập trực tiếp thông qua Elastic IP.

![EC2 Running](/images/5-Workshop/5.6-EC2-ALB-WAF/12-ec2-instance-running.png)

![EC2 instance summary](/images/5-Workshop/5.6-EC2-ALB-WAF/13-ec2-instance-summary-eip.png)

Kiểm tra ứng dụng bằng cách truy cập:

```
http://18.140.214.160
```

Nếu giao diện ứng dụng hiển thị đúng, quá trình triển khai lên Amazon EC2 đã hoàn tất.

#### Application Load Balancer

Tiếp theo, tạo **Application Load Balancer (ALB)** để tiếp nhận lưu lượng truy cập từ Internet và phân phối đến EC2 Instance.

Cấu hình ALB như sau:

- Tên: `dacswebsk-alb`
- Scheme: **Internet-facing**
- Availability Zones: `ap-southeast-1a` và `ap-southeast-1b`
- Listener: **HTTP (80)** chuyển tiếp đến Target Group `dacswebsk-tg`
- Target: EC2 `dacswebsk-app` trên cổng **80**, trạng thái **Healthy**

Sau khi cấu hình hoàn tất, kiểm tra trạng thái của Load Balancer và Target Group để đảm bảo EC2 đã vượt qua Health Check.

![ALB Active](/images/5-Workshop/5.6-EC2-ALB-WAF/14-alb-active-listener.png)

![Target Group Healthy](/images/5-Workshop/5.6-EC2-ALB-WAF/15-target-group-healthy.png)

Ví dụ địa chỉ DNS do ALB cung cấp:

```
http://dacswebsk-alb-1804460399.ap-southeast-1.elb.amazonaws.com
```

Từ thời điểm này, người dùng nên truy cập ứng dụng thông qua địa chỉ DNS của ALB thay vì Elastic IP của EC2.

#### AWS WAF

Để tăng cường khả năng bảo vệ ứng dụng, tạo một **AWS WAF Web ACL** có tên **`dacswebsk-waf`** (Regional) và liên kết với Application Load Balancer.

Workshop sử dụng các **AWS Managed Rule Groups** với tổng cộng **18 luật** nhằm phát hiện và ngăn chặn các cuộc tấn công phổ biến như SQL Injection, Cross-Site Scripting (XSS) và các mẫu lưu lượng truy cập bất thường.

![WAF protection pack](/images/5-Workshop/5.6-EC2-ALB-WAF/16-waf-protection-pack.png)

> **Lưu ý:** Trong quá trình thực hành, **AWS Managed Common Rule Set** có thể chặn các yêu cầu POST chứa tệp tải lên (ví dụ: tạo sự kiện kèm hình ảnh trong giao diện quản trị). Để thuận tiện cho việc kiểm thử, bạn có thể:
>
> 1. Chuyển rule **`SizeRestrictions_BODY`** (và các BODY Rules liên quan) sang chế độ **Count**.
> 2. Hoặc tạm thời **Disassociate** WAF khỏi ALB trong quá trình kiểm thử chức năng tải tệp.

Sau khi hoàn thành việc kiểm thử, nên khôi phục lại cấu hình bảo vệ ban đầu để đảm bảo ứng dụng được bảo vệ đầy đủ.

#### Section summary

Bây giờ chúng ta đã triển khai thành công ứng dụng **Event Management (DACSWEBSK)** trên **Amazon EC2**, cấu hình **Application Load Balancer** để phân phối lưu lượng truy cập và sử dụng **AWS WAF** nhằm bảo vệ ứng dụng trước các mối đe dọa ở tầng ứng dụng. Đây là nền tảng giúp hệ thống hoạt động ổn định, có khả năng mở rộng và đáp ứng tốt hơn các yêu cầu của môi trường production.
