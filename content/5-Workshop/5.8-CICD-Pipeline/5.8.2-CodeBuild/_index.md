---
title: "CodeBuild"
date:
weight: 2
chapter: false
pre: " <b> 5.8.2. </b> "
---

#### Overview

Configure AWS CodeBuild for building and testing applications.

#### Create Build Project

| Setting | Value |
|---------|-------|
| **Project name** | `ielts-backend-build` |
| **Environment** | Managed image |
| **Operating system** | Amazon Linux 2 |
| **Runtime** | Standard |
| **Image** | aws/codebuild/amazonlinux2-x86_64-standard:5.0 |
| **Privileged** | Yes (for Docker) |

#### buildspec.yml

```yaml
version: 0.2

phases:
  pre_build:
    commands:
      - echo Logging in to Amazon ECR...
      - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com
  build:
    commands:
      - echo Build started on `date`
      - echo Building the Docker image...
      - docker build -t $IMAGE_REPO_NAME:$IMAGE_TAG .
      - docker tag $IMAGE_REPO_NAME:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
  post_build:
    commands:
      - echo Build completed on `date`
      - echo Pushing the Docker image...
      - docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG
      - printf '[{"name":"backend","imageUri":"%s"}]' $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$IMAGE_REPO_NAME:$IMAGE_TAG > imagedefinitions.json

artifacts:
  files: imagedefinitions.json
```

#### Environment Variables

| Variable | Value |
|----------|-------|
| `AWS_ACCOUNT_ID` | Your account ID |
| `AWS_DEFAULT_REGION` | ap-southeast-1 |
| `IMAGE_REPO_NAME` | ielts-backend |
| `IMAGE_TAG` | latest |

#### Next Steps

Proceed to [Security & IAM](../../5.9-Security-IAM/).

