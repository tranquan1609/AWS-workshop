---
title: "Dọn dẹp tài nguyên"
date: 2026-07-01
weight: 9
chapter: false
pre: " <b> 5.9. </b> "
---

Sau khi hoàn thành workshop và lưu lại đầy đủ hình ảnh phục vụ báo cáo, bạn nên dọn dẹp các tài nguyên AWS không còn sử dụng để tránh phát sinh chi phí ngoài ý muốn. Một số dịch vụ như **Application Load Balancer (ALB)**, **AWS WAF** hoặc **Amazon RDS** vẫn tiếp tục tính phí ngay cả khi ứng dụng không có lưu lượng truy cập.

Trong chương này, chúng ta thực hiện việc xóa tài nguyên theo trình tự phù hợp nhằm hạn chế lỗi phụ thuộc giữa các dịch vụ và đảm bảo môi trường AWS được dọn dẹp an toàn.

#### Vì sao cần dọn dẹp tài nguyên?

Nhiều dịch vụ trên AWS được tính phí theo thời gian tồn tại hoặc theo mức sử dụng. Nếu các tài nguyên vẫn được giữ lại sau khi kết thúc workshop, tài khoản AWS có thể tiếp tục phát sinh chi phí mặc dù hệ thống không còn được sử dụng.

Vì vậy, sau khi hoàn tất quá trình thực hành và báo cáo, bạn nên kiểm tra và xóa những tài nguyên không còn cần thiết.

#### Thứ tự dọn dẹp được khuyến nghị

Để tránh lỗi do các dịch vụ còn phụ thuộc lẫn nhau, hãy thực hiện việc dọn dẹp theo thứ tự sau:

1. Gỡ liên kết (Disassociate) và xóa **AWS WAF Web ACL** `dacswebsk-waf`.
2. Xóa **Application Load Balancer** `dacswebsk-alb` và **Target Group** `dacswebsk-tg`.
3. Vô hiệu hóa hoặc xóa các **Amazon EventBridge Rules**, sau đó xóa các **AWS Lambda Functions** và **Amazon API Gateway**.
4. Xóa các **Amazon CloudWatch Alarms**.
5. Dừng hoặc xóa EC2 Instance `dacswebsk-app`, đồng thời giải phóng (Release) **Elastic IP**.
6. *(Tùy chọn)* Tạo Snapshot cuối cùng của Amazon RDS trước khi xóa cơ sở dữ liệu `dacswebsk-db`.
7. Xóa toàn bộ đối tượng trong các **Amazon S3 Buckets**, sau đó xóa Bucket *(có thể giữ lại Bucket chứa CloudTrail Logs nếu vẫn cần phục vụ kiểm tra)*.
8. Xóa **AWS Secrets Manager Secret**, **AWS Backup Assignment** và **AWS CloudTrail Trail**.
9. Cuối cùng, xóa các **Security Groups**, **Subnets**, **Internet Gateway** và **Amazon VPC**.

#### Giữ lại một phần tài nguyên (tùy chọn)

Nếu bạn muốn tiếp tục phát triển hoặc trình diễn hệ thống sau workshop, không nhất thiết phải xóa toàn bộ tài nguyên.

Có thể cân nhắc giữ lại:

- Amazon EC2.
- Amazon RDS.
- Amazon S3.

Trong khi đó, **Application Load Balancer** và **AWS WAF** thường là những dịch vụ phát sinh chi phí đáng kể theo thời gian, vì vậy nên ưu tiên xóa trước nếu chưa có nhu cầu sử dụng.

#### Kiểm tra sau khi dọn dẹp

Sau khi hoàn tất quá trình xóa tài nguyên, có thể sử dụng **AWS CLI** để kiểm tra lại trạng thái của một số dịch vụ.

```powershell
aws ec2 describe-instances --region ap-southeast-1 --filters Name=tag:Name,Values=dacswebsk-app

aws rds describe-db-instances --region ap-southeast-1 --db-instance-identifier dacswebsk-db

aws elbv2 describe-load-balancers --region ap-southeast-1 --names dacswebsk-alb
```

Nếu quá trình dọn dẹp thành công, các lệnh trên sẽ trả về kết quả **Not Found**, không có dữ liệu hoặc danh sách tài nguyên rỗng, tùy theo từng dịch vụ.

#### Section summary

Trong chương cuối cùng, bạn đã thực hiện quy trình dọn dẹp các tài nguyên AWS được tạo trong workshop. Việc xóa tài nguyên sau khi hoàn thành thực hành không chỉ giúp tránh phát sinh chi phí ngoài ý muốn mà còn duy trì môi trường AWS gọn gàng và dễ quản lý. Đây cũng là một bước quan trọng trong quy trình quản trị tài nguyên trên nền tảng đám mây và là thực hành được AWS khuyến nghị sau mỗi buổi thử nghiệm hoặc workshop.
