---
title: "Clean Up Resources"
date:
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Overview

This section guides you through cleaning up all AWS resources created during this workshop to avoid ongoing charges. **Follow the steps in order** as some resources depend on others.

{{% notice warning %}}
**Important:** Deleting resources is irreversible. Make sure you have backed up any data you need before proceeding.
{{% /notice %}}

#### Estimated Time: ~30 minutes

---

### Step 1: Delete CI/CD Pipeline Resources

First, delete the CI/CD pipeline to stop any automated deployments.

1. Navigate to **AWS CodePipeline** console
2. Select your pipeline (e.g., `ielts-pipeline`)
3. Click **Delete pipeline** → Confirm deletion
4. Navigate to **AWS CodeBuild** console
5. Delete all build projects associated with the workshop
6. Delete any **S3 buckets** used for pipeline artifacts

---

### Step 2: Delete ECS Services and Cluster

Stop and delete all ECS services before deleting the cluster.

1. Navigate to **Amazon ECS** console
2. Select your cluster (e.g., `ielts-cluster`)
3. Go to **Services** tab
4. For each service:
   - Select the service
   - Click **Update** → Set **Desired tasks** to `0` → Update
   - Wait for running tasks to stop
   - Click **Delete service** → Confirm
5. After all services are deleted, go back to **Clusters**
6. Select your cluster → **Delete cluster** → Confirm

---

### Step 3: Delete ECR Repositories

Delete container images and repositories.

1. Navigate to **Amazon ECR** console
2. Select each repository:
   - `ielts-frontend`
   - `ielts-backend`
3. Click **Delete** → Type the repository name to confirm

---

### Step 4: Delete AI Service Resources

Delete serverless AI components in this order:

#### 4.1 Delete Lambda Functions
1. Navigate to **AWS Lambda** console
2. Delete each function:
   - `writing-evaluator`
   - `speaking-evaluator`
   - `flashcard-generator`
   - `evaluation-status`
   - `s3-upload`
3. Select function → **Actions** → **Delete** → Confirm

#### 4.2 Delete API Gateway
1. Navigate to **Amazon API Gateway** console
2. Select your API (e.g., `ielts-ai-api`)
3. Click **Actions** → **Delete API** → Confirm

#### 4.3 Delete SQS Queues
1. Navigate to **Amazon SQS** console
2. Delete each queue:
   - `writing-evaluation-queue`
   - `writing-evaluation-dlq`
   - `speaking-evaluation-queue`
   - `speaking-evaluation-dlq`
   - `flashcard-generation-queue`
   - `flashcard-generation-dlq`
3. Select queue → **Delete** → Confirm

#### 4.4 Delete DynamoDB Tables
1. Navigate to **Amazon DynamoDB** console
2. Delete each table:
   - `evaluations`
   - `flashcard-sets`
3. Select table → **Delete** → Confirm deletion

---

### Step 5: Delete Load Balancer Resources

1. Navigate to **EC2** console → **Load Balancers**
2. Select your ALB (e.g., `ielts-alb`)
3. Click **Actions** → **Delete** → Confirm
4. Go to **Target Groups**
5. Delete all target groups associated with the workshop
6. Go to **Listeners** and delete any remaining listeners

---

### Step 6: Delete Database Resources

#### 6.1 Delete RDS Instance
1. Navigate to **Amazon RDS** console
2. Select your database instance
3. Click **Actions** → **Delete**
4. Uncheck **Create final snapshot** (if not needed)
5. Check **I acknowledge...** → **Delete**

{{% notice note %}}
RDS deletion may take 5-10 minutes to complete.
{{% /notice %}}

#### 6.2 Delete ElastiCache (Redis)
1. Navigate to **Amazon ElastiCache** console
2. Select your Redis cluster
3. Click **Delete** → Confirm

#### 6.3 Delete RDS Subnet Group
1. In RDS console, go to **Subnet groups**
2. Select your subnet group → **Delete**

---

### Step 7: Delete S3 Buckets

1. Navigate to **Amazon S3** console
2. For each bucket created in the workshop:
   - `ielts-audio-bucket`
   - `ielts-documents-bucket`
   - Any pipeline artifact buckets
3. Select bucket → **Empty** → Confirm
4. After emptying, select bucket → **Delete** → Confirm

{{% notice warning %}}
S3 buckets must be emptied before they can be deleted.
{{% /notice %}}

---

