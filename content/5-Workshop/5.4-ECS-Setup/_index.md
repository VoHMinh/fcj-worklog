---
title: "ECS & Container Setup"
date:
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Overview

In this section, you will deploy the IELTS Self-Learning Web System applications using Amazon ECS (Elastic Container Service) with Fargate launch type. The architecture implements an active-passive Multi-AZ deployment pattern for high availability.

#### ECS Architecture

![ECS Architecture](/images/2-Proposal/AWS-Bandup-Architecture.png)

**Deployment Strategy:**
- **Active-Passive Multi-AZ**: Primary services run in AZ-1, standby in AZ-2
- **Fargate Launch Type**: Serverless container management
- **Service Connect**: Internal service discovery between Frontend and Backend

**Container Services:**

| Service | Container | Port | Replicas (Active) | Replicas (Standby) |
|---------|-----------|------|-------------------|-------------------|
| Frontend | Next.js 14+ | 3000 | 2 | 1 |
| Backend | Spring Boot 3.x | 8080 | 2 | 1 |

#### ECS Components

**1. Amazon ECR (Elastic Container Registry)**
- Private container image repository
- Stores Frontend (Next.js) and Backend (Spring Boot) images
- Integrated with ECS for seamless image pulls
- Image scanning for vulnerabilities

**2. Task Definitions**
- Blueprint for running containers
- Defines CPU, memory, environment variables
- Configures logging to CloudWatch
- Sets up IAM roles for task execution

**3. ECS Cluster**
- Logical grouping of services and tasks
- Cluster capacity providers (Fargate, Fargate Spot)
- Service Connect namespace for internal discovery

**4. ECS Services**
- Maintains desired number of running tasks
- Integrates with ALB for load balancing
- Auto Scaling based on metrics
- Rolling deployment for zero-downtime updates

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

#### What You Will Create

In this section, you will:

1. Create ECR repositories for Frontend and Backend images
2. Build and push Docker images to ECR
3. Create ECS Task Definitions with proper configurations
4. Set up an ECS Cluster with Fargate capacity
5. Deploy ECS Services with ALB integration
6. Configure Service Connect for internal communication

#### Estimated Time

| Task | Time |
|------|------|
| Create ECR Repositories | 10 minutes |
| Build & Push Images | 15 minutes |
| Create Task Definitions | 15 minutes |
| Set up ECS Cluster | 5 minutes |
| Deploy ECS Services | 15 minutes |
| **Total** | **~60 minutes** |

#### Content

1. [ECR Repository](5.4.1-ECR-Repository/)
2. [Task Definition](5.4.2-Task-Definition/)
3. [ECS Cluster](5.4.3-ECS-Cluster/)
4. [ECS Service](5.4.4-ECS-Service/)

#### Best Practices

{{% notice tip %}}
**ECS Best Practices:**
- Use Fargate for serverless container management
- Implement health checks for automatic recovery
- Use task IAM roles instead of hardcoded credentials
- Enable container insights for monitoring
- Use Service Connect for internal service discovery
- Implement proper logging to CloudWatch
{{% /notice %}}

