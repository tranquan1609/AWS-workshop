---
title: "Worklog Tuần 12"
date: 2026-07-06
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

**Tuần 12:** 06/07/2026 – 09/07/2026 *(đến Thứ 5)*

### Mục tiêu tuần 12

- Hoàn thiện tầng vận hành và bảo mật: Secrets Manager, CloudWatch, CloudTrail, AWS Backup.
- Kiểm thử toàn diện (end-to-end) các luồng nghiệp vụ của DACSWEBSK trên AWS.
- Hoàn thiện tài liệu Workshop, sơ đồ kiến trúc và chuẩn bị báo cáo FCJ.

### Các nhiệm vụ trong tuần

| Thứ | Nhiệm vụ | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Lưu connection string và cấu hình nhạy cảm vào AWS Secrets Manager (`dacswebskproduction`).<br>- Cấu hình CloudWatch Alarms cho EC2 và RDS; bật CloudTrail `dacswebsk-trail` ghi audit log.<br>- Tạo AWS Backup plan `dacswebsk-backup-plan` sao lưu RDS theo lịch.<br>- Rà soát IAM Role, Security Group và nguyên tắc least privilege. | 06/07/2026 | 06/07/2026 | [5.8 — Monitoring & bảo mật](../../5-Workshop/5.8-Monitoring-Security/) |
| 3 | - Kiểm thử end-to-end: đăng ký/đăng nhập, quản trị sự kiện, upload ảnh/video lên S3.<br>- Kiểm tra email SES (xác nhận, chứng chỉ), Lambda job (cập nhật trạng thái, cấp chứng chỉ tự động).<br>- Kiểm tra chatbot qua API Gateway (`POST /chat`), truy cập ứng dụng qua ALB.<br>- Ghi nhận và xử lý các lỗi phát sinh (WAF chặn form, RDS timeout, SES sandbox). | 07/07/2026 | 07/07/2026 | [5.1 — Tổng quan](../../5-Workshop/5.1-Workshop-overview/)<br>[5.7 — Lambda & EventBridge](../../5-Workshop/5.7-Lambda-EventBridge/) |
| 4 | - Hoàn thiện tài liệu Workshop (Chương 5): 9 phần từ chuẩn bị đến dọn dẹp.<br>- Cập nhật sơ đồ kiến trúc DACSWEBSK trong Proposal, đảm bảo khớp triển khai thực tế.<br>- Đối chiếu danh sách 15 dịch vụ AWS với tài nguyên đã tạo trên Console. | 08/07/2026 | 08/07/2026 | [5 — Workshop](../../5-Workshop/)<br>[2 — Bản đề xuất](../../2-Proposal/) |
| 5 | - Tổng hợp kết quả triển khai: URL demo (ALB, API Gateway), danh sách tài nguyên AWS.<br>- Rà soát chi phí và khuyến nghị tối ưu (instance nhỏ, tắt tài nguyên khi không dùng).<br>- Chuẩn bị demo và báo cáo kết thúc thực tập FCJ.<br>- Lập kế hoạch dọn dẹp tài nguyên hoặc giữ lại môi trường demo (theo hướng dẫn 5.9). | 09/07/2026 | 09/07/2026 | [5.9 — Dọn dẹp](../../5-Workshop/5.9-Cleanup/)<br>[2 — Bản đề xuất](../../2-Proposal/) |

### Kết quả đạt được tuần 12

- Hoàn thiện tầng vận hành với Secrets Manager, CloudWatch Alarms, CloudTrail và AWS Backup; tăng cường bảo mật và khả năng phục hồi dữ liệu.
- Kiểm thử thành công các luồng chính: người dùng, quản trị viên, upload S3, email SES, job Lambda/EventBridge và chatbot API Gateway.
- Hoàn thiện bộ tài liệu Workshop đầy đủ kèm screenshot thực tế từ AWS Console, phục vụ báo cáo FCAJ.
- Cập nhật sơ đồ kiến trúc và Proposal khớp với hệ thống đã triển khai (WAF → ALB → EC2 → RDS; S3/SES; Lambda/EventBridge/API Gateway).
- Hệ thống DACSWEBSK chạy ổn định trên AWS region `ap-southeast-1`, sẵn sàng demo và báo cáo kết thúc chương trình thực tập First Cloud Journey.

### Thông tin triển khai (tham chiếu)

| Thành phần | Endpoint / tài nguyên |
| --- | --- |
| Application Load Balancer | `http://dacswebsk-alb-1804460399.ap-southeast-1.elb.amazonaws.com` |
| EC2 (Elastic IP) | `http://18.140.214.160` |
| Chatbot API | `https://tcunvi9s86.execute-api.ap-southeast-1.amazonaws.com/chat` |
| Region | `ap-southeast-1` (Singapore) |
