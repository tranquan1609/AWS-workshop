---
title: "Các bài blog đã đăng"
date: 2026-06-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Trong quá trình thực tập **First Cloud Journey (FCJ)**, nhóm chúng mình đã đăng các bài blog chia sẻ kinh nghiệm học AWS trên nhóm [AWS Study Group FCJ](https://www.facebook.com/groups/awsstudygroupfcj/). Các bài viết được viết theo góc nhìn người mới bắt đầu, tập trung vào những khái niệm thực sự hữu ích khi triển khai dự án thực tế — trong đó có workshop **Event Management (DACSWEBSK)**.

Trang này giới thiệu **bối cảnh, mục đích và điểm chính** của từng bài. Nội dung đầy đủ nằm ở từng trang blog con bên dưới.

---

### [Blog 1 — IAM User & IAM Policy](3.1-Blog1/)

**Chủ đề:** Hai khái niệm nền tảng mà bất kỳ ai học AWS cũng nên nắm vững.

Khi mới tạo tài khoản AWS, nhiều người — kể cả mình — thường dùng **Root Account** cho mọi thao tác vì đó là tài khoản đầu tiên AWS cung cấp. Bài viết giải thích vì sao đây là sai lầm phổ biến: Root có toàn quyền, không bị giới hạn bởi IAM Policy, và nếu lộ credential có thể dẫn đến xóa tài nguyên hoặc phát sinh chi phí ngoài ý muốn.

Bài blog đi sâu vào cách AWS khuyến nghị vận hành an toàn:

- Tạo **IAM User** riêng cho từng người (username, MFA, Access Key riêng).
- Dùng **IAM Policy** để phân quyền theo vai trò — ví dụ thực tập sinh chỉ được xem/start/stop EC2, không được xóa RDS hay truy cập Billing.
- Áp dụng **Principle of Least Privilege** — chỉ cấp đúng quyền cần thiết.
- So sánh cách gắn Policy vào User, Group, Role; phân biệt **Managed Policy** và **Inline Policy**.

**Liên hệ với dự án:** Trong workshop DACSWEBSK, mình tạo IAM User `dacswebsk-dev` với các managed policies phù hợp thay vì dùng Root [mục 5.2 — Chuẩn bị IAM & CLI](../5-workshop/5.2-prerequisite/).

**Nguồn:** [Bài đăng Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206918726739754/)

---

### [Blog 2 — Những tài liệu miễn phí giúp học AWS](3.2-Blog2/)

**Chủ đề:** Hành trình học AWS sẽ dễ dàng hơn khi bạn có trong tay những tài liệu phù hợp.

Người mới thường bị “choáng” bởi hàng nghìn video, blog và khóa học sau một lần tìm kiếm. Bài viết chia sẻ cách mình thay đổi từ học lung tung nhiều dịch vụ (EC2 → Lambda → EKS…) sang **lộ trình có hệ thống**: IAM → VPC → EC2 → S3 → RDS, giúp hiểu cách các dịch vụ kết hợp trong một hệ thống thực tế.

Bài blog tổng hợp các nguồn miễn phí mình dùng nhiều nhất:

| Nguồn | Vai trò |
|-------|---------|
| **AWS Skill Builder** | Learning Plan, quiz, hands-on lab cho người mới |
| **AWS Documentation** | Nguồn chính xác nhất khi cần xác minh tính năng |
| **AWS Well-Architected** | Tư duy thiết kế hệ thống đúng (Security, Reliability, Cost) |
| **YouTube** (AWS Events, re:Invent) | Học qua demo — kèm thực hành trên Console |
| **AWS Free Tier** | Tạo tài nguyên thật, xóa sau khi xong |
| **GitHub** (AWS Samples, Awesome AWS) | Tham khảo triển khai thực tế |

Bài cũng liệt kê **sai lầm thường gặp**: đọc nhiều thực hành ít, học quá nhiều dịch vụ cùng lúc, ngại Documentation, quên xóa tài nguyên sau lab.

**Liên hệ với dự án:** Lộ trình IAM → VPC → EC2 → S3 → RDS.

**Nguồn:** [Bài đăng Facebook](https://www.facebook.com/groups/awsstudygroupfcj/permalink/2206923336739293/)

---

### [Blog 3 — Thiết kế kiến trúc AWS](3.3-Blog3/)

**Chủ đề:** Lần đầu thiết kế kiến trúc AWS và những thứ mình từng đặt sai vị trí.

Bài viết chia sẻ trải nghiệm của sinh viên IT khi vẽ kiến trúc cho hệ thống web trong kỳ thực tập: ban đầu tưởng chỉ cần kéo icon EC2, RDS, S3, ALB, NAT Gateway… và nối lại là xong, nhưng thực tế **biết dịch vụ làm gì chưa chắc đã biết nó thuộc lớp nào trên sơ đồ**.

Các câu hỏi mình từng vướng:

- S3 và SES có nên vẽ **bên trong VPC** không?
- Internet Gateway có nằm trong **Public Subnet** không?
- ALB thuộc cấp **Region**, **VPC** hay **Availability Zone**?
- **IAM Role** nên đặt ở đâu trên sơ đồ?

Bài tổng hợp cách phân chia đúng hơn:

| Thành phần | Vị trí trên kiến trúc |
|------------|------------------------|
| Internet Gateway | Gắn VPC, **không** nằm trong subnet |
| NAT Gateway | Thường ở **Public Subnet** |
| EC2 | **Private Subnet** nếu không cần Internet trực tiếp |
| RDS | **Private Subnet**; Multi-AZ khi cần HA |
| S3, SES | Dịch vụ cấp **Region**, ngoài VPC |
| IAM | Dịch vụ **global** |
| ALB | Thuộc VPC, trải nhiều **AZ** qua subnet |

Bài cũng nhấn mạnh: kiến trúc tốt không phải càng nhiều dịch vụ càng tốt — đồ án hoặc hệ thống nhỏ không nhất thiết cần đủ CloudFront, WAF, Auto Scaling, NAT… nếu chưa có nhu cầu thực tế.

**Liên hệ với dự án:** Khi thiết kế kiến trúc **DACSWEBSK**, mình áp dụng các lớp Region → VPC → AZ → Subnet; workshop triển khai VPC `dacswebsk-vpc`, ALB multi-AZ, RDS trong VPC — xem [Chương 5 — Workshop](../5-workshop/).

**Nguồn:** [Bài đăng Facebook](https://www.facebook.com/share/p/1CfFENFe8k/)
