---
title: "Target Groups"
date:
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Tổng Quan

Target groups route requests đến registered targets (ECS tasks) và thực hiện health checks.

#### Tạo Frontend Target Group

1. Điều hướng đến **EC2** → **Target Groups** → **Create target group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Target type** | IP addresses |
| **Target group name** | `ielts-frontend-tg` |
| **Protocol** | HTTP |
| **Port** | 3000 |
| **VPC** | ielts-prod-vpc |
| **Health check path** | `/api/health` |
| **Healthy threshold** | 2 |
| **Unhealthy threshold** | 3 |
| **Interval** | 30 seconds |


#### Tạo Backend Target Group

| Cài Đặt | Giá Trị |
|---------|-------|
| **Target type** | IP addresses |
| **Target group name** | `ielts-backend-tg` |
| **Protocol** | HTTP |
| **Port** | 8080 |
| **VPC** | ielts-prod-vpc |
| **Health check path** | `/actuator/health` |

#### AWS CLI Commands

```bash
# Tạo frontend target group
aws elbv2 create-target-group \
    --name ielts-frontend-tg \
    --protocol HTTP \
    --port 3000 \
    --vpc-id $VPC_ID \
    --target-type ip \
    --health-check-path /api/health \
    --health-check-interval-seconds 30

# Tạo backend target group
aws elbv2 create-target-group \
    --name ielts-backend-tg \
    --protocol HTTP \
    --port 8080 \
    --vpc-id $VPC_ID \
    --target-type ip \
    --health-check-path /actuator/health \
    --health-check-interval-seconds 30
```

#### Bước Tiếp Theo

Tiến hành đến [ALB Configuration](../5.5.2-ALB-Configuration/).

