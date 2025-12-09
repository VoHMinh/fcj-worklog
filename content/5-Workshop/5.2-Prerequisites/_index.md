---
title: "Prerequisites"
date:
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Overview

Before starting this workshop, you need to prepare your environment with the necessary tools, accounts, and configurations. This section covers all prerequisites required to successfully deploy the IELTS Self-Learning Web System infrastructure.

#### AWS Account Requirements

**1. AWS Account Setup**

You need an AWS account with the following:
- **Administrator Access** or IAM user with sufficient permissions
- Access to create resources in your preferred region (recommended: `ap-southeast-1` Singapore)
- Billing alerts configured to monitor costs

**2. Required IAM Permissions**

Your IAM user/role should have permissions for:
- Amazon VPC (full access)
- Amazon ECS (full access)
- Amazon ECR (full access)
- Amazon RDS (full access)
- Amazon ElastiCache (full access)
- Amazon S3 (full access)
- Amazon CloudFront (full access)
- Amazon API Gateway (full access)
- Amazon SQS (full access)
- AWS Lambda (full access)
- Amazon DynamoDB (full access)
- Amazon Bedrock (full access)
- AWS IAM (limited - for creating service roles)
- AWS Secrets Manager (full access)
- AWS WAF (full access)
- Amazon CloudWatch (full access)
- AWS CodePipeline (full access)
- AWS CodeBuild (full access)
- Amazon Route 53 (full access)
- AWS Certificate Manager (full access)

{{% notice tip %}}
For learning purposes, you can use the `AdministratorAccess` managed policy. For production environments, follow the principle of least privilege.
{{% /notice %}}

#### Local Development Tools

**1. AWS CLI**

Install and configure AWS CLI v2:

```bash
# Windows (PowerShell)
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi

# macOS
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /

# Linux
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

Configure AWS CLI:
```bash
aws configure
# Enter your Access Key ID, Secret Access Key, Region (ap-southeast-1), Output format (json)
```

Verify installation:
```bash
aws --version
aws sts get-caller-identity
```

**2. Docker**

Install Docker Desktop for container builds:
- **Windows/macOS**: Download from [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- **Linux**: Follow official installation guide

Verify installation:
```bash
docker --version
docker run hello-world
```

**3. Git**

Install Git for version control:
```bash
# Windows
winget install Git.Git

# macOS
brew install git

# Linux (Ubuntu/Debian)
sudo apt-get install git
```

Verify installation:
```bash
git --version
```

**4. Node.js (for Frontend)**

Install Node.js 18+ for Next.js development:
```bash
# Using nvm (recommended)
nvm install 18
nvm use 18

# Verify
node --version
npm --version
```

**5. Java & Maven (for Backend)**

Install Java 17+ and Maven for Spring Boot:
```bash
# Windows (using Chocolatey)
choco install openjdk17 maven

# macOS
brew install openjdk@17 maven

# Linux (Ubuntu/Debian)
sudo apt-get install openjdk-17-jdk maven
```

Verify installation:
```bash
java --version
mvn --version
```

#### Domain and SSL Prerequisites

**1. Domain Name**

For this workshop, you need:
- A registered domain name (e.g., `bughunters.site`)
- Access to DNS management (Route 53 or external DNS provider)

{{% notice info %}}
If you don't have a domain, you can skip the Route 53 and ACM sections and access the application via the ALB DNS name directly.
{{% /notice %}}

**2. Route 53 Hosted Zone**

If using Route 53 for DNS:
1. Go to Route 53 Console
2. Create a Hosted Zone for your domain
3. Update your domain registrar's nameservers to Route 53's nameservers

#### Third-Party API Keys

**1. Google Gemini API Key**

For AI-powered assessments and smart query generation:
1. Go to [Google AI Studio](https://aistudio.google.com/)
2. Sign in with your Google account
3. Create an API key
4. Save the API key securely (you'll store it in Secrets Manager later)

{{% notice warning %}}
Keep your API keys secure. Never commit them to version control.
{{% /notice %}}

**2. OAuth Credentials (Optional)**

For social login (Google, Facebook):
- **Google OAuth**: Create credentials in [Google Cloud Console](https://console.cloud.google.com/)
- **Facebook OAuth**: Create app in [Facebook Developers](https://developers.facebook.com/)

#### Project Source Code

Clone the project repositories:

```bash

git clone https://github.com/Ojt-BugHunters/band-up.git

```
#### AWS Region Selection

For this workshop, we recommend using **ap-southeast-1 (Singapore)** region for:
- Low latency for Southeast Asian users
- Full availability of all required services
- Amazon Bedrock availability

Verify Bedrock availability in your chosen region:
```bash
aws bedrock list-foundation-models --region ap-southeast-1 --query 'modelSummaries[?contains(modelId, `titan`)].modelId'
```

#### Cost Estimation

Estimated costs for running this workshop:

| Service | Estimated Cost (per day) |
|---------|-------------------------|
| ECS Fargate | $2-5 |
| RDS PostgreSQL | $3-5 |
| ElastiCache | $0.50-1 |
| ALB | $0.80 |
| NAT Gateway | $1-2 |
| Lambda | $0.10-0.50 |
| Other services | $1-2 |
| **Total** | **~$10-15/day** |

{{% notice warning %}}
Remember to clean up resources after completing the workshop to avoid unnecessary charges. The cleanup instructions are provided in Section 5.11.
{{% /notice %}}

#### Environment Checklist

Before proceeding, ensure you have:

- [ ] AWS account with sufficient permissions
- [ ] AWS CLI installed and configured
- [ ] Docker installed and running
- [ ] Git installed
- [ ] Node.js 18+ installed
- [ ] Java 17+ and Maven installed
- [ ] Google Gemini API key obtained
- [ ] Domain name (optional but recommended)
- [ ] Source code repositories cloned

#### Next Steps

Once you have completed all prerequisites, proceed to [VPC & Network Setup](../5.3-VPC-Network/) to begin building the network infrastructure.