### Step 8: Delete Secrets Manager Secrets

1. Navigate to **AWS Secrets Manager** console
2. Select each secret created for the workshop
3. Click **Actions** → **Delete secret**
4. Set recovery window to **7 days** (minimum) or choose immediate deletion
5. Confirm deletion

---

### Step 9: Delete CloudWatch Resources

1. Navigate to **Amazon CloudWatch** console
2. Go to **Log groups**
3. Delete log groups:
   - `/aws/lambda/writing-evaluator`
   - `/aws/lambda/speaking-evaluator`
   - `/aws/lambda/flashcard-generator`
   - `/ecs/ielts-frontend`
   - `/ecs/ielts-backend`
4. Go to **Alarms** and delete any created alarms

---

### Step 10: Delete VPC and Network Resources

Delete network resources in this specific order:

#### 10.1 Delete NAT Gateway
1. Navigate to **VPC** console → **NAT Gateways**
2. Select your NAT Gateway
3. Click **Actions** → **Delete NAT gateway** → Confirm
4. Wait for status to change to **Deleted**

#### 10.2 Release Elastic IPs
1. Go to **Elastic IPs**
2. Select any Elastic IPs associated with NAT Gateway
3. Click **Actions** → **Release Elastic IP addresses** → Confirm

#### 10.3 Delete VPC Endpoints
1. Go to **Endpoints**
2. Select all VPC endpoints created for the workshop
3. Click **Actions** → **Delete VPC endpoints** → Confirm

#### 10.4 Delete Security Groups
1. Go to **Security Groups**
2. Delete security groups in this order (due to dependencies):
   - Application security groups first
   - Database security groups
   - Load balancer security groups
   - **Do not delete** the default security group

#### 10.5 Delete Subnets
1. Go to **Subnets**
2. Select all subnets in your workshop VPC
3. Click **Actions** → **Delete subnet** → Confirm

#### 10.6 Delete Route Tables
1. Go to **Route Tables**
2. Delete custom route tables (not the main route table)
3. Select route table → **Actions** → **Delete route table**

#### 10.7 Delete Internet Gateway
1. Go to **Internet Gateways**
2. Select your IGW → **Actions** → **Detach from VPC** → Confirm
3. Select IGW again → **Actions** → **Delete internet gateway** → Confirm

#### 10.8 Delete VPC
1. Go to **Your VPCs**
2. Select your workshop VPC
3. Click **Actions** → **Delete VPC** → Confirm

---

### Step 11: Delete IAM Resources

1. Navigate to **IAM** console
2. Go to **Roles** and delete:
   - `ecsTaskExecutionRole` (if created for this workshop)
   - `ielts-lambda-execution-role`
   - Any other workshop-specific roles
3. Go to **Policies** and delete custom policies created for the workshop

{{% notice warning %}}
Be careful not to delete IAM resources used by other applications.
{{% /notice %}}

---

### Verification Checklist

After completing the cleanup, verify all resources are deleted:

| Resource | Service Console | Status |
|----------|-----------------|--------|
| CodePipeline | CodePipeline | ☐ Deleted |
| ECS Cluster | ECS | ☐ Deleted |
| ECR Repositories | ECR | ☐ Deleted |
| Lambda Functions | Lambda | ☐ Deleted |
| API Gateway | API Gateway | ☐ Deleted |
| SQS Queues | SQS | ☐ Deleted |
| DynamoDB Tables | DynamoDB | ☐ Deleted |
| Load Balancer | EC2 | ☐ Deleted |
| RDS Instance | RDS | ☐ Deleted |
| ElastiCache | ElastiCache | ☐ Deleted |
| S3 Buckets | S3 | ☐ Deleted |
| Secrets | Secrets Manager | ☐ Deleted |
| CloudWatch Logs | CloudWatch | ☐ Deleted |
| NAT Gateway | VPC | ☐ Deleted |
| VPC | VPC | ☐ Deleted |
| IAM Roles | IAM | ☐ Deleted |

---

### Cost Verification

To ensure no unexpected charges:

1. Go to **AWS Billing** console
2. Check **Bills** for the current month
3. Review **Cost Explorer** to verify no active resources
4. Set up a **Budget alert** if you plan to continue using AWS

{{% notice tip %}}
Wait 24-48 hours and check your billing dashboard again to confirm all resources have been cleaned up and no charges are accruing.
{{% /notice %}}


