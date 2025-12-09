---
title: "Task Definition"
date:
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Overview

An ECS Task Definition is a blueprint that describes how Docker containers should run. It specifies the container image, CPU/memory requirements, environment variables, logging configuration, and IAM roles.

#### Create IAM Roles

Before creating task definitions, set up the required IAM roles:

**Step 1: Create Task Execution Role**

This role allows ECS to pull images from ECR and write logs to CloudWatch.

```bash
# Create trust policy
cat > ecs-task-execution-trust.json << 'EOF'
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ecs-tasks.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
EOF

# Create role
aws iam create-role \
    --role-name ecsTaskExecutionRole \
    --assume-role-policy-document file://ecs-task-execution-trust.json

# Attach managed policy
aws iam attach-role-policy \
    --role-name ecsTaskExecutionRole \
    --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
```

**Step 2: Create Task Role (for Backend)**

This role grants the backend container access to AWS services.

```bash
# Create task role
aws iam create-role \
    --role-name ielts-backend-task-role \
    --assume-role-policy-document file://ecs-task-execution-trust.json

# Create custom policy
cat > backend-policy.json << 'EOF'
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:DeleteObject"
            ],
            "Resource": "arn:aws:s3:::ielts-prod-media/*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "arn:aws:secretsmanager:*:*:secret:ielts-prod/*"
        }
    ]
}
EOF

aws iam put-role-policy \
    --role-name ielts-backend-task-role \
    --policy-name ielts-backend-permissions \
    --policy-document file://backend-policy.json
```

#### Create Frontend Task Definition

**Step 1: Navigate to ECS Console**

1. Go to **Amazon ECS** → **Task Definitions**
2. Click **Create new task definition**

**Step 2: Configure Task Definition**

| Setting | Value |
|---------|-------|
| **Task definition family** | `ielts-frontend` |
| **Launch type** | AWS Fargate |
| **Operating system** | Linux/X86_64 |
| **CPU** | 0.5 vCPU |
| **Memory** | 1 GB |
| **Task role** | None |
| **Task execution role** | ecsTaskExecutionRole |

**Step 3: Configure Container**

| Setting | Value |
|---------|-------|
| **Name** | `frontend` |
| **Image URI** | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-frontend:latest` |
| **Essential** | Yes |
| **Port mappings** | 3000 (TCP) |

**Environment Variables:**
| Key | Value |
|-----|-------|
| `NODE_ENV` | `production` |
| `NEXT_PUBLIC_API_URL` | `http://backend.ielts.local:8080` |

**Logging:**
| Setting | Value |
|---------|-------|
| **Log driver** | awslogs |
| **Log group** | `/ecs/ielts-frontend` |
| **Region** | ap-southeast-1 |
| **Stream prefix** | ecs |


#### Create Backend Task Definition

**Step 1: Configure Task Definition**

| Setting | Value |
|---------|-------|
| **Task definition family** | `ielts-backend` |
| **Launch type** | AWS Fargate |
| **Operating system** | Linux/X86_64 |
| **CPU** | 1 vCPU |
| **Memory** | 2 GB |
| **Task role** | ielts-backend-task-role |
| **Task execution role** | ecsTaskExecutionRole |

**Step 2: Configure Container**

| Setting | Value |
|---------|-------|
| **Name** | `backend` |
| **Image URI** | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-backend:latest` |
| **Essential** | Yes |
| **Port mappings** | 8080 (TCP) |

**Environment Variables:**
| Key | Value Type | Value |
|-----|------------|-------|
| `SPRING_PROFILES_ACTIVE` | Value | `prod` |
| `DB_HOST` | Value | `ielts-prod-db.xxxx.ap-southeast-1.rds.amazonaws.com` |
| `DB_NAME` | Value | `ielts` |
| `DB_USERNAME` | Secrets Manager | `ielts-prod/db:username` |
| `DB_PASSWORD` | Secrets Manager | `ielts-prod/db:password` |
| `REDIS_HOST` | Value | `ielts-prod-redis.xxxx.cache.amazonaws.com` |
| `S3_BUCKET` | Value | `ielts-prod-media` |

**Health Check:**
| Setting | Value |
|---------|-------|
| **Command** | `CMD-SHELL, curl -f http://localhost:8080/actuator/health || exit 1` |
| **Interval** | 30 seconds |
| **Timeout** | 5 seconds |
| **Retries** | 3 |
| **Start period** | 60 seconds |


