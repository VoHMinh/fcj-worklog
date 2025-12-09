---
title: "ECR Repository"
date:
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Tổng Quan

Amazon Elastic Container Registry (ECR) là fully managed container registry lưu trữ, quản lý, và deploy Docker container images. Trong bước này, bạn sẽ tạo ECR repositories cho Frontend và Backend applications.

#### Tạo ECR Repositories

**Bước 1: Điều Hướng đến ECR Console**

1. Đăng nhập vào AWS Management Console
2. Điều hướng đến dịch vụ **Amazon ECR**
3. Click **Create repository**

**Bước 2: Tạo Frontend Repository**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Visibility settings** | Private |
| **Repository name** | `ielts-frontend` |
| **Tag immutability** | Disabled |
| **Scan on push** | Enabled |
| **Encryption** | AES-256 |

Click **Create repository**.


**Bước 3: Tạo Backend Repository**

Lặp lại quy trình:

| Cài Đặt | Giá Trị |
|---------|-------|
| **Visibility settings** | Private |
| **Repository name** | `ielts-backend` |
| **Tag immutability** | Disabled |
| **Scan on push** | Enabled |
| **Encryption** | AES-256 |

#### AWS CLI Commands

```bash
# Tạo Frontend repository
aws ecr create-repository \
    --repository-name ielts-frontend \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256 \
    --region ap-southeast-1

# Tạo Backend repository
aws ecr create-repository \
    --repository-name ielts-backend \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256 \
    --region ap-southeast-1
```

#### Cấu Hình Lifecycle Policy

Thiết lập lifecycle policies để tự động dọn dẹp old images:

```bash
# Lifecycle policy JSON
cat > lifecycle-policy.json << 'EOF'
{
    "rules": [
        {
            "rulePriority": 1,
            "description": "Keep last 10 images",
            "selection": {
                "tagStatus": "any",
                "countType": "imageCountMoreThan",
                "countNumber": 10
            },
            "action": {
                "type": "expire"
            }
        }
    ]
}
EOF

# Apply cho cả hai repositories
aws ecr put-lifecycle-policy \
    --repository-name ielts-frontend \
    --lifecycle-policy-text file://lifecycle-policy.json

aws ecr put-lifecycle-policy \
    --repository-name ielts-backend \
    --lifecycle-policy-text file://lifecycle-policy.json
```

#### Build và Push Docker Images

**Bước 1: Authenticate Docker với ECR**

```bash
# Lấy account ID và region
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
AWS_REGION=ap-southeast-1

# Login vào ECR
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

**Bước 2: Build Frontend Image**

```bash
# Điều hướng đến frontend project
cd ielts-frontend

# Tạo Dockerfile nếu chưa có
cat > Dockerfile << 'EOF'
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/next.config.js ./
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
ENV PORT 3000
CMD ["node", "server.js"]
EOF

# Build image
docker build -t ielts-frontend:latest .

# Tag image
docker tag ielts-frontend:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-frontend:latest

# Push lên ECR
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-frontend:latest
```

**Bước 3: Build Backend Image**

```bash
# Điều hướng đến backend project
cd ielts-backend

# Tạo Dockerfile nếu chưa có
cat > Dockerfile << 'EOF'
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
EOF

# Build image
docker build -t ielts-backend:latest .

# Tag image
docker tag ielts-backend:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-backend:latest

# Push lên ECR
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-backend:latest
```


#### Xác Minh Images

```bash
# Liệt kê images trong frontend repository
aws ecr describe-images --repository-name ielts-frontend

# Liệt kê images trong backend repository
aws ecr describe-images --repository-name ielts-backend
```

#### Image Vulnerability Scanning

Sau khi push, ECR tự động scan images cho vulnerabilities:

1. Điều hướng đến **ECR** → **Repositories**
2. Chọn repository
3. Click vào image tag
4. Xem tab **Vulnerabilities**

{{% notice warning %}}
Giải quyết bất kỳ CRITICAL hoặc HIGH severity vulnerabilities trước khi deploy lên production.
{{% /notice %}}

#### Tóm Tắt ECR Repository

| Repository | URI | Image |
|------------|-----|-------|
| ielts-frontend | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-frontend` | Next.js app |
| ielts-backend | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-backend` | Spring Boot app |

#### Bước Tiếp Theo

Tiến hành đến [Task Definition](../5.4.2-Task-Definition/) để tạo ECS task definitions cho running containers.

