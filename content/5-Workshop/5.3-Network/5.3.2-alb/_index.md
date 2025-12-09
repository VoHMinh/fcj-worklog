---
title: "Application Load Balancer (ALB)"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

The Application Load Balancer (ALB) serves as the entry point for all incoming traffic to our platform. It distributes requests to the appropriate containers (Frontend or Backend) and handles SSL termination.

### 1. Create Security Group for ALB

Before creating the load balancer, we need a firewall rule that allows public access.

1.  Go to **EC2 Dashboard** > **Security Groups** > **Create security group**.
2.  **Security group name:** `alb-sg`.
3.  **Description:** `Allow http and https traffic`.
4.  **VPC:** Select `band-up-vpc`.
5.  **Inbound rules:** Add the following rules to allow traffic from anywhere:
    -   **Type:** `HTTP` | **Port:** `80` | **Source:** `Anywhere-IPv4` (`0.0.0.0/0`).
    -   **Type:** `HTTPS` | **Port:** `443` | **Source:** `Anywhere-IPv4` (`0.0.0.0/0`).
6.  Click **Create security group**.

![Create Security Group for ALB](/images/5-Workshop/5.3-Network/alb-sg.png)

### 2. Create Target Group

The ALB needs to know where to route the traffic. We will create a Target Group for our Frontend service first.

1.  Go to **EC2 Dashboard** > **Target groups** > **Create target group**.
2.  **Choose a target type:** Select **IP addresses** (Required for ECS Fargate).
3.  **Target group name:** `target-bandup-fe`.
4.  **Protocol:** `HTTP`.
5.  **Port:** `3000` (Our Next.js frontend runs on port 3000).
6.  **VPC:** Select `band-up-vpc`.
7.  Click **Next**.

![Create Target Group Step 1](/images/5-Workshop/5.3-Network/alb-target-group-1.png)

8.  **Register targets:** Since we haven't deployed the ECS tasks yet, skip this step and click **Create target group**.

![Target Group Created](/images/5-Workshop/5.3-Network/alb-target-group-result.png)

### 3. Create Application Load Balancer

Now, we aggregate everything into the Load Balancer.

**Step 1: Basic Configuration**

1.  Go to **Load Balancers** > **Create load balancer**.
2.  Select **Application Load Balancer** and click **Create**.
3.  **Load balancer name:** `bandup-public-alb`.
4.  **Scheme:** `Internet-facing` (To allow public access).
5.  **IP address type:** `IPv4`.

![Select ALB Type](/images/5-Workshop/5.3-Network/create-alb.png)
![Configure ALB Basics](/images/5-Workshop/5.3-Network/create-alb-1.png)

**Step 2: Network Mapping**

1.  **VPC:** Select `band-up-vpc`.
2.  **Mappings:** Select **two Availability Zones** (`ap-southeast-1a` and `ap-southeast-1b`).
3.  **Subnets:** IMPORTANT - Select the **Public Subnets** (`public-subnet-1` and `public-subnet-2`) created in the previous section.
    -   _Note: Ensure you do not select Private subnets, otherwise the ALB cannot be reached from the internet._

![Network Mapping](/images/5-Workshop/5.3-Network/create-alb-2.png)

**Step 3: Security Groups & Listeners**

1.  **Security groups:** Deselect the default one and choose `alb-sg`.
2.  **Listeners and routing:**
    -   **Protocol:** `HTTP` | **Port:** `80`.
    -   **Default action:** Forward to `target-bandup-fe`.
3.  Click **Create load balancer**.

![Configure Listener](/images/5-Workshop/5.3-Network/create-alb-3.png)

Your ALB is now provisioning. Once active, it will be ready to route traffic to your frontend application.
