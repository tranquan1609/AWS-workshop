---
title: "Chuẩn bị — IAM & CLI"
date: 2026-07-01
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Trong chương này, chúng ta chuẩn bị môi trường AWS để sẵn sàng triển khai các dịch vụ trong workshop. Các tài nguyên được tạo trong cùng một Region nhằm đảm bảo tính nhất quán; đồng thời workshop sử dụng **IAM User** thay vì tài khoản Root để tuân thủ khuyến nghị bảo mật của AWS. **AWS CLI** cũng được cấu hình để hỗ trợ quản lý và triển khai tài nguyên qua dòng lệnh.

#### Region

Toàn bộ tài nguyên trong workshop được triển khai tại **Asia Pacific (Singapore)** (`ap-southeast-1`). Việc dùng thống nhất một Region giúp các dịch vụ kết nối với nhau ổn định và tránh lỗi do khác biệt giữa các Region.

#### Tạo IAM User

Để đảm bảo an toàn, workshop sử dụng IAM User thay cho tài khoản Root trong quá trình triển khai.

1. Truy cập **IAM → Users → Create user**.
2. Tạo các người dùng sau:
   - **`dacswebsk-dev`**: IAM User chính dùng để quản lý và triển khai tài nguyên qua AWS CLI hoặc SDK (không bật **AWS Management Console access**).
   - **`dacswebsk-ses-smtp`** *(tùy chọn)*: dùng khi cấu hình xác thực SMTP cho Amazon SES.
3. Gắn trực tiếp các **AWS Managed Policies** sau cho người dùng **`dacswebsk-dev`**:

```
AmazonAPIGatewayAdministrator
AmazonEC2FullAccess
AmazonRDSFullAccess
AmazonS3FullAccess
AmazonSESFullAccess
AmazonVPCFullAccess
AWSLambda_FullAccess
CloudWatchFullAccessV2
SecretsManagerReadWrite
```

![Danh sách IAM users](/images/5-Workshop/5.2-Prerequisite/01-iam-users-list.png)

![Quyền của dacswebsk-dev](/images/5-Workshop/5.2-Prerequisite/02-iam-user-permissions-dacswebsk-dev.png)

#### Cấu hình AWS CLI

Sau khi tạo IAM User, cấu hình AWS CLI bằng Access Key của người dùng **`dacswebsk-dev`**.

```powershell
aws configure
```

Nhập các thông tin sau:

```
Default region name: ap-southeast-1
Default output format: json
```

Sau khi hoàn tất, kiểm tra tài khoản đang sử dụng:

```powershell
aws sts get-caller-identity
```

Kết quả mong đợi hiển thị ARN của IAM User, ví dụ:

```
arn:aws:iam::185529490133:user/dacswebsk-dev
```

Nếu lệnh trả về đúng ARN của người dùng vừa tạo, AWS CLI đã được cấu hình thành công và sẵn sàng cho các chương tiếp theo.

#### Section summary

Trong chương này, chúng ta đã chuẩn bị môi trường AWS cho toàn bộ workshop: tạo IAM User với các quyền cần thiết và cấu hình AWS CLI để xác thực với tài khoản AWS. Ở các chương tiếp theo, mọi thao tác triển khai sẽ dùng IAM User này. Các thông tin nhạy cảm như Access Key hoặc chuỗi kết nối cơ sở dữ liệu sẽ không lưu trực tiếp trong mã nguồn mà được quản lý qua **AWS Secrets Manager**.
