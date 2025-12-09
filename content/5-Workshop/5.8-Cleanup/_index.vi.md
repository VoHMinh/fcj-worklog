---
title: "Dọn Dẹp Tài Nguyên"
date:
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Tổng Quan

Phần này hướng dẫn bạn dọn dẹp tất cả tài nguyên AWS đã tạo trong workshop này để tránh các khoản phí phát sinh. **Thực hiện theo thứ tự các bước** vì một số tài nguyên phụ thuộc vào tài nguyên khác.

{{% notice warning %}}
**Quan trọng:** Việc xóa tài nguyên không thể hoàn tác. Hãy đảm bảo bạn đã sao lưu bất kỳ dữ liệu nào cần thiết trước khi tiếp tục.
{{% /notice %}}

#### Thời Gian Ước Tính: ~30 phút

---

### Bước 1: Xóa Tài Nguyên CI/CD Pipeline

Đầu tiên, xóa CI/CD pipeline để dừng mọi deployment tự động.

1. Truy cập console **AWS CodePipeline**
2. Chọn pipeline của bạn (ví dụ: `ielts-pipeline`)
3. Click **Delete pipeline** → Xác nhận xóa
4. Truy cập console **AWS CodeBuild**
5. Xóa tất cả build projects liên quan đến workshop
6. Xóa bất kỳ **S3 buckets** nào được sử dụng cho pipeline artifacts

---

### Bước 2: Xóa ECS Services và Cluster

Dừng và xóa tất cả ECS services trước khi xóa cluster.

1. Truy cập console **Amazon ECS**
2. Chọn cluster của bạn (ví dụ: `ielts-cluster`)
3. Vào tab **Services**
4. Với mỗi service:
   - Chọn service
   - Click **Update** → Đặt **Desired tasks** thành `0` → Update
   - Chờ các running tasks dừng lại
   - Click **Delete service** → Xác nhận
5. Sau khi tất cả services đã xóa, quay lại **Clusters**
6. Chọn cluster → **Delete cluster** → Xác nhận

---

### Bước 3: Xóa ECR Repositories

Xóa container images và repositories.

1. Truy cập console **Amazon ECR**
2. Chọn từng repository:
   - `ielts-frontend`
   - `ielts-backend`
3. Click **Delete** → Nhập tên repository để xác nhận

---

### Bước 4: Xóa Tài Nguyên AI Service

Xóa các thành phần AI serverless theo thứ tự:

#### 4.1 Xóa Lambda Functions
1. Truy cập console **AWS Lambda**
2. Xóa từng function:
   - `writing-evaluator`
   - `speaking-evaluator`
   - `flashcard-generator`
   - `evaluation-status`
   - `s3-upload`
3. Chọn function → **Actions** → **Delete** → Xác nhận

#### 4.2 Xóa API Gateway
1. Truy cập console **Amazon API Gateway**
2. Chọn API của bạn (ví dụ: `ielts-ai-api`)
3. Click **Actions** → **Delete API** → Xác nhận

#### 4.3 Xóa SQS Queues
1. Truy cập console **Amazon SQS**
2. Xóa từng queue:
   - `writing-evaluation-queue`
   - `writing-evaluation-dlq`
   - `speaking-evaluation-queue`
   - `speaking-evaluation-dlq`
   - `flashcard-generation-queue`
   - `flashcard-generation-dlq`
3. Chọn queue → **Delete** → Xác nhận

#### 4.4 Xóa DynamoDB Tables
1. Truy cập console **Amazon DynamoDB**
2. Xóa từng table:
   - `evaluations`
   - `flashcard-sets`
3. Chọn table → **Delete** → Xác nhận xóa

---

### Bước 5: Xóa Tài Nguyên Load Balancer

1. Truy cập console **EC2** → **Load Balancers**
2. Chọn ALB của bạn (ví dụ: `ielts-alb`)
3. Click **Actions** → **Delete** → Xác nhận
4. Vào **Target Groups**
5. Xóa tất cả target groups liên quan đến workshop
6. Vào **Listeners** và xóa bất kỳ listeners còn lại

---

### Bước 6: Xóa Tài Nguyên Database

#### 6.1 Xóa RDS Instance
1. Truy cập console **Amazon RDS**
2. Chọn database instance của bạn
3. Click **Actions** → **Delete**
4. Bỏ chọn **Create final snapshot** (nếu không cần)
5. Chọn **I acknowledge...** → **Delete**

{{% notice note %}}
Việc xóa RDS có thể mất 5-10 phút để hoàn thành.
{{% /notice %}}

#### 6.2 Xóa ElastiCache (Redis)
1. Truy cập console **Amazon ElastiCache**
2. Chọn Redis cluster của bạn
3. Click **Delete** → Xác nhận

#### 6.3 Xóa RDS Subnet Group
1. Trong RDS console, vào **Subnet groups**
2. Chọn subnet group của bạn → **Delete**

---

### Bước 7: Xóa S3 Buckets

1. Truy cập console **Amazon S3**
2. Với mỗi bucket đã tạo trong workshop:
   - `ielts-audio-bucket`
   - `ielts-documents-bucket`
   - Bất kỳ pipeline artifact buckets nào
3. Chọn bucket → **Empty** → Xác nhận
4. Sau khi làm trống, chọn bucket → **Delete** → Xác nhận

{{% notice warning %}}
S3 buckets phải được làm trống trước khi có thể xóa.
{{% /notice %}}

