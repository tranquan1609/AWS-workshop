---
title: "Giám sát, bảo mật & vận hành"
date: 2026-07-01
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

Trong chương này, chúng ta hoàn thiện hệ thống bằng cách bổ sung các dịch vụ hỗ trợ **giám sát (Monitoring)**, **bảo mật (Security)** và **quản trị vận hành (Operations)**. Những dịch vụ này giúp bảo vệ thông tin nhạy cảm, theo dõi hoạt động của hệ thống, giám sát tình trạng tài nguyên và hỗ trợ khôi phục dữ liệu khi cần thiết.

Đây là những thành phần quan trọng để đưa ứng dụng tiến gần hơn đến mô hình triển khai thực tế trên AWS.

#### AWS Secrets Manager

Thông tin nhạy cảm như **chuỗi kết nối đến Amazon RDS**, cấu hình **Amazon SES** hoặc các khóa truy cập không nên được lưu trực tiếp trong mã nguồn hoặc tệp `appsettings.json`.

Trong workshop này, các thông tin cấu hình của môi trường production được lưu trong Secret có tên **`dacswebskproduction`** và được ứng dụng truy xuất khi cần sử dụng.

Việc sử dụng **AWS Secrets Manager** giúp tăng cường bảo mật, đơn giản hóa quá trình quản lý thông tin xác thực và giảm nguy cơ rò rỉ dữ liệu khi chia sẻ hoặc quản lý mã nguồn.

![AWS Secrets Manager](/images/5-Workshop/5.8-Monitoring-Security/20-secrets-manager.png)

#### AWS Backup

Để bảo vệ dữ liệu của hệ thống, tạo một **Backup Plan** có tên **`dacswebsk-backup-plan`** và cấu hình sao lưu tự động hằng ngày cho cơ sở dữ liệu Amazon RDS.

Backup Plan sẽ được liên kết với tài nguyên **`dacswebsk-rds`**, giúp hệ thống có thể khôi phục dữ liệu khi xảy ra sự cố hoặc mất mát dữ liệu ngoài ý muốn.

![AWS Backup Plan](/images/5-Workshop/5.8-Monitoring-Security/21-backup-plan.png)

#### AWS CloudTrail

Tiếp theo, kích hoạt **AWS CloudTrail** để ghi lại toàn bộ các hoạt động quản trị và thao tác API trên tài khoản AWS.

Cấu hình Trail với các thông số sau:

- Trail Name: `dacswebsk-trail`
- Trail Logging: **Enabled**
- Multi-region Trail: **Yes**
- Lưu nhật ký vào Amazon S3

CloudTrail giúp theo dõi lịch sử hoạt động của tài khoản AWS, hỗ trợ kiểm tra, phân tích và truy vết khi xảy ra sự cố hoặc các thay đổi ngoài mong muốn.

![CloudTrail logging](/images/5-Workshop/5.8-Monitoring-Security/22-cloudtrail-logging.png)

#### Amazon CloudWatch

Cuối cùng, tạo các **CloudWatch Alarms** để theo dõi trạng thái hoạt động của hệ thống.

Workshop sử dụng các cảnh báo cho:

- Dung lượng lưu trữ còn lại của Amazon RDS.
- Số lượng kết nối đến Amazon RDS.
- Trạng thái hoạt động của Amazon EC2 (`StatusCheckFailed`).

Khi hệ thống hoạt động bình thường, các cảnh báo sẽ hiển thị trạng thái **OK**, giúp người quản trị dễ dàng theo dõi tình trạng của các tài nguyên AWS.

![CloudWatch alarms OK](/images/5-Workshop/5.8-Monitoring-Security/23-cloudwatch-alarms-ok.png)

#### Ghi chú

Một số dịch vụ và cấu hình được đơn giản hóa để phù hợp với phạm vi của workshop.

| Chủ đề | Quyết định trong workshop |
|--------|----------------------------|
| AWS Shield | Sử dụng **AWS Shield Standard** (miễn phí) để bảo vệ ALB và Elastic IP trước các cuộc tấn công DDoS phổ biến ở tầng mạng và tầng vận chuyển (Layer 3/Layer 4). |
| Amazon Route 53 | Chưa cấu hình do workshop không sử dụng tên miền riêng. Người dùng truy cập ứng dụng thông qua DNS của Application Load Balancer. |
| AWS WAF | Một số Managed Rules có thể chặn các yêu cầu POST chứa tệp tải lên. Trong quá trình kiểm thử, có thể chuyển các BODY Rules sang chế độ **Count** hoặc tạm thời gỡ liên kết (Disassociate) giữa WAF và ALB. |

#### Section summary

Trong chương này, bạn đã bổ sung các dịch vụ hỗ trợ vận hành và bảo mật cho hệ thống Event Management trên AWS. **AWS Secrets Manager** giúp quản lý an toàn các thông tin nhạy cảm, **AWS Backup** bảo vệ dữ liệu của Amazon RDS thông qua cơ chế sao lưu tự động, **AWS CloudTrail** ghi lại toàn bộ hoạt động trên tài khoản AWS, trong khi **Amazon CloudWatch** giám sát trạng thái hoạt động của các tài nguyên và cảnh báo khi phát hiện sự cố. Những dịch vụ này góp phần hoàn thiện khả năng quan sát (Observability), bảo mật và quản trị hệ thống trong môi trường cloud.
