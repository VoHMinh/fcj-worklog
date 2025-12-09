---
title: "Workshop"
date:
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# IELTS Self-Learning Web System - AWS Infrastructure Workshop

#### Overview

This comprehensive workshop guides you through building a **production-ready AWS infrastructure** for the IELTS Self-Learning Web System. You will learn how to deploy a highly available, scalable, and secure web application using modern AWS services and best practices.

The architecture implements an **active-passive Multi-AZ deployment** pattern on Amazon ECS, with a serverless AI service layer for intelligent assessment and content generation.

![Architecture Overview](/images/2-Proposal/AWS-Bandup-Architecture.png)

#### What You Will Build

By completing this workshop, you will have deployed:

| Component | AWS Service | Purpose |
|-----------|-------------|---------|
| **Network Layer** | VPC, Subnets, NAT Gateway | Isolated, secure network infrastructure |
| **Container Platform** | ECS Fargate, ECR | Serverless container orchestration |
| **Load Balancing** | ALB, Route 53, ACM | Traffic distribution and SSL termination |
| **Data Layer** | RDS PostgreSQL, ElastiCache, S3 | Relational database, caching, object storage |
| **AI Services** | API Gateway, SQS, Lambda, DynamoDB | Serverless AI processing pipeline |
| **CI/CD** | CodePipeline, CodeBuild | Automated deployment pipeline |
| **Security** | IAM, Secrets Manager, WAF | Identity management and protection |
| **Monitoring** | CloudWatch Logs, Alarms | Observability and alerting |

#### Architecture Highlights

**High Availability Design:**
- Multi-AZ deployment across two Availability Zones
- Active-passive failover for ECS services
- RDS Multi-AZ with automatic failover
- Application Load Balancer with health checks

**Serverless AI Architecture:**
- API Gateway for RESTful AI endpoints
- SQS for asynchronous message processing
- Lambda functions for Writing Assessment, Speaking Assessment, and RAG-based Flashcard Generation
- DynamoDB for storing AI results
- Amazon Bedrock integration for AI models (Gemma 3 12B, Titan Embeddings)
- Google Gemini API for smart query generation

**Security Best Practices:**
- Private subnets for application and database tiers
- Security groups with least-privilege access
- AWS WAF for application-level protection
- Secrets Manager for credential management
- IAM roles with minimal required permissions

#### Prerequisites

Before starting this workshop, ensure you have:
- An AWS account with appropriate permissions
- AWS CLI installed and configured
- Basic understanding of AWS services (VPC, EC2, ECS)
- Docker installed locally for container builds
- Git for version control

#### Time to Complete

| Section | Estimated Time |
|---------|---------------|
| Prerequisites | 15 minutes |
| VPC & Network Setup | 30 minutes |
| ECS & Container Setup | 45 minutes |
| Load Balancer Configuration | 30 minutes |
| Database & Storage Setup | 45 minutes |
| AI Service Architecture | 60 minutes |
| CI/CD Pipeline | 30 minutes |
| Security & IAM | 30 minutes |
| Monitoring Setup | 20 minutes |
| **Total** | **~5 hours** |

#### Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisites/)
3. [VPC & Network Setup](5.3-VPC-Network/)
4. [ECS & Container Setup](5.4-ECS-Setup/)
5. [Load Balancer Configuration](5.5-Load-Balancer/)
6. [Database & Storage Setup](5.6-Database-Storage/)
7. [AI Service Architecture](5.7-AI-Service/)
8. [CI/CD Pipeline](5.8-CICD-Pipeline/)
9. [Security & IAM](5.9-Security-IAM/)
10. [Monitoring & Logging](5.10-Monitoring/)
11. [Clean Up](5.11-Cleanup/)

