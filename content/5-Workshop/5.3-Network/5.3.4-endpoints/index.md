---
title: "VPC Endpoints Setup"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

To ensure security, our backend services running in Private Subnets should not access key AWS services over the public internet. Instead, we use **AWS PrivateLink** (VPC Endpoints) to keep this traffic within the AWS network.

We will create **4 Endpoints**:

-   **Interface Endpoints:** For ECR (Docker & API) and CloudWatch Logs.
-   **Gateway Endpoint:** For Amazon S3.

### 1. Create Interface Endpoints (ECR & CloudWatch)

We will start by creating the endpoint for **ECR Docker** (`ecr.dkr`). The process is identical for **ECR API** (`ecr.api`) and **CloudWatch** (`logs`).

**Step 1: Service Selection**

1.  Navigate to **VPC Dashboard** > **Endpoints** > **Create endpoint**.
2.  **Name tag:** `ecr-endpoint` (for Docker).
3.  **Service category:** Select **AWS services**.
4.  **Services:** Search for `ecr.dkr` and select `com.amazonaws.ap-southeast-1.ecr.dkr`.

![Select ECR Service](/images/5-Workshop/5.3-Network/ecr-endpoint1.png)

**Step 2: VPC & Subnets**

1.  **VPC:** Select `band-up-vpc`.
2.  **Subnets:** Select the Availability Zones and choose the **Private Subnets** (`private-app-subnet-1` and `private-app-subnet-2`).
    -   _Note: This creates Elastic Network Interfaces (ENIs) in your private subnets to serve as entry points._

![Select Subnets](/images/5-Workshop/5.3-Network/ecr-endpoint2.png)

**Step 3: Security Group**

1.  **Security groups:** Select the Security Group that allows HTTPS (Port 443) traffic from your VPC.
    -   _For this workshop, you can use the default security group if it allows inbound traffic from within the VPC._

![Select Security Group](/images/5-Workshop/5.3-Network/ecr-endpoint-3.png)

2.  Click **Create endpoint**.

![Endpoint Created](/images/5-Workshop/5.3-Network/ecr-endpoint-success.png)

**Step 4: Repeat for ECR API and CloudWatch**
Repeat the steps above to create two more Interface Endpoints:

-   **ECR API:** Search for `ecr.api` -> Name: `ecr-api-endpoint`.
-   **CloudWatch Logs:** Search for `logs` -> Name: `cloudwatch-endpoint`.

### 2. Create Gateway Endpoint (S3)

For Amazon S3, we use a **Gateway Endpoint**, which is cost-effective and uses routing tables instead of network interfaces.

1.  Click **Create endpoint**.
2.  **Name tag:** `s3-endpoint`.
3.  **Services:** Search for `s3` and select `com.amazonaws.ap-southeast-1.s3` (Type: **Gateway**).
4.  **VPC:** Select `band-up-vpc`.
5.  **Route tables:** Select the Route Tables associated with your **Private Subnets**.
6.  Click **Create endpoint**.

### 3. Verify All Endpoints

Once completed, navigate to the **Endpoints** list. You should see 4 active endpoints ensuring secure connectivity for your infrastructure.

-   `ecr-endpoint` (Interface)
-   `ecr-api-endpoint` (Interface)
-   `cloudwatch-endpoint` (Interface)
-   `s3-endpoint` (Gateway)

![List of Created Endpoints](/images/5-Workshop/5.3-Network/more-endpoint.png)
