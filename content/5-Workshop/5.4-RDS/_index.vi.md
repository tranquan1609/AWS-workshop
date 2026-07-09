---
title: "Amazon RDS (SQL Server)"
date: 2026-07-01
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Bây giờ chúng ta triển khai **Amazon Relational Database Service (Amazon RDS)** sử dụng **Microsoft SQL Server Express Edition** để thay thế cơ sở dữ liệu SQL Server cài đặt cục bộ. Việc sử dụng Amazon RDS giúp giảm đáng kể công sức quản trị cơ sở dữ liệu, đồng thời cung cấp khả năng sao lưu, giám sát và mở rộng dễ dàng hơn so với việc tự quản lý máy chủ cơ sở dữ liệu.

#### Vì sao sử dụng Amazon RDS for SQL Server?

Ứng dụng **Event Management (DACSWEBSK)** được xây dựng trên nền tảng **ASP.NET Core**, sử dụng **Entity Framework Core** kết hợp với **Microsoft SQL Server** và **ASP.NET Identity**. Vì vậy, việc lựa chọn **Amazon RDS for SQL Server Express Edition** cho phép giữ nguyên cấu trúc cơ sở dữ liệu, các EF Core Migrations và mã nguồn hiện có. Khi triển khai lên AWS, ứng dụng chỉ cần cập nhật chuỗi kết nối (Connection String) mà không cần thay đổi logic xử lý dữ liệu.

#### Tạo cơ sở dữ liệu

Tạo một phiên bản Amazon RDS với các thông số sau:

| Mục | Giá trị |
|-----|---------|
| Engine | Microsoft SQL Server **Express Edition** |
| DB identifier | `dacswebsk-db` |
| Instance class | `db.t3.micro` |
| Storage | 20 GB (gp2 hoặc gp3) |
| Master username | `admin` |
| Region / AZ | `ap-southeast-1` |

Sau khi quá trình khởi tạo hoàn tất, trạng thái của cơ sở dữ liệu sẽ chuyển sang **Available**, cho biết RDS đã sẵn sàng tiếp nhận kết nối từ ứng dụng.

![RDS Available](/images/5-Workshop/5.4-RDS/06-rds-databases-available.png)

#### Connectivity

Sau khi tạo thành công cơ sở dữ liệu, ghi nhận các thông tin kết nối để sử dụng trong ứng dụng.

- Endpoint: `dacswebsk-db.xxxxx.ap-southeast-1.rds.amazonaws.com`
- Port: **1433**
- Security Group: `dacswebsk-rds-sg`

Các thông tin này sẽ được sử dụng khi cấu hình chuỗi kết nối (Connection String) cho ứng dụng ASP.NET Core.

![RDS connectivity / endpoint](/images/5-Workshop/5.4-RDS/07-rds-connectivity-endpoint.png)

#### Cấu hình Security Group

Để ứng dụng có thể kết nối đến cơ sở dữ liệu, cấu hình Security Group của Amazon RDS cho phép lưu lượng TCP trên cổng **1433** từ các nguồn sau:

- CIDR của VPC: `10.0.0.0/16`
- Elastic IP của máy chủ EC2 đang chạy ứng dụng
- Địa chỉ IP của máy quản trị (chỉ sử dụng tạm thời trong quá trình migration hoặc kiểm thử)
- Rule phục vụ môi trường demo cho Lambda chatbot *(nên giới hạn hoặc loại bỏ khi triển khai production)*

![RDS SG inbound](/images/5-Workshop/5.4-RDS/08-rds-security-group-inbound-1433.png)

#### Tích hợp với ứng dụng

Sau khi cơ sở dữ liệu đã sẵn sàng, cập nhật **DefaultConnection** trong cấu hình của ứng dụng. Trong môi trường triển khai thực tế, chuỗi kết nối sẽ được lưu trữ và quản lý bằng **AWS Secrets Manager** nhằm tăng cường tính bảo mật.

Tiếp theo, thực hiện Entity Framework Core Migration để tạo cơ sở dữ liệu trên Amazon RDS.

```powershell
dotnet ef database update
```

Sau khi migration hoàn tất, có thể kiểm tra hoạt động của hệ thống bằng cách đăng ký một tài khoản mới và tạo sự kiện trên giao diện quản trị để xác nhận dữ liệu đã được lưu thành công vào Amazon RDS.

#### Section summary

Bạn đã triển khai thành công **Amazon RDS for SQL Server Express Edition** làm cơ sở dữ liệu cho hệ thống Event Management. Toàn bộ dữ liệu quan hệ, bao gồm thông tin sự kiện, người tham gia và các bảng của **ASP.NET Identity**, được lưu trữ trên Amazon RDS. Khi trạng thái của cơ sở dữ liệu là **Available** và Security Group đã cho phép kết nối qua cổng **1433**, tầng cơ sở dữ liệu đã sẵn sàng để ứng dụng trên Amazon EC2 truy cập và sử dụng.
