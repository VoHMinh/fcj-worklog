---
title: "Prerequiste"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

This workshop is designed for DevOps Engineers, Cloud Architects, and Full-stack Developers who aim to deploy a modern, AI-integrated application on AWS.

To successfully complete this workshop, participants are expected to possess the following knowledge, skills, and tools.

### 1. Technical Knowledge Requirements

#### AWS Fundamentals

-   **Console Navigation:** Familiarity with the [AWS Management Console](https://aws.amazon.com/console/).
-   **Core Services:** Basic understanding of compute services like [Amazon EC2](https://aws.amazon.com/ec2/) and [AWS Fargate](https://aws.amazon.com/fargate/), networking concepts within [Amazon VPC](https://aws.amazon.com/vpc/), and storage using [Amazon S3](https://aws.amazon.com/s3/).
-   **IAM & Security:** Understanding of [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/), specifically Roles, Policies, and the principle of least privilege.

#### Containerization & Orchestration

-   **Docker:** Proficiency in creating `Dockerfiles`, building images, and running containers locally. Understanding of concepts like layers, exposing ports, and environment variables is essential. Refer to the [Docker Documentation](https://docs.docker.com/).
-   **ECS Concepts:** Familiarity with [Amazon ECS](https://aws.amazon.com/ecs/) terminology including _Task Definitions_, _Services_, _Clusters_, and the operational differences between EC2 and Fargate launch types.

#### DevOps & CI/CD

-   **Git:** Proficiency in version control (commit, push, branching) to manage source code and trigger automated pipelines.
-   **CI/CD Flow:** Understanding of Continuous Integration and Continuous Delivery principles using tools like [AWS CodePipeline](https://aws.amazon.com/codepipeline/) and [AWS CodeBuild](https://aws.amazon.com/codebuild/).

#### Networking Basics

-   **Protocols:** Understanding of HTTP/HTTPS, DNS resolution with [Amazon Route 53](https://aws.amazon.com/route53/), and load balancing concepts using [Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/).
-   **Network Security:** Knowledge of IP addressing (CIDR blocks) and traffic control using [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html).

### 2. Environment Setup

Before starting the workshop, ensure your local development environment is equipped with the following tools:

-   **AWS Account:** An active AWS account with **Administrator access** to provision resources.
-   **IDE:** A code editor such as [Visual Studio Code](https://code.visualstudio.com/) or IntelliJ IDEA.
-   **Command Line Tools:**
    -   **AWS CLI (v2):** Installed and configured with your account credentials. [Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
    -   **Git:** Installed for cloning repositories. [Downloads](https://git-scm.com/downloads).
    -   **Docker Desktop:** Running locally to inspect or build images if necessary. [Get Docker](https://www.docker.com/products/docker-desktop/).

### 3. Service Quotas & Costs

{{% notice warning %}}
**Cost Alert:** This workshop utilizes resources that **are not** covered by the AWS Free Tier, including:

-   **NAT Gateways** (Hourly charge + Data processing fees)
-   **Application Load Balancers**
-   **ECS Fargate Tasks** (vCPU/Memory usage)
-   **Amazon RDS & ElastiCache**
    {{% /notice %}}

Please ensure you clean up resources immediately after finishing the workshop to avoid unexpected charges. A cleanup guide is provided at the end of this documentation.
