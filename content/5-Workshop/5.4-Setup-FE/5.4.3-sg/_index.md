---
title: "Configure Security Group"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

Our Frontend containers run in Private Subnets. To allow them to receive traffic, we must configure a Security Group that acts as a firewall.

For security best practices, we will **only allow traffic from the Application Load Balancer (ALB)** on port 3000. Direct access from the internet or other sources will be blocked.

### 1. Create Security Group

1.  Navigate to **EC2 Dashboard** > **Security Groups** > **Create security group**.
2.  **Basic details:**
    -   **Security group name:** `ecs-private-sg`.
    -   **Description:** `security group for ecs`.
    -   **VPC:** Select `band-up-vpc`.

![Security Group Basic Details](/images/5-Workshop/5.4-fe/fe-sg-1.png)

### 2. Configure Inbound Rules

This is the most critical step. We need to allow the ALB to talk to our Next.js application.

1.  **Inbound rules:** Click **Add rule**.
    -   **Type:** `Custom TCP`.
    -   **Port range:** `3000` (The port our Next.js app listens on).
    -   **Source:** Select **Custom** and choose the Security Group ID of your ALB (e.g., `alb-sg`).
        -   _Note: By selecting the ALB's Security Group ID instead of an IP range, we ensure that only traffic originating from our Load Balancer is accepted._

![Configure Inbound Rules](/images/5-Workshop/5.4-fe/fe-sg-2.png)

2.  **Outbound rules:** Leave the default settings (Allow all traffic) to enable the container to download packages or communicate with external APIs.
3.  Click **Create security group**.

The Security Group is now ready to be attached to our ECS Tasks in the next step.
