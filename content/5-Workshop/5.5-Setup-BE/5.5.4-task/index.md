---
title: "Create Service & Task"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

In the final step of the backend deployment, we define the runtime configuration for the Spring Boot application and launch it as a stable ECS Service.

### 1. Create Task Definition

1.  Navigate to **Amazon ECS** > **Task definitions** > **Create new task definition**.
2.  **Task definition configuration:**
    -   **Family:** `bandup-backend`.
    -   **Launch type:** `AWS Fargate`.
    -   **OS/Architecture:** `Linux/X86_64`.
    -   **Task size:** `1 vCPU` and `2 GB` Memory.
        -   _Note: Java applications (Spring Boot) generally require more memory than Node.js apps to handle the JVM heap effectively._
    -   **Task Role & Execution Role:** Select `ecsTaskExecutionRole`.

![Task Definition Overview](/images/5-Workshop/5.5-be/be-task-1.png)

3.  **Container Details:**

    -   **Name:** `bandup-be-container`.
    -   **Image URI:** Enter the ECR URI (`.../band-up-backend:v1.0.0`).
    -   **Container Port:** `8080` (Default Spring Boot port).

4.  **Environment Configuration (Best Practice):**
    Instead of manually entering sensitive variables (Database URL, User, Password) in plain text, we load them from a secure file stored in S3.
    -   **Environment files:** Add the S3 ARN of your `.env` file (e.g., `arn:aws:s3:::bandup2025-fcj/.env`).
    -   _Requirement:_ Ensure your `ecsTaskExecutionRole` has permission to read this S3 object.

![Container and S3 Env Config](/images/5-Workshop/5.5-be/be-task-2.png)

### 2. Create ECS Service

Deploy the task definition to the cluster.

1.  Navigate to `bandup-cluster` > **Services** > **Create**.
2.  **Deployment configuration:**
    -   **Compute options:** `FARGATE`.
    -   **Family:** `bandup-backend` (Revision 7 or latest).
    -   **Service name:** `bandup-backend-service`.
    -   **Desired tasks:** `1`.

![Service Deployment Config](/images/5-Workshop/5.5-be/be-service-1.png)

3.  **Networking:**
    -   **VPC:** `band-up-vpc`.
    -   **Subnets:** Select **Private Subnets** (`private-subnet-1`, `private-subnet-2`).
    -   **Security group:** Select `ecs-backend-sg` (Created in step 5.5.2).

![Service Networking](/images/5-Workshop/5.5-be/be-service-2.png)

4.  **Load Balancing:**
    -   **Load balancer:** Select `bandup-public-alb`.
    -   **Listener:** Use existing listener `80:HTTP`.
    -   **Target group:** Create a new target group `target-bandup-be`.
    -   **Container info:** Ensure traffic is routed to port `8080`.

![Service Load Balancing](/images/5-Workshop/5.5-be/be-service-3.png)

5.  Click **Create**. The service will provision the Fargate tasks, pull the image, load the environment variables from S3, and register with the ALB.
