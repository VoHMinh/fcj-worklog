---
title: "ECS Service"
date:
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Tổng Quan

ECS Services duy trì desired number của running tasks và tích hợp với load balancers để phân phối traffic. Trong bước này, bạn sẽ deploy Frontend và Backend services với ALB integration và Service Connect.

#### Tạo Backend Service

Deploy backend service trước vì frontend cần nó.

**Bước 1: Điều Hướng đến ECS Console**

1. Vào **ECS** → **Clusters** → `ielts-prod-cluster`
2. Click tab **Services** → **Create**

**Bước 2: Cấu Hình Service**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Launch type** | FARGATE |
| **Task definition** | ielts-backend |
| **Service name** | `ielts-backend-service` |
| **Desired tasks** | 2 |

**Bước 3: Cấu Hình Networking**

| Cài Đặt | Giá Trị |
|---------|-------|
| **VPC** | ielts-prod-vpc |
| **Subnets** | Private App subnets (1a, 1b) |
| **Security group** | ielts-prod-ecs-be-sg |
| **Public IP** | Disabled |

**Bước 4: Cấu Hình Service Connect**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Enable Service Connect** | Yes |
| **Namespace** | ielts.local |
| **Client mode** | Client and server |
| **Port alias** | backend |
| **Port** | 8080 |
| **DNS name** | backend.ielts.local |

**Bước 5: Cấu Hình Load Balancer**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Load balancer type** | Application Load Balancer |
| **Load balancer** | Create new hoặc select existing |
| **Target group** | Create new: `ielts-backend-tg` |
| **Health check path** | `/actuator/health` |
| **Container port** | 8080 |


#### Tạo Frontend Service

**Bước 1: Tạo Service**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Launch type** | FARGATE |
| **Task definition** | ielts-frontend |
| **Service name** | `ielts-frontend-service` |
| **Desired tasks** | 2 |

**Bước 2: Cấu Hình Networking**

| Cài Đặt | Giá Trị |
|---------|-------|
| **VPC** | ielts-prod-vpc |
| **Subnets** | Private App subnets (1a, 1b) |
| **Security group** | ielts-prod-ecs-fe-sg |
| **Public IP** | Disabled |

**Bước 3: Cấu Hình Service Connect**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Enable Service Connect** | Yes |
| **Namespace** | ielts.local |
| **Client mode** | Client and server |
| **Port alias** | frontend |
| **Port** | 3000 |
| **DNS name** | frontend.ielts.local |

**Bước 4: Cấu Hình Load Balancer**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Load balancer type** | Application Load Balancer |
| **Load balancer** | Select existing ALB |
| **Target group** | Create new: `ielts-frontend-tg` |
| **Health check path** | `/api/health` |
| **Container port** | 3000 |


#### AWS CLI Commands

**Tạo Backend Service:**

```bash
aws ecs create-service \
    --cluster ielts-prod-cluster \
    --service-name ielts-backend-service \
    --task-definition ielts-backend \
    --desired-count 2 \
    --launch-type FARGATE \
    --network-configuration "awsvpcConfiguration={subnets=[$PRIVATE_APP_1A,$PRIVATE_APP_1B],securityGroups=[$BE_SG],assignPublicIp=DISABLED}" \
    --service-connect-configuration '{
        "enabled": true,
        "namespace": "ielts.local",
        "services": [{
            "portName": "backend",
            "discoveryName": "backend",
            "clientAliases": [{"port": 8080, "dnsName": "backend.ielts.local"}]
        }]
    }' \
    --load-balancers '[{
        "targetGroupArn": "arn:aws:elasticloadbalancing:ap-southeast-1:{account}:targetgroup/ielts-backend-tg/xxx",
        "containerName": "backend",
        "containerPort": 8080
    }]'
```

**Tạo Frontend Service:**

