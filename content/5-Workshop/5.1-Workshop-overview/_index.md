---
title: "Workshop Overview"
date:
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Project Introduction

The **IELTS Self-Learning Web System** is a comprehensive online platform designed to support students in their independent IELTS preparation journey. This workshop focuses on deploying the complete AWS infrastructure required to run this application in a production environment.

#### System Components

The platform consists of five major functional modules:

| Module | Description |
|--------|-------------|
| **User Management** | Multi-tier authentication (Guest, Member, Premium, Admin), OAuth integration |
| **Blog Platform** | Community-driven educational content with CRUD, filtering, comments |
| **Study Rooms** | Virtual spaces with WebRTC video/voice, Pomodoro timer, screen sharing |
| **Practice Tests** | IELTS mock tests with AI-powered Speaking/Writing assessment |
| **Flashcard System** | Intelligent flashcards with RAG-based AI generation |

#### Architecture Overview

The architecture follows AWS Well-Architected Framework principles, implementing:

**1. Network Layer (VPC)**
- Custom VPC spanning two Availability Zones (AZ-1 and AZ-2)
- Public subnets for ALB, NAT Gateways
- Private subnets for ECS tasks, RDS, ElastiCache
- Security Groups and Network ACLs for defense in depth

**2. Application Layer (ECS)**
- **Frontend**: Next.js 14+ containerized application
- **Backend**: Spring Boot 3.x monolithic REST API
- Active-passive deployment pattern for high availability
- Auto Scaling based on CPU/memory utilization

**3. Data Layer**
- **Amazon RDS PostgreSQL**: Primary database with Multi-AZ standby
- **Amazon ElastiCache (Redis)**: Session management and caching
- **Amazon S3**: Media storage with CloudFront CDN
- **Amazon DynamoDB**: AI assessment results storage

**4. AI Service Layer (Serverless)**
- **Amazon API Gateway**: Entry point for AI requests
- **Amazon SQS**: Message queue for asynchronous processing
- **AWS Lambda**: Three specialized functions:
  - Writing Assessment (evaluates grammar, vocabulary, coherence)
  - Speaking Assessment (transcription + evaluation)
  - Flashcard Generation (RAG-based with Titan Embeddings)
- **Amazon Bedrock**: Gemma 3 12B model and Titan Embeddings
- **Google Gemini API**: Smart query generation

![Architecture Diagram](/images/2-Proposal/AWS-Bandup-Architecture.png)

#### Traffic Flow

The following numbered flow corresponds to the architecture diagram:

| Step | Component | Action |
|------|-----------|--------|
| **1** | User | Accesses `https://bandup.bughunters.site` |
| **2** | Route 53 | DNS resolution with health checks |
| **2** | ACM | SSL/TLS certificate validation |
| **3** | AWS WAF | Web Application Firewall inspection |
| **3** | ALB | Application Load Balancer receives traffic |
| **4** | Traffic Division | ALB routes to active AZ (AZ-1) |
| **5** | ECS Services | Frontend and Backend containers process requests |
| **6** | Service Connect | Internal service discovery between FE and BE |
| **7** | ElastiCache | Redis caching layer |
| **8** | RDS Primary | PostgreSQL database queries |
| **9** | API Gateway | AI service requests entry point |
| **10** | Queue | SQS receives async AI processing requests |
| **11** | SQS Queues | Three separate queues for Writing, Speaking, Flashcard |
| **12** | Lambda Functions | Processing Writing/Speaking evaluation, Flashcard generation |
| **13** | Secrets Manager | Secure credential retrieval |
| **14** | Amazon Bedrock | AI model inference (Gemma 3 12B) |
| **15** | DynamoDB | Store AI results |
| **16** | CloudWatch | Logs and metrics collection |

#### AI Service Architecture Detail

The AI service implements a fully serverless pattern:

```
User Request → API Gateway → SQS Queue → Lambda Function → AI Model → DynamoDB → Response
```

**Three Lambda Functions:**

1. **Writing Evaluate**: Analyzes IELTS writing samples
   - Grammar and vocabulary assessment
   - Task achievement scoring
   - Coherence and cohesion evaluation
   - Band score prediction (0-9)

2. **Speaking Evaluate**: Processes audio recordings
   - Speech-to-text transcription
   - Pronunciation analysis
   - Fluency and coherence scoring
   - Lexical resource evaluation

3. **Flashcard Generate**: RAG-based intelligent flashcard creation
   - Document chunking and embedding (Titan V2)
   - Smart query generation (Google Gemini)
   - Context-aware flashcard generation
   - Storage in DynamoDB

#### CI/CD Pipeline

The deployment pipeline automates the entire release process:

```
Developer → GitLab/GitHub → CodePipeline → CodeBuild → ECR → ECS
```

1. Developer creates new tag/pushes code
2. GitLab Webhook triggers AWS CodePipeline
3. CodeBuild compiles code and runs tests
4. Docker images are built and pushed to ECR
5. ECS service updates with new task definitions
6. Rolling deployment ensures zero-downtime updates

#### Cost Optimization Strategies

This architecture is designed for cost efficiency:

- **ECS Fargate**: Pay only for running containers
- **Active-Passive Multi-AZ**: Standby resources consume minimal cost
- **Lambda**: Pay-per-invocation for AI processing
- **DynamoDB On-Demand**: Scales with actual usage
- **S3 Lifecycle Policies**: Automatic tiering to cheaper storage
- **Reserved Capacity**: Available for predictable workloads

#### Workshop Learning Objectives

By completing this workshop, you will learn:

1. **Network Design**: How to create a secure VPC with public/private subnets
2. **Container Orchestration**: Deploying applications on ECS Fargate
3. **Database Management**: Setting up RDS Multi-AZ and ElastiCache
4. **Serverless Architecture**: Building event-driven AI processing pipelines
5. **CI/CD Best Practices**: Automated deployments with CodePipeline
6. **Security Implementation**: IAM roles, Secrets Manager, WAF configuration
7. **Observability**: CloudWatch monitoring and alerting

#### Next Steps

Proceed to [Prerequisites](../5.2-Prerequisites/) to set up your environment before starting the hands-on deployment.