#### Task Definition JSON (CLI)

**Frontend Task Definition:**

```json
{
    "family": "ielts-frontend",
    "networkMode": "awsvpc",
    "requiresCompatibilities": ["FARGATE"],
    "cpu": "512",
    "memory": "1024",
    "executionRoleArn": "arn:aws:iam::{account}:role/ecsTaskExecutionRole",
    "containerDefinitions": [
        {
            "name": "frontend",
            "image": "{account}.dkr.ecr.ap-southeast-1.amazonaws.com/ielts-frontend:latest",
            "essential": true,
            "portMappings": [
                {
                    "containerPort": 3000,
                    "protocol": "tcp"
                }
            ],
            "environment": [
                {"name": "NODE_ENV", "value": "production"},
                {"name": "NEXT_PUBLIC_API_URL", "value": "http://backend.ielts.local:8080"}
            ],
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "/ecs/ielts-frontend",
                    "awslogs-region": "ap-southeast-1",
                    "awslogs-stream-prefix": "ecs"
                }
            }
        }
    ]
}
```

**Backend Task Definition:**

```json
{
    "family": "ielts-backend",
    "networkMode": "awsvpc",
    "requiresCompatibilities": ["FARGATE"],
    "cpu": "1024",
    "memory": "2048",
    "executionRoleArn": "arn:aws:iam::{account}:role/ecsTaskExecutionRole",
    "taskRoleArn": "arn:aws:iam::{account}:role/ielts-backend-task-role",
    "containerDefinitions": [
        {
            "name": "backend",
            "image": "{account}.dkr.ecr.ap-southeast-1.amazonaws.com/ielts-backend:latest",
            "essential": true,
            "portMappings": [
                {
                    "containerPort": 8080,
                    "protocol": "tcp"
                }
            ],
            "environment": [
                {"name": "SPRING_PROFILES_ACTIVE", "value": "prod"}
            ],
            "secrets": [
                {
                    "name": "DB_PASSWORD",
                    "valueFrom": "arn:aws:secretsmanager:ap-southeast-1:{account}:secret:ielts-prod/db:password::"
                }
            ],
            "healthCheck": {
                "command": ["CMD-SHELL", "curl -f http://localhost:8080/actuator/health || exit 1"],
                "interval": 30,
                "timeout": 5,
                "retries": 3,
                "startPeriod": 60
            },
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "/ecs/ielts-backend",
                    "awslogs-region": "ap-southeast-1",
                    "awslogs-stream-prefix": "ecs"
                }
            }
        }
    ]
}
```

#### Register Task Definitions

```bash
# Create CloudWatch log groups first
aws logs create-log-group --log-group-name /ecs/ielts-frontend
aws logs create-log-group --log-group-name /ecs/ielts-backend

# Register task definitions
aws ecs register-task-definition --cli-input-json file://frontend-task-def.json
aws ecs register-task-definition --cli-input-json file://backend-task-def.json
```

#### Task Definition Summary

| Task Definition | CPU | Memory | Container Port | Task Role |
|-----------------|-----|--------|----------------|-----------|
| ielts-frontend | 0.5 vCPU | 1 GB | 3000 | None |
| ielts-backend | 1 vCPU | 2 GB | 8080 | ielts-backend-task-role |

#### Next Steps

Proceed to [ECS Cluster](../5.4.3-ECS-Cluster/) to create the cluster for running these tasks.