```bash
aws ecs create-service \
    --cluster ielts-prod-cluster \
    --service-name ielts-frontend-service \
    --task-definition ielts-frontend \
    --desired-count 2 \
    --launch-type FARGATE \
    --network-configuration "awsvpcConfiguration={subnets=[$PRIVATE_APP_1A,$PRIVATE_APP_1B],securityGroups=[$FE_SG],assignPublicIp=DISABLED}" \
    --service-connect-configuration '{
        "enabled": true,
        "namespace": "ielts.local",
        "services": [{
            "portName": "frontend",
            "discoveryName": "frontend",
            "clientAliases": [{"port": 3000, "dnsName": "frontend.ielts.local"}]
        }]
    }' \
    --load-balancers '[{
        "targetGroupArn": "arn:aws:elasticloadbalancing:ap-southeast-1:{account}:targetgroup/ielts-frontend-tg/xxx",
        "containerName": "frontend",
        "containerPort": 3000
    }]'
```

#### Cấu Hình Auto Scaling

**Bước 1: Register Scalable Target**

```bash
# Backend service
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/ielts-prod-cluster/ielts-backend-service \
    --min-capacity 2 \
    --max-capacity 10

# Frontend service
aws application-autoscaling register-scalable-target \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/ielts-prod-cluster/ielts-frontend-service \
    --min-capacity 2 \
    --max-capacity 10
```

**Bước 2: Tạo Scaling Policy**

```bash
# CPU-based scaling cho backend
aws application-autoscaling put-scaling-policy \
    --service-namespace ecs \
    --scalable-dimension ecs:service:DesiredCount \
    --resource-id service/ielts-prod-cluster/ielts-backend-service \
    --policy-name backend-cpu-scaling \
    --policy-type TargetTrackingScaling \
    --target-tracking-scaling-policy-configuration '{
        "TargetValue": 70.0,
        "PredefinedMetricSpecification": {
            "PredefinedMetricType": "ECSServiceAverageCPUUtilization"
        },
        "ScaleOutCooldown": 60,
        "ScaleInCooldown": 120
    }'
```

#### Xác Minh Service Deployment

**Console Verification:**

1. Điều hướng đến **ECS** → **Clusters** → `ielts-prod-cluster`
2. Click tab **Services**
3. Xác minh cả hai services hiển thị:
   - **Status**: Active
   - **Running tasks**: 2/2
   - **Deployments**: 1 (PRIMARY)


**CLI Verification:**

```bash
# List services
aws ecs list-services --cluster ielts-prod-cluster

# Describe service
aws ecs describe-services \
    --cluster ielts-prod-cluster \
    --services ielts-backend-service ielts-frontend-service

# List running tasks
aws ecs list-tasks --cluster ielts-prod-cluster --service-name ielts-backend-service
```

#### Xác Minh Service Connect

Test internal DNS resolution giữa services:

```bash
# Từ trong task (sử dụng ECS Exec)
aws ecs execute-command \
    --cluster ielts-prod-cluster \
    --task <task-id> \
    --container frontend \
    --interactive \
    --command "/bin/sh"

# Trong container
curl http://backend.ielts.local:8080/actuator/health
```

#### Deployment Configuration

| Cài Đặt | Frontend | Backend |
|---------|----------|---------|
| **Desired Count** | 2 | 2 |
| **Min Healthy %** | 100 | 100 |
| **Max %** | 200 | 200 |
| **Deployment Type** | Rolling update | Rolling update |

Điều này đảm bảo zero-downtime deployments bằng cách:
- Duy trì ít nhất 100% tasks trong quá trình deployment
- Cho phép lên đến 200% trong transition

#### Tóm Tắt Kiến Trúc Service

```
Internet
    │
    ▼
┌─────────────────────────────────────┐
│       Application Load Balancer     │
│  ┌─────────────┬─────────────────┐  │
│  │ /api/*      │ /*              │  │
│  │ → Backend   │ → Frontend      │  │
│  └─────────────┴─────────────────┘  │
└─────────────────────────────────────┘
    │                     │
    ▼                     ▼
┌─────────────┐    ┌─────────────┐
│  Backend    │    │  Frontend   │
│  Service    │◄───│  Service    │
│  (2 tasks)  │    │  (2 tasks)  │
└─────────────┘    └─────────────┘
    │ Service Connect: backend.ielts.local:8080
```

#### Bước Tiếp Theo

ECS setup đã hoàn thành. Tiến hành đến [Load Balancer Configuration](../../5.5-Load-Balancer/) để cấu hình ALB routing rules.

