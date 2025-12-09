---
title: "Security Groups"
date:
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

#### Overview

Security Groups act as virtual firewalls controlling inbound and outbound traffic at the instance level. In this step, you will create security groups for each tier of the application following the principle of least privilege.

#### Security Group Architecture

We will create the following security groups:

| Security Group | Purpose | Attached To |
|----------------|---------|-------------|
| `ielts-prod-alb-sg` | Internet-facing traffic | Application Load Balancer |
| `ielts-prod-ecs-fe-sg` | Frontend containers | ECS Frontend Tasks |
| `ielts-prod-ecs-be-sg` | Backend containers | ECS Backend Tasks |
| `ielts-prod-rds-sg` | Database access | RDS PostgreSQL |
| `ielts-prod-redis-sg` | Cache access | ElastiCache Redis |


#### Create ALB Security Group

**Step 1: Create Security Group**

1. Navigate to **VPC** → **Security Groups**
2. Click **Create security group**

| Setting | Value |
|---------|-------|
| **Security group name** | `ielts-prod-alb-sg` |
| **Description** | Allow HTTP/HTTPS from internet |
| **VPC** | ielts-prod-vpc |

**Step 2: Configure Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| HTTP | TCP | 80 | 0.0.0.0/0 | Allow HTTP from anywhere |
| HTTPS | TCP | 443 | 0.0.0.0/0 | Allow HTTPS from anywhere |

**Step 3: Configure Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |



#### Create ECS Frontend Security Group

**Step 1: Create Security Group**

| Setting | Value |
|---------|-------|
| **Security group name** | `ielts-prod-ecs-fe-sg` |
| **Description** | Allow traffic from ALB to Frontend containers |
| **VPC** | ielts-prod-vpc |

**Step 2: Configure Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| Custom TCP | TCP | 3000 | ielts-prod-alb-sg | Allow from ALB |

**Step 3: Configure Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

{{% notice tip %}}
Port 3000 is the default port for Next.js applications. Adjust if your frontend uses a different port.
{{% /notice %}}

#### Create ECS Backend Security Group

**Step 1: Create Security Group**

| Setting | Value |
|---------|-------|
| **Security group name** | `ielts-prod-ecs-be-sg` |
| **Description** | Allow traffic from Frontend and ALB to Backend |
| **VPC** | ielts-prod-vpc |

**Step 2: Configure Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| Custom TCP | TCP | 8080 | ielts-prod-alb-sg | Allow from ALB (API routes) |
| Custom TCP | TCP | 8080 | ielts-prod-ecs-fe-sg | Allow from Frontend |

**Step 3: Configure Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

{{% notice tip %}}
Port 8080 is the default port for Spring Boot applications. The backend needs outbound access for external APIs (Gemini, etc.).
{{% /notice %}}

#### Create RDS Security Group

**Step 1: Create Security Group**

| Setting | Value |
|---------|-------|
| **Security group name** | `ielts-prod-rds-sg` |
| **Description** | Allow PostgreSQL access from ECS Backend |
| **VPC** | ielts-prod-vpc |

**Step 2: Configure Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| PostgreSQL | TCP | 5432 | ielts-prod-ecs-be-sg | Allow from Backend |

**Step 3: Configure Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

{{% notice warning %}}
Never expose RDS directly to the internet. Always use security groups to restrict access to only the application tier.
{{% /notice %}}

#### Create ElastiCache Security Group

**Step 1: Create Security Group**

| Setting | Value |
|---------|-------|
| **Security group name** | `ielts-prod-redis-sg` |
| **Description** | Allow Redis access from ECS Backend |
| **VPC** | ielts-prod-vpc |

**Step 2: Configure Inbound Rules**

| Type | Protocol | Port | Source | Description |
|------|----------|------|--------|-------------|
| Custom TCP | TCP | 6379 | ielts-prod-ecs-be-sg | Allow from Backend |

**Step 3: Configure Outbound Rules**

| Type | Protocol | Port | Destination | Description |
|------|----------|------|-------------|-------------|
| All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

#### AWS CLI Commands

Create all security groups using CLI:

```bash
# Get VPC ID
VPC_ID=$(aws ec2 describe-vpcs --filters "Name=tag:Name,Values=ielts-prod-vpc" --query 'Vpcs[0].VpcId' --output text)

# Create ALB Security Group
ALB_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-alb-sg \
    --description "Allow HTTP/HTTPS from internet" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $ALB_SG --protocol tcp --port 80 --cidr 0.0.0.0/0
aws ec2 authorize-security-group-ingress --group-id $ALB_SG --protocol tcp --port 443 --cidr 0.0.0.0/0

# Create ECS Frontend Security Group
FE_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-ecs-fe-sg \
    --description "Allow traffic from ALB" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $FE_SG --protocol tcp --port 3000 --source-group $ALB_SG

# Create ECS Backend Security Group
BE_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-ecs-be-sg \
    --description "Allow traffic from ALB and Frontend" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $BE_SG --protocol tcp --port 8080 --source-group $ALB_SG
aws ec2 authorize-security-group-ingress --group-id $BE_SG --protocol tcp --port 8080 --source-group $FE_SG

# Create RDS Security Group
RDS_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-rds-sg \
    --description "Allow PostgreSQL from Backend" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $RDS_SG --protocol tcp --port 5432 --source-group $BE_SG

# Create Redis Security Group
REDIS_SG=$(aws ec2 create-security-group \
    --group-name ielts-prod-redis-sg \
    --description "Allow Redis from Backend" \
    --vpc-id $VPC_ID \
    --query 'GroupId' --output text)

aws ec2 authorize-security-group-ingress --group-id $REDIS_SG --protocol tcp --port 6379 --source-group $BE_SG
```

#### Security Group Traffic Flow

```
Internet → ALB (80/443) → Frontend (3000) → Backend (8080) → RDS (5432)
                                               ↓
                                          Redis (6379)
```

#### Security Group Summary

| Security Group | Inbound From | Port | Outbound |
|----------------|--------------|------|----------|
| ALB | Internet | 80, 443 | All |
| ECS Frontend | ALB | 3000 | All |
| ECS Backend | ALB, Frontend | 8080 | All |
| RDS | Backend | 5432 | All |
| Redis | Backend | 6379 | All |



#### Best Practices

{{% notice tip %}}
**Security Group Best Practices:**
- Use security group references instead of IP ranges when possible
- Follow least privilege - only open required ports
- Use descriptive names and descriptions
- Regularly audit security group rules
- Never allow 0.0.0.0/0 to database ports
{{% /notice %}}

#### Next Steps

Proceed to [NAT Gateway](../5.3.4-NAT-Gateway/) to configure outbound internet access for private subnets.

