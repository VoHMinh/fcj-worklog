---
title: "Introduction"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. High-Level Architecture

The **IELTS BandUp** platform is built on a robust, highly available architecture on AWS. The system is designed to handle user traffic securely while providing low-latency access to study materials and AI-powered features.

### 2. Core AWS Services

To achieve the goals of scalability, security, and high availability, we utilize the following key AWS services:

#### Networking & Content Delivery

-   **Amazon VPC (Virtual Private Cloud):** The foundational network layer. We utilize a custom VPC with isolated **Public** and **Private** subnets to strictly control traffic flow.
-   **NAT Gateway:** Allows instances in private subnets (like our Backend containers) to access the internet for updates or external API calls without being exposed to incoming public traffic.
-   **Application Load Balancer (ALB):** Distributes incoming application traffic across multiple targets (containers) in different Availability Zones, ensuring fault tolerance.
-   **Amazon Route 53:** A scalable Domain Name System (DNS) web service used for domain registration and traffic routing.

#### Compute & Containers

-   **Amazon ECS (Elastic Container Service) on Fargate:** A serverless compute engine for containers. We use Fargate to run both our **Next.js Frontend** and **Spring Boot Backend**, removing the need to provision or manage servers.
-   **Amazon ECR (Elastic Container Registry):** A fully managed container registry where we store, manage, and deploy our Docker container images.

#### Database & Storage

-   **Amazon RDS (Relational Database Service):** We use PostgreSQL in a **Multi-AZ deployment** (Primary and Standby) to ensure data durability and disaster recovery for user profiles and test data.
-   **Amazon ElastiCache (Redis):** Acts as an in-memory data store to cache frequent queries and manage user sessions, significantly improving application performance.
-   **Amazon S3 (Simple Storage Service):** Stores static assets, media files (audio for listening tests), and user-generated content securely.

#### AI & Serverless Integration

To power the intelligent features of BandUp (Writing/Speaking Feedback, Flashcard Generation), we use a Serverless approach:

-   **Amazon Bedrock & Google Gemini API:** The core Generative AI models used to analyze user inputs and generate personalized study feedback.
-   **AWS Lambda:** Serverless compute functions that orchestrate the AI workflow, connecting the application to AI models.
-   **Amazon SQS (Simple Queue Service):** Decouples the backend from the AI processing layer, allowing requests to be queued and processed asynchronously to prevent system overload.
-   **Amazon API Gateway:** Acts as the "front door" for the AI services, managing RESTful API calls securely.

#### DevOps & CI/CD

-   **AWS CodePipeline:** Automates the release pipelines for fast and reliable application and infrastructure updates.
-   **AWS CodeBuild:** Compiles source code, runs tests, and produces software packages (Docker images) ready to deploy.

#### Security

-   **AWS WAF (Web Application Firewall):** Protects the web application from common web exploits.
-   **AWS Secrets Manager:** Securely stores and manages sensitive credentials (database passwords, API keys) throughout their lifecycle.

![Architecture Diagram](/images/2-Proposal/AWS-Bandup-Architecture.png)
