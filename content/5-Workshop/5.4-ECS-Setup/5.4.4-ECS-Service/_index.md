---
title: "ECS Service"
date:
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Overview

ECS Services maintain the desired number of running tasks and integrate with load balancers for traffic distribution. In this step, you will deploy Frontend and Backend services with ALB integration and Service Connect.

#### Create Backend Service

Deploy the backend service first as it's required by the frontend.

**Step 1: Navigate to ECS Console**

1. Go to **ECS** → **Clusters** → `ielts-prod-cluster`
2. Click **Services** tab → **Create**

**Step 2: Configure Service**

| Setting | Value |
|---------|-------|
| **Launch type** | FARGATE |
| **Task definition** | ielts-backend |
| **Service name** | `ielts-backend-service` |
| **Desired tasks** | 2 |

**Step 3: Configure Networking**

| Setting | Value |
|---------|-------|
| **VPC** | ielts-prod-vpc |
| **Subnets** | Private App subnets (1a, 1b) |
| **Security group** | ielts-prod-ecs-be-sg |
| **Public IP** | Disabled |

**Step 4: Configure Service Connect**

| Setting | Value |
|---------|-------|
| **Enable Service Connect** | Yes |
| **Namespace** | ielts.local |
| **Client mode** | Client and server |
| **Port alias** | backend |
| **Port** | 8080 |
| **DNS name** | backend.ielts.local |

**Step 5: Configure Load Balancer**

| Setting | Value |
|---------|-------|
| **Load balancer type** | Application Load Balancer |
| **Load balancer** | Create new or select existing |
| **Target group** | Create new: `ielts-backend-tg` |
| **Health check path** | `/actuator/health` |
| **Container port** | 8080 |


#### Create Frontend Service

**Step 1: Create Service**

| Setting | Value |
|---------|-------|
| **Launch type** | FARGATE |
| **Task definition** | ielts-frontend |
| **Service name** | `ielts-frontend-service` |
| **Desired tasks** | 2 |

**Step 2: Configure Networking**

| Setting | Value |
|---------|-------|
| **VPC** | ielts-prod-vpc |
| **Subnets** | Private App subnets (1a, 1b) |
| **Security group** | ielts-prod-ecs-fe-sg |
| **Public IP** | Disabled |

**Step 3: Configure Service Connect**

| Setting | Value |
|---------|-------|
| **Enable Service Connect** | Yes |
| **Namespace** | ielts.local |
| **Client mode** | Client and server |
| **Port alias** | frontend |
| **Port** | 3000 |
| **DNS name** | frontend.ielts.local |

**Step 4: Configure Load Balancer**

| Setting | Value |
|---------|-------|
| **Load balancer type** | Application Load Balancer |
| **Load balancer** | Select existing ALB |
| **Target group** | Create new: `ielts-frontend-tg` |
| **Health check path** | `/api/health` |
| **Container port** | 3000 |


#### AWS CLI Commands

**Create Backend Service:**

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

**Create Frontend Service:**

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

#### Configure Auto Scaling

**Step 1: Register Scalable Target**

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

**Step 2: Create Scaling Policy**

```bash
# CPU-based scaling for backend
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

#### Verify Service Deployment

**Console Verification:**

1. Navigate to **ECS** → **Clusters** → `ielts-prod-cluster`
2. Click **Services** tab
3. Verify both services show:
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

#### Service Connect Verification

Test internal DNS resolution between services:

```bash
# From within a task (using ECS Exec)
aws ecs execute-command \
    --cluster ielts-prod-cluster \
    --task <task-id> \
    --container frontend \
    --interactive \
    --command "/bin/sh"

# Inside container
curl http://backend.ielts.local:8080/actuator/health
```

#### Deployment Configuration

| Setting | Frontend | Backend |
|---------|----------|---------|
| **Desired Count** | 2 | 2 |
| **Min Healthy %** | 100 | 100 |
| **Max %** | 200 | 200 |
| **Deployment Type** | Rolling update | Rolling update |

This ensures zero-downtime deployments by:
- Maintaining at least 100% of tasks during deployment
- Allowing up to 200% during transition

#### Service Architecture Summary

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

#### Next Steps

The ECS setup is complete. Proceed to [Load Balancer Configuration](../../5.5-Load-Balancer/) to configure ALB routing rules.