---

### Bước 8: Xóa Secrets Manager Secrets

1. Truy cập console **AWS Secrets Manager**
2. Chọn từng secret đã tạo cho workshop
3. Click **Actions** → **Delete secret**
4. Đặt thời gian khôi phục thành **7 ngày** (tối thiểu) hoặc chọn xóa ngay lập tức
5. Xác nhận xóa

---

### Bước 9: Xóa Tài Nguyên CloudWatch

1. Truy cập console **Amazon CloudWatch**
2. Vào **Log groups**
3. Xóa các log groups:
   - `/aws/lambda/writing-evaluator`
   - `/aws/lambda/speaking-evaluator`
   - `/aws/lambda/flashcard-generator`
   - `/ecs/ielts-frontend`
   - `/ecs/ielts-backend`
4. Vào **Alarms** và xóa bất kỳ alarms đã tạo

---

### Bước 10: Xóa VPC và Tài Nguyên Mạng

Xóa tài nguyên mạng theo thứ tự cụ thể này:

#### 10.1 Xóa NAT Gateway
1. Truy cập console **VPC** → **NAT Gateways**
2. Chọn NAT Gateway của bạn
3. Click **Actions** → **Delete NAT gateway** → Xác nhận
4. Chờ trạng thái chuyển thành **Deleted**

#### 10.2 Giải Phóng Elastic IPs
1. Vào **Elastic IPs**
2. Chọn bất kỳ Elastic IPs nào liên kết với NAT Gateway
3. Click **Actions** → **Release Elastic IP addresses** → Xác nhận

#### 10.3 Xóa VPC Endpoints
1. Vào **Endpoints**
2. Chọn tất cả VPC endpoints đã tạo cho workshop
3. Click **Actions** → **Delete VPC endpoints** → Xác nhận

#### 10.4 Xóa Security Groups
1. Vào **Security Groups**
2. Xóa security groups theo thứ tự này (do phụ thuộc):
   - Application security groups trước
   - Database security groups
   - Load balancer security groups
   - **Không xóa** default security group

#### 10.5 Xóa Subnets
1. Vào **Subnets**
2. Chọn tất cả subnets trong workshop VPC của bạn
3. Click **Actions** → **Delete subnet** → Xác nhận

#### 10.6 Xóa Route Tables
1. Vào **Route Tables**
2. Xóa custom route tables (không phải main route table)
3. Chọn route table → **Actions** → **Delete route table**

#### 10.7 Xóa Internet Gateway
1. Vào **Internet Gateways**
2. Chọn IGW → **Actions** → **Detach from VPC** → Xác nhận
3. Chọn lại IGW → **Actions** → **Delete internet gateway** → Xác nhận

#### 10.8 Xóa VPC
1. Vào **Your VPCs**
2. Chọn workshop VPC của bạn
3. Click **Actions** → **Delete VPC** → Xác nhận

---

### Bước 11: Xóa Tài Nguyên IAM

1. Truy cập console **IAM**
2. Vào **Roles** và xóa:
   - `ecsTaskExecutionRole` (nếu được tạo cho workshop này)
   - `ielts-lambda-execution-role`
   - Bất kỳ roles cụ thể nào khác của workshop
3. Vào **Policies** và xóa custom policies đã tạo cho workshop

{{% notice warning %}}
Cẩn thận không xóa tài nguyên IAM được sử dụng bởi các ứng dụng khác.
{{% /notice %}}

---

### Danh Sách Kiểm Tra Xác Minh

Sau khi hoàn thành dọn dẹp, xác minh tất cả tài nguyên đã được xóa:

| Tài Nguyên | Console Dịch Vụ | Trạng Thái |
|----------|-----------------|--------|
| CodePipeline | CodePipeline | ☐ Đã xóa |
| ECS Cluster | ECS | ☐ Đã xóa |
| ECR Repositories | ECR | ☐ Đã xóa |
| Lambda Functions | Lambda | ☐ Đã xóa |
| API Gateway | API Gateway | ☐ Đã xóa |
| SQS Queues | SQS | ☐ Đã xóa |
| DynamoDB Tables | DynamoDB | ☐ Đã xóa |
| Load Balancer | EC2 | ☐ Đã xóa |
| RDS Instance | RDS | ☐ Đã xóa |
| ElastiCache | ElastiCache | ☐ Đã xóa |
| S3 Buckets | S3 | ☐ Đã xóa |
| Secrets | Secrets Manager | ☐ Đã xóa |
| CloudWatch Logs | CloudWatch | ☐ Đã xóa |
| NAT Gateway | VPC | ☐ Đã xóa |
| VPC | VPC | ☐ Đã xóa |
| IAM Roles | IAM | ☐ Đã xóa |

---

### Xác Minh Chi Phí

Để đảm bảo không có khoản phí bất ngờ:

1. Vào console **AWS Billing**
2. Kiểm tra **Bills** cho tháng hiện tại
3. Xem **Cost Explorer** để xác minh không còn tài nguyên hoạt động
4. Thiết lập **Budget alert** nếu bạn có kế hoạch tiếp tục sử dụng AWS

{{% notice tip %}}
Chờ 24-48 giờ và kiểm tra lại billing dashboard để xác nhận tất cả tài nguyên đã được dọn dẹp và không có khoản phí nào đang phát sinh.
{{% /notice %}}


