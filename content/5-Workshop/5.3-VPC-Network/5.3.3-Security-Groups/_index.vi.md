---
title: "Security Groups"
date:
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

#### Tổng Quan

Security Groups hoạt động như virtual firewalls kiểm soát inbound và outbound traffic ở instance level. Trong bước này, bạn sẽ tạo security groups cho mỗi tier của ứng dụng tuân theo nguyên tắc least privilege.

#### Kiến Trúc Security Group

Chúng ta sẽ tạo các security groups sau:

| Security Group | Mục Đích | Gắn Vào |
|----------------|---------|-------------|
| `ielts-prod-alb-sg` | Internet-facing traffic | Application Load Balancer |
| `ielts-prod-ecs-fe-sg` | Frontend containers | ECS Frontend Tasks |
| `ielts-prod-ecs-be-sg` | Backend containers | ECS Backend Tasks |
| `ielts-prod-rds-sg` | Database access | RDS PostgreSQL |
| `ielts-prod-redis-sg` | Cache access | ElastiCache Redis |



#### Tạo ALB Security Group

**Bước 1: Tạo Security Group**

1. Điều hướng đến **VPC** → **Security Groups**
2. Click **Create security group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Security group name** | `ielts-prod-alb-sg` |
| **Description** | Allow HTTP/HTTPS from internet |
| **VPC** | ielts-prod-vpc |

**Bước 2: Cấu Hình Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| HTTP | TCP | 80 | 0.0.0.0/0 | Allow HTTP từ anywhere |
| HTTPS | TCP | 443 | 0.0.0.0/0 | Allow HTTPS từ anywhere |

**Bước 3: Cấu Hình Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |



#### Tạo ECS Frontend Security Group

**Bước 1: Tạo Security Group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Security group name** | `ielts-prod-ecs-fe-sg` |
| **Description** | Allow traffic from ALB to Frontend containers |
| **VPC** | ielts-prod-vpc |

**Bước 2: Cấu Hình Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| Custom TCP | TCP | 3000 | ielts-prod-alb-sg | Allow từ ALB |

**Bước 3: Cấu Hình Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

{{% notice tip %}}
Port 3000 là default port cho Next.js applications. Điều chỉnh nếu frontend của bạn dùng port khác.
{{% /notice %}}

#### Tạo ECS Backend Security Group

**Bước 1: Tạo Security Group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Security group name** | `ielts-prod-ecs-be-sg` |
| **Description** | Allow traffic from Frontend and ALB to Backend |
| **VPC** | ielts-prod-vpc |

**Bước 2: Cấu Hình Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| Custom TCP | TCP | 8080 | ielts-prod-alb-sg | Allow từ ALB (API routes) |
| Custom TCP | TCP | 8080 | ielts-prod-ecs-fe-sg | Allow từ Frontend |

**Bước 3: Cấu Hình Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

{{% notice tip %}}
Port 8080 là default port cho Spring Boot applications. Backend cần outbound access cho external APIs (Gemini, etc.).
{{% /notice %}}

#### Tạo RDS Security Group

**Bước 1: Tạo Security Group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Security group name** | `ielts-prod-rds-sg` |
| **Description** | Allow PostgreSQL access from ECS Backend |
| **VPC** | ielts-prod-vpc |

**Bước 2: Cấu Hình Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| PostgreSQL | TCP | 5432 | ielts-prod-ecs-be-sg | Allow từ Backend |

**Bước 3: Cấu Hình Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

{{% notice warning %}}
Không bao giờ expose RDS trực tiếp ra internet. Luôn dùng security groups để restrict access chỉ đến application tier.
{{% /notice %}}

#### Tạo ElastiCache Security Group

**Bước 1: Tạo Security Group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Security group name** | `ielts-prod-redis-sg` |
| **Description** | Allow Redis access from ECS Backend |
| **VPC** | ielts-prod-vpc |

**Bước 2: Cấu Hình Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| Custom TCP | TCP | 6379 | ielts-prod-ecs-be-sg | Allow từ Backend |

**Bước 3: Cấu Hình Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

#### AWS CLI Commands

Tạo tất cả security groups bằng CLI:

```bash
# Lấy VPC ID
VPC_ID=$(aws ec2 describe-vpcs --filters "Name=tag:Name,Values=ielts-prod-vpc" --query 'Vpcs[0].VpcId' --output text)

# Tạo ALB Security Group
ALB_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-alb-sg \
    --description "Allow HTTP/HTTPS from internet" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $ALB_SG --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $ALB_SG --protocol tcp --port 443 --cidr 0.0.0.0/0

# Tạo ECS Frontend Security Group
FE_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-ecs-fe-sg \
    --description "Allow traffic from ALB" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $FE_SG --protocol tcp --port 3000 --source-group $ALB_SG

# Tạo ECS Backend Security Group
BE_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-ecs-be-sg \
    --description "Allow traffic from ALB and Frontend" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $BE_SG --protocol tcp --port 8080 --source-group $ALB_SG
aws ec2 authorize-security-group-ingress --group-id $BE_SG --protocol tcp --port 8080 --source-group $FE_SG

# Tạo RDS Security Group
RDS_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-rds-sg \
    --description "Allow PostgreSQL from Backend" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $RDS_SG --protocol tcp --port 5432 --source-group $BE_SG

# Tạo Redis Security Group
REDIS_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-redis-sg \
    --description "Allow Redis from Backend" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $REDIS_SG --protocol tcp --port 6379 --source-group $BE_SG
```

#### Luồng Traffic Security Group

```
Internet → ALB (80/443) → Frontend (3000) → Backend (8080) → RDS (5432)
                                               ↓
                                          Redis (6379)
```

#### Tóm Tắt Security Group

| Security Group | Inbound Từ | Port | Outbound |
|----------------|--------------|------|----------|
| ALB | Internet | 80, 443 | All |
| ECS Frontend | ALB | 3000 | All |
| ECS Backend | ALB, Frontend | 8080 | All |
| RDS | Backend | 5432 | All |
| Redis | Backend | 6379 | All |



#### Best Practices

{{% notice tip %}}
**Security Group Best Practices:**
- Sử dụng security group references thay vì IP ranges khi có thể
- Tuân theo least privilege - chỉ mở ports cần thiết
- Dùng descriptive names và descriptions
- Regularly audit security group rules
- Không bao giờ allow 0.0.0.0/0 đến database ports
{{% /notice %}}

#### Bước Tiếp Theo

Tiến hành đến [NAT Gateway](../5.3.4-NAT-Gateway/) để cấu hình outbound internet access cho private subnets.

