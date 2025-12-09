---
title: "Điều Kiện Tiên Quyết"
date:
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Tổng Quan

Trước khi bắt đầu workshop này, bạn cần chuẩn bị môi trường với các công cụ, tài khoản và cấu hình cần thiết. Phần này bao gồm tất cả điều kiện tiên quyết cần thiết để triển khai thành công hạ tầng Hệ Thống Tự Học IELTS.

#### Yêu Cầu Tài Khoản AWS

**1. Thiết Lập Tài Khoản AWS**

Bạn cần một tài khoản AWS với:
- **Administrator Access** hoặc IAM user với đủ quyền
- Quyền truy cập để tạo resources trong region ưa thích (khuyến nghị: `ap-southeast-1` Singapore)
- Billing alerts được cấu hình để monitor chi phí

**2. Quyền IAM Cần Thiết**

IAM user/role của bạn cần có quyền cho:
- Amazon VPC (full access)
- Amazon ECS (full access)
- Amazon ECR (full access)
- Amazon RDS (full access)
- Amazon ElastiCache (full access)
- Amazon S3 (full access)
- Amazon CloudFront (full access)
- Amazon API Gateway (full access)
- Amazon SQS (full access)
- AWS Lambda (full access)
- Amazon DynamoDB (full access)
- Amazon Bedrock (full access)
- AWS IAM (limited - để tạo service roles)
- AWS Secrets Manager (full access)
- AWS WAF (full access)
- Amazon CloudWatch (full access)
- AWS CodePipeline (full access)
- AWS CodeBuild (full access)
- Amazon Route 53 (full access)
- AWS Certificate Manager (full access)

{{% notice tip %}}
Cho mục đích học tập, bạn có thể sử dụng managed policy `AdministratorAccess`. Cho môi trường production, hãy tuân theo nguyên tắc least privilege.
{{% /notice %}}

#### Công Cụ Phát Triển Local

**1. AWS CLI**

Cài đặt và cấu hình AWS CLI v2:

```bash
# Windows (PowerShell)
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi

# macOS
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# Linux
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Cấu hình AWS CLI:
```bash
aws configure
# Nhập Access Key ID, Secret Access Key, Region (ap-southeast-1), Output format (json)
```

Xác minh cài đặt:
```bash
aws --version
aws sts get-caller-identity
```

**2. Docker**

Cài đặt Docker Desktop cho container builds:
- **Windows/macOS**: Tải từ [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- **Linux**: Theo hướng dẫn cài đặt chính thức

Xác minh cài đặt:
```bash
docker --version
docker run hello-world
```

**3. Git**

Cài đặt Git cho version control:
```bash
# Windows
winget install Git.Git

# macOS
brew install git

# Linux (Ubuntu/Debian)
sudo apt-get install git
```

Xác minh cài đặt:
```bash
git --version
```

**4. Node.js (cho Frontend)**

Cài đặt Node.js 18+ cho phát triển Next.js:
```bash
# Sử dụng nvm (khuyến nghị)
nvm install 18
nvm use 18

# Xác minh
node --version
npm --version
```

**5. Java & Maven (cho Backend)**

Cài đặt Java 17+ và Maven cho Spring Boot:
```bash
# Windows (sử dụng Chocolatey)
choco install openjdk17 maven

# macOS
brew install openjdk@17 maven

# Linux (Ubuntu/Debian)
sudo apt-get install openjdk-17-jdk maven
```

Xác minh cài đặt:
```bash
java --version
mvn --version
```

#### Điều Kiện Domain và SSL

**1. Tên Miền**

Cho workshop này, bạn cần:
- Tên miền đã đăng ký (ví dụ: `bughunters.site`)
- Quyền truy cập quản lý DNS (Route 53 hoặc DNS provider bên ngoài)

{{% notice info %}}
Nếu bạn không có domain, bạn có thể bỏ qua các phần Route 53 và ACM và truy cập ứng dụng trực tiếp qua ALB DNS name.
{{% /notice %}}

**2. Route 53 Hosted Zone**

Nếu sử dụng Route 53 cho DNS:
1. Vào Route 53 Console
2. Tạo Hosted Zone cho domain của bạn
3. Cập nhật nameservers của domain registrar thành nameservers của Route 53

#### API Keys Bên Thứ Ba

**1. Google Gemini API Key**

Cho AI-powered assessments và smart query generation:
1. Truy cập [Google AI Studio](https://aistudio.google.com/)
2. Đăng nhập bằng tài khoản Google
3. Tạo API key
4. Lưu API key một cách bảo mật (bạn sẽ lưu trong Secrets Manager sau)

{{% notice warning %}}
Giữ API keys của bạn an toàn. Không bao giờ commit chúng vào version control.
{{% /notice %}}

**2. OAuth Credentials (Tùy Chọn)**

Cho social login (Google, Facebook):
- **Google OAuth**: Tạo credentials trong [Google Cloud Console](https://console.cloud.google.com/)
- **Facebook OAuth**: Tạo app trong [Facebook Developers](https://developers.facebook.com/)

#### Source Code Dự Án

Clone các project repositories:

```bash

git clone https://github.com/Ojt-BugHunters/band-up.git

```


#### Lựa Chọn AWS Region

Cho workshop này, chúng tôi khuyến nghị sử dụng region **ap-southeast-1 (Singapore)** vì:
- Latency thấp cho người dùng Đông Nam Á
- Đầy đủ tất cả các services cần thiết
- Amazon Bedrock availability

Xác minh Bedrock availability trong region đã chọn:
```bash
aws bedrock list-foundation-models --region ap-southeast-1 --query 'modelSummaries[?contains(modelId, `titan`)].modelId'
```

#### Ước Tính Chi Phí

Chi phí ước tính cho việc chạy workshop này:

| Dịch Vụ | Chi Phí Ước Tính (mỗi ngày) |
|---------|-------------------------|
| ECS Fargate | $2-5 |
| RDS PostgreSQL | $3-5 |
| ElastiCache | $0.50-1 |
| ALB | $0.80 |
| NAT Gateway | $1-2 |
| Lambda | $0.10-0.50 |
| Các dịch vụ khác | $1-2 |
| **Tổng** | **~$10-15/ngày** |

{{% notice warning %}}
Nhớ dọn dẹp resources sau khi hoàn thành workshop để tránh chi phí không cần thiết. Hướng dẫn cleanup được cung cấp trong Phần 5.11.
{{% /notice %}}

#### Checklist Môi Trường

Trước khi tiếp tục, hãy đảm bảo bạn có:

- [ ] Tài khoản AWS với đủ quyền
- [ ] AWS CLI đã cài đặt và cấu hình
- [ ] Docker đã cài đặt và đang chạy
- [ ] Git đã cài đặt
- [ ] Node.js 18+ đã cài đặt
- [ ] Java 17+ và Maven đã cài đặt
- [ ] Google Gemini API key đã có
- [ ] Tên miền (tùy chọn nhưng khuyến nghị)
- [ ] Source code repositories đã clone


#### Bước Tiếp Theo

Sau khi hoàn thành tất cả điều kiện tiên quyết, tiến hành đến [VPC & Network Setup](../5.3-VPC-Network/) để bắt đầu xây dựng hạ tầng mạng.

