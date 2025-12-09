---
title: "Create Task Definition & Service"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

In this final step for the frontend, we define how our application container should run (Task Definition) and deploy it as a scalable service (ECS Service) connected to our Load Balancer.

### 1. Create Task Definition

The Task Definition serves as a blueprint for our application.

1.  Navigate to **Amazon ECS** > **Task definitions** > **Create new task definition**.
2.  **Task definition configuration:**
    -   **Task definition family:** `bandup-frontend`.
    -   **Launch type:** `AWS Fargate`.
    -   **OS/Architecture:** `Linux/X86_64`.
    -   **Task size:** `.5 vCPU` and `1 GB` Memory (Sufficient for our Next.js frontend).
    -   **Task Role & Task Execution Role:** Select `ecsTaskExecutionRole` (Created in section 5.3.3).

![Task Definition Infrastructure](/images/5-Workshop/5.4-fe/fe-task-1.png)

3.  **Container details:**
    -   **Name:** `bandup-fe-container`.
    -   **Image URI:** Enter the ECR URI we pushed earlier (e.g., `.../band-up-frontend:v1.0.0`).
    -   **Container port:** `3000`.
4.  Click **Create**.

![Container Configuration](/images/5-Workshop/5.4-fe/fe-task-2.png)

### 2. Create ECS Service

Now we deploy this blueprint into our Cluster.

1.  Navigate to **Clusters** > Select `bandup-cluster`.
2.  In the **Services** tab, click **Create**.

![Cluster Overview](/images/5-Workshop/5.4-fe/fe-cluster-1.png)

**Step 1: Environment**

-   **Compute options:** `Launch type` -> `FARGATE`.
-   **Task definition:** `bandup-frontend` (Revision 1).
-   **Service name:** `bandup-frontend-service`.
-   **Desired tasks:** `1`.

![Service Environment Config](/images/5-Workshop/5.4-fe/fe-service-1.png)

**Step 2: Networking**

-   **VPC:** `band-up-vpc`.
-   **Subnets:** Select the **Private Subnets** (`private-subnet-1`, `private-subnet-2`).
-   **Security group:** Select `ecs-private-sg` (This allows traffic from ALB).

![Service Networking Config](/images/5-Workshop/5.4-fe/fe-service-2.png)

**Step 3: Load Balancing**

-   **Load balancer type:** Application Load Balancer.
-   **Load balancer:** Select `bandup-public-alb`.
-   **Container to load balance:** `bandup-fe-container 3000:3000`.

![Load Balancing Selection](/images/5-Workshop/5.4-fe/fe-service-3.png)

-   **Listener:** Create a new listener on Port `80` (HTTP).
-   **Target group:** Use an existing target group -> `target-bandup-fe`.

![Listener Configuration](/images/5-Workshop/5.4-fe/fe-service-5.png)

3.  Click **Create**. The service will start deploying your container. Wait until the status changes to **Active** and the Task status is **Running**.

![Service Running Successfully](/images/5-Workshop/5.4-fe/fe-service-6.png)

### 3. Verify Deployment

Once the service is stable, open your web browser and navigate to the **DNS name** of your Application Load Balancer.

You should see the **IELTS BandUp** landing page loading successfully, served from your container in the private subnet.

![Access via ALB DNS](/images/5-Workshop/5.4-fe/check-fe-using-alb.png)
![BandUp Landing Page](/images/5-Workshop/5.4-fe/result.png)
