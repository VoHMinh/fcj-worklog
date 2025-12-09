---
title: "Target Groups"
date:
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

#### Overview

Target groups route requests to registered targets (ECS tasks) and perform health checks.

#### Create Frontend Target Group

1. Navigate to **EC2** → **Target Groups** → **Create target group**

| Setting | Value |
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


#### Create Backend Target Group

| Setting | Value |
|---------|-------|
| **Target type** | IP addresses |
| **Target group name** | `ielts-backend-tg` |
| **Protocol** | HTTP |
| **Port** | 8080 |
| **VPC** | ielts-prod-vpc |
| **Health check path** | `/actuator/health` |

#### AWS CLI Commands

```bash
# Create frontend target group
aws elbv2 create-target-group \
    --name ielts-frontend-tg \
    --protocol HTTP \
    --port 3000 \
    --vpc-id $VPC_ID \
    --target-type ip \
    --health-check-path /api/health \
    --health-check-interval-seconds 30

# Create backend target group
aws elbv2 create-target-group \
    --name ielts-backend-tg \
    --protocol HTTP \
    --port 8080 \
    --vpc-id $VPC_ID \
    --target-type ip \
    --health-check-path /actuator/health \
    --health-check-interval-seconds 30
```

#### Next Steps

Proceed to [ALB Configuration](../5.5.2-ALB-Configuration/).

