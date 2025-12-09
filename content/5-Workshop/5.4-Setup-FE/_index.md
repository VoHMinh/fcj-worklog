---
title: "Frontend Deployment (ECS Fargate)"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Overview

In this section, we will deploy the **IELTS BandUp Frontend** (Next.js application) to the AWS Cloud.

We will utilize **Amazon Elastic Container Service (ECS)** with the **Fargate** launch type. This serverless approach allows us to run containers without managing the underlying EC2 instances. The frontend service will be placed in Private Subnets for security but will be accessible to users via the Application Load Balancer (ALB) we configured in the previous section.

### Implementation Steps

To successfully deploy the frontend, we will follow this workflow:

1.  **Container Registry (ECR):** Create a repository to store our Docker images and push the local application code to AWS.
2.  **Security Configuration:** Define specific security group rules allowing the Frontend container to receive traffic only from the ALB.
3.  **ECS Task & Service:** Define the blueprint (Task Definition) for our container (CPU, Memory, Environment Variables) and launch it as a stable Service.

### Content

1. [Dockerize Application](5.4.1-docker/)
2. [Setup ECR & Push Image](5.4.2-ecr/)
3. [Configure Security Group](5.4.3-sg/)
4. [Create Task Definition & Service](5.4.4-task/)
