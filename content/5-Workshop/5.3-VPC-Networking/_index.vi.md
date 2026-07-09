---
title: "VPC & mạng"
date: 2026-07-01
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

Trong chương này, chúng ta xây dựng hạ tầng mạng cho hệ thống Event Management bằng **Amazon Virtual Private Cloud (Amazon VPC)**. Kiến trúc mạng được thiết kế theo mô hình nhiều tầng (Multi-tier Architecture), sử dụng các Public Subnets để tiếp nhận lưu lượng từ Internet và chuẩn bị Private Subnets cho các tài nguyên nội bộ khi cần mở rộng trong tương lai.

Bên cạnh đó, các **Security Groups** được cấu hình để kiểm soát lưu lượng giữa các thành phần của hệ thống, góp phần nâng cao tính bảo mật và tuân theo nguyên tắc **Least Privilege**.

#### Tạo VPC

Tạo VPC **`dacswebsk-vpc`** với các thông số sau:

| Mục | Giá trị |
|-----|---------|
| IPv4 CIDR | `10.0.0.0/16` |
| DNS resolution | Enabled |
| DNS hostnames | Enabled |
| Internet Gateway | `dacswebsk-igw` |

Sau khi hoàn tất, VPC đóng vai trò là mạng riêng chứa toàn bộ tài nguyên được triển khai trong workshop.

![Resource map VPC](/images/5-Workshop/5.3-VPC-Networking/03-vpc-dacswebsk-resource-map.png)

#### Subnets

Tiếp theo, tạo các Subnets theo bảng dưới đây:

| Subnet | AZ | CIDR | Vai trò |
|--------|----|------|---------|
| `dacswebsk-subnet-public1-ap-southeast-1a` | `ap-southeast-1a` | `10.0.0.0/20` | EC2 và ALB |
| `dacswebsk-subnet-public2-ap-southeast-1b` | `ap-southeast-1b` | `10.0.16.0/20` | ALB tại Availability Zone thứ hai |
| `dacswebsk-subnet-private1-ap-southeast-1a` | `ap-southeast-1a` | `10.0.128.0/20` | Tài nguyên nội bộ (tùy chọn) |

Hai Public Subnets được triển khai trên hai Availability Zones nhằm hỗ trợ khả năng chịu lỗi cho **Application Load Balancer (ALB)**. Public Subnets sử dụng Route Table **`dacswebsk-rtb-public`**, trong đó tuyến mặc định (`0.0.0.0/0`) được định tuyến đến **Internet Gateway** để cho phép truy cập từ Internet.

![Subnets](/images/5-Workshop/5.3-VPC-Networking/04-vpc-subnets-public-private.png)

#### Security Groups

Sau khi hoàn thành cấu hình mạng, tạo các Security Groups để kiểm soát lưu lượng giữa các thành phần của hệ thống.

| Security Group | Mục đích |
|----------------|----------|
| `dacswebsk-alb-sg` | Cho phép lưu lượng HTTP (cổng 80) từ Internet đến ALB |
| `dacswebsk-ec2-sg` | Cho phép HTTP từ ALB Security Group và SSH/RDP từ địa chỉ IP quản trị |
| `dacswebsk-rds-sg` | Cho phép kết nối MSSQL (cổng 1433) từ EC2, Lambda hoặc các tài nguyên trong VPC khi cần |
| `dacswebsk-lambda-sg` | Cho phép Lambda thực hiện các kết nối outbound khi truy cập tài nguyên trong VPC |

Việc phân tách Security Groups theo từng thành phần giúp kiểm soát lưu lượng mạng rõ ràng hơn, đồng thời hạn chế quyền truy cập không cần thiết giữa các dịch vụ.

![Security groups](/images/5-Workshop/5.3-VPC-Networking/05-vpc-security-groups.png)

#### Section summary

Trong chương này, bạn đã xây dựng hạ tầng mạng cơ bản cho hệ thống bằng Amazon VPC, bao gồm VPC, các Public và Private Subnets, Internet Gateway, Route Table và Security Groups. Kiến trúc mạng được thiết kế theo mô hình nhiều tầng, với ALB hoạt động trên hai Availability Zones nhằm tăng tính sẵn sàng, trong khi các Security Groups kiểm soát lưu lượng truy cập theo nguyên tắc **Least Privilege**, đảm bảo luồng kết nối giữa **ALB → EC2 → RDS** được bảo vệ an toàn.
