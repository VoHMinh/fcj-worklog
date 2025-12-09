---
title: "ECR Repository"
date:
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Overview

Amazon Elastic Container Registry (ECR) is a fully managed container registry that stores, manages, and deploys Docker container images. In this step, you will create ECR repositories for the Frontend and Backend applications.

#### Create ECR Repositories

**Step 1: Navigate to ECR Console**

1. Sign in to AWS Management Console
2. Navigate to **Amazon ECR** service
3. Click **Create repository**

**Step 2: Create Frontend Repository**

| Setting | Value |
|---------|-------|
| **Visibility settings** | Private |
| **Repository name** | `ielts-frontend` |
| **Tag immutability** | Disabled |
| **Scan on push** | Enabled |
| **Encryption** | AES-256 |

Click **Create repository**.

**Step 3: Create Backend Repository**

Repeat the process:

| Setting | Value |
|---------|-------|
| **Visibility settings** | Private |
| **Repository name** | `ielts-backend` |
| **Tag immutability** | Disabled |
| **Scan on push** | Enabled |
| **Encryption** | AES-256 |

#### AWS CLI Commands

```bash
# Create Frontend repository
aws ecr create-repository \
    --repository-name ielts-frontend \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256 \
    --region ap-southeast-1

# Create Backend repository
aws ecr create-repository \
    --repository-name ielts-backend \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256 \
    --region ap-southeast-1
```

#### Configure Lifecycle Policy

Set up lifecycle policies to automatically clean up old images:

```bash
# Lifecycle policy JSON
cat > lifecycle-policy.json << 'EOF'
{
    "rules": [
        {
            "rulePriority": 1,
            "description": "Keep last 10 images",
            "selection": {
                "tagStatus": "any",
                "countType": "imageCountMoreThan",
                "countNumber": 10
            },
            "action": {
                "type": "expire"
            }
        }
    ]
}
EOF

# Apply to both repositories
aws ecr put-lifecycle-policy \
    --repository-name ielts-frontend \
    --lifecycle-policy-text file://lifecycle-policy.json

aws ecr put-lifecycle-policy \
    --repository-name ielts-backend \
    --lifecycle-policy-text file://lifecycle-policy.json
```

#### Build and Push Docker Images

**Step 1: Authenticate Docker with ECR**

```bash
# Get account ID and region
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
AWS_REGION=ap-southeast-1

# Login to ECR
aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
```

**Step 2: Build Frontend Image**

```bash
# Navigate to frontend project
cd ielts-frontend

# Create Dockerfile if not exists
cat > Dockerfile << 'EOF'
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/next.config.js ./
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

EXPOSE 3000
ENV PORT 3000
CMD ["node", "server.js"]
EOF

# Build image
docker build -t ielts-frontend:latest .

# Tag image
docker tag ielts-frontend:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-frontend:latest

# Push to ECR
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-frontend:latest
```

**Step 3: Build Backend Image**

```bash
# Navigate to backend project
cd ielts-backend

# Create Dockerfile if not exists
cat > Dockerfile << 'EOF'
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
EOF

# Build image
docker build -t ielts-backend:latest .

# Tag image
docker tag ielts-backend:latest $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-backend:latest

# Push to ECR
docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com/ielts-backend:latest
```

#### Verify Images

```bash
# List images in frontend repository
aws ecr describe-images --repository-name ielts-frontend

# List images in backend repository
aws ecr describe-images --repository-name ielts-backend
```

#### Image Vulnerability Scanning

After pushing, ECR automatically scans images for vulnerabilities:

1. Navigate to **ECR** → **Repositories**
2. Select a repository
3. Click on the image tag
4. View **Vulnerabilities** tab

{{% notice warning %}}
Address any CRITICAL or HIGH severity vulnerabilities before deploying to production.
{{% /notice %}}

#### ECR Repository Summary

| Repository | URI | Image |
|------------|-----|-------|
| ielts-frontend | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-frontend` | Next.js app |
| ielts-backend | `{account}.dkr.ecr.{region}.amazonaws.com/ielts-backend` | Spring Boot app |

#### Next Steps

Proceed to [Task Definition](../5.4.2-Task-Definition/) to create ECS task definitions for running containers.

