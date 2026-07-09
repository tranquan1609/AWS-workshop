---
title: "Thiết kế kiến trúc AWS"
date: 2026-06-25
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# Lần đầu thiết kế kiến trúc AWS và những thứ mình từng đặt sai vị trí

**Nguồn gốc:** [Bài đăng trên AWS Study Group FCJ](https://www.facebook.com/share/p/1CfFENFe8k/)

Chào những anh chị admin và các bạn trong cộng đồng AWS, mình là sinh viên năm cuối ngành CNTT và mình bắt đầu tìm hiểu về AWS trong quá trình thực tập để thiết kế kiến trúc cho một hệ thống web.

Ban đầu mình nghĩ việc vẽ kiến trúc AWS khá đơn giản: có EC2, RDS, S3, ALB, NAT Gateway... thì cứ kéo icon vào sơ đồ rồi nối lại với nhau là xong. Nhưng càng làm mới càng nhận ra một vấn đề đó là: **biết dịch vụ dùng để làm gì chưa chắc đã biết nó nên nằm ở đâu trong kiến trúc**.

Ví dụ, lúc đầu mình từng phân vân không biết Amazon S3 và SES có nên đặt bên trong VPC hay không, Internet Gateway có nằm trong Public Subnet không, ALB nên nằm ở cấp Region, VPC hay Availability Zone, rồi IAM Role thì nên vẽ ở đâu cho hợp lý.

Sau một thời gian tìm hiểu, mình mới dần hiểu được cách phân chia:

- **Internet Gateway** được gắn với VPC nhưng **không nằm trong Public Subnet**.
- **NAT Gateway** thường được đặt trong **Public Subnet** để các tài nguyên ở Private Subnet có thể truy cập Internet theo chiều outbound.
- **EC2** thường được đặt trong **Private Subnet** nếu không cần truy cập trực tiếp từ Internet.
- **RDS** nên nằm trong **Private Subnet** và triển khai **Multi-AZ** nếu hệ thống yêu cầu tính sẵn sàng cao.
- **S3** và **SES** là các dịch vụ AWS cấp **Region**, không nằm trực tiếp bên trong VPC.
- **IAM** là dịch vụ **global**, vì vậy không thuộc VPC hay một Region cụ thể.
- **Application Load Balancer** thuộc VPC và có thể trải trên nhiều **Availability Zone** thông qua các subnet được cấu hình.

Điều mình thấy thú vị nhất là khi học AWS, việc hiểu từng dịch vụ riêng lẻ chỉ là bước đầu. Quan trọng hơn là hiểu ranh giới của **AWS Cloud**, **Region**, **VPC**, **Availability Zone** và **Subnet**, vì khi hiểu được các lớp này thì việc đặt các thành phần vào sơ đồ sẽ logic hơn rất nhiều.

Một điều nữa mình nhận ra là kiến trúc AWS không phải cứ thêm nhiều dịch vụ là tốt. Một hệ thống nhỏ hoặc đồ án sinh viên không nhất thiết phải có đầy đủ CloudFront, WAF, Auto Scaling, Multi-AZ, NAT Gateway hay hàng loạt dịch vụ khác nếu không có nhu cầu thực tế. Kiến trúc tốt nên giải quyết đúng bài toán, dễ hiểu, có khả năng mở rộng và cân đối với chi phí.

Hiện tại mình vẫn đang trong quá trình học và hoàn thiện kiến trúc cho dự án của mình. Có thể vẫn còn nhiều điểm chưa chính xác, nên mình rất mong nhận được góp ý từ các anh chị và các bạn có kinh nghiệm về AWS.
