---
title: "ECS & Container Setup"
date:
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Tổng Quan

Trong phần này, bạn sẽ triển khai các ứng dụng Hệ Thống Tự Học IELTS sử dụng Amazon ECS (Elastic Container Service) với Fargate launch type. Kiến trúc triển khai theo mô hình active-passive Multi-AZ cho high availability.

#### Kiến Trúc ECS

![Kiến Trúc ECS](/images/2-Proposal/AWS-Bandup-Architecture.png)

**Chiến Lược Triển Khai:**
- **Active-Passive Multi-AZ**: Primary services chạy trong AZ-1, standby trong AZ-2
- **Fargate Launch Type**: Serverless container management
- **Service Connect**: Internal service discovery giữa Frontend và Backend

**Container Services:**

| Service | Container | Port | Replicas (Active) | Replicas (Standby) |
|---------|-----------|------|-------------------|-------------------|
| Frontend | Next.js 14+ | 3000 | 2 | 1 |
| Backend | Spring Boot 3.x | 8080 | 2 | 1 |

#### Các Thành Phần ECS

**1. Amazon ECR (Elastic Container Registry)**
- Private container image repository
- Lưu trữ Frontend (Next.js) và Backend (Spring Boot) images
- Tích hợp với ECS cho seamless image pulls
- Image scanning cho vulnerabilities

**2. Task Definitions**
- Blueprint cho running containers
- Định nghĩa CPU, memory, environment variables
- Cấu hình logging đến CloudWatch
- Thiết lập IAM roles cho task execution

**3. ECS Cluster**
- Logical grouping của services và tasks
- Cluster capacity providers (Fargate, Fargate Spot)
- Service Connect namespace cho internal discovery

**4. ECS Services**
- Duy trì desired number của running tasks
- Tích hợp với ALB cho load balancing
- Auto Scaling dựa trên metrics
- Rolling deployment cho zero-downtime updates

#### Container Specifications

**Frontend (Next.js):**

| Resource | Value |
|----------|-------|
| **Image** | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-frontend:latest` |
| **CPU** | 512 (0.5 vCPU) |
| **Memory** | 1024 MB |
| **Port** | 3000 |
| **Health Check** | `/api/health` |

**Backend (Spring Boot):**

| Resource | Value |
|----------|-------|
| **Image** | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-backend:latest` |
| **CPU** | 1024 (1 vCPU) |
| **Memory** | 2048 MB |
| **Port** | 8080 |
| **Health Check** | `/actuator/health` |

#### Những Gì Bạn Sẽ Tạo

Trong phần này, bạn sẽ:

1. Tạo ECR repositories cho Frontend và Backend images
2. Build và push Docker images lên ECR
3. Tạo ECS Task Definitions với proper configurations
4. Thiết lập ECS Cluster với Fargate capacity
5. Triển khai ECS Services với ALB integration
6. Cấu hình Service Connect cho internal communication

#### Thời Gian Ước Tính

| Tác Vụ | Thời Gian |
|------|------|
| Tạo ECR Repositories | 10 phút |
| Build & Push Images | 15 phút |
| Tạo Task Definitions | 15 phút |
| Thiết lập ECS Cluster | 5 phút |
| Triển khai ECS Services | 15 phút |
| **Tổng** | **~60 phút** |

#### Nội Dung

1. [ECR Repository](5.4.1-ECR-Repository/)
2. [Task Definition](5.4.2-Task-Definition/)
3. [ECS Cluster](5.4.3-ECS-Cluster/)
4. [ECS Service](5.4.4-ECS-Service/)

#### Best Practices

{{% notice tip %}}
**ECS Best Practices:**
- Sử dụng Fargate cho serverless container management
- Implement health checks cho automatic recovery
- Sử dụng task IAM roles thay vì hardcoded credentials
- Enable container insights cho monitoring
- Sử dụng Service Connect cho internal service discovery
- Implement proper logging đến CloudWatch
{{% /notice %}}

