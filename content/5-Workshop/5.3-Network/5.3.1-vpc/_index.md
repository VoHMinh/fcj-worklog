---
title: "VPC, Subnets & Routing"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

In this step, we establish the isolated network environment for IELTS BandUp. We will create a Virtual Private Cloud (VPC), partition it into subnets across multiple Availability Zones, and configure routing for internet access.

### 1. Create the VPC

First, we need a private network space.

1.  Navigate to the **VPC Dashboard**.
2.  Click **Create VPC**.
3.  Choose **VPC only**.
4.  **Name tag:** `band-up-vpc`
5.  **IPv4 CIDR block:** `10.0.0.0/16` (This provides 65,536 IP addresses, sufficient for future scaling).
6.  Leave other settings as default and click **Create VPC**.

![VPC Settings](/images/5-Workshop/5.3-Network/create-vpc.png)
![VPC Created Successfully](/images/5-Workshop/5.3-Network/create-vpc-success.png)

### 2. Create Subnets

Next, we divide the VPC into smaller networks (Subnets) distributed across two Availability Zones (AZs) for High Availability. We will follow this IP schema:

| Subnet Name                 | Type     | CIDR Block    | Availability Zone |
| :-------------------------- | :------- | :------------ | :---------------- |
| `public-subnet-1`           | Public   | `10.0.0.0/24` | `ap-southeast-1a` |
| `public-subnet-2`           | Public   | `10.0.1.0/24` | `ap-southeast-1b` |
| `private-app-subnet-1`      | Private  | `10.0.2.0/24` | `ap-southeast-1a` |
| `private-app-subnet-2`      | Private  | `10.0.3.0/24` | `ap-southeast-1b` |
| `private-database-subnet-1` | Database | `10.0.4.0/24` | `ap-southeast-1a` |
| `private-database-subnet-2` | Database | `10.0.5.0/24` | `ap-southeast-1b` |

**Steps:**

1.  Go to **Subnets** > **Create subnet**.
2.  Select the **VPC ID**: `band-up-vpc`.
3.  Enter the **Subnet name**, **Availability Zone**, and **IPv4 CIDR block** for each subnet according to the table above.
4.  Repeat the process until all 6 subnets are created.

![Create Public Subnet](/images/5-Workshop/5.3-Network/create-subnet.png)
![Create Database Subnet](/images/5-Workshop/5.3-Network/create-subnet-end.png)

### 3. Create Internet Gateway (IGW)

By default, a VPC is closed to the internet. To allow resources in our Public Subnets to communicate with the outside world, we need an Internet Gateway.

1.  Go to **Internet gateways** > **Create internet gateway**.
2.  **Name tag:** `band-up-igw`.
3.  Click **Create internet gateway**.
4.  After creation, click **Actions** > **Attach to VPC**.
5.  Select `band-up-vpc` and click **Attach internet gateway**.

![Attach IGW](/images/5-Workshop/5.3-Network/create-igw-success.png)

### 4. Configure Route Tables

Finally, we need to direct traffic from our Public Subnets to the Internet Gateway.

1.  Go to **Route tables** > **Create route table**.
2.  **Name:** `public-route-table`.
3.  **VPC:** `band-up-vpc`.
4.  Click **Create route table**.

**Add Route to Internet:**

1.  Select the newly created `public-route-table`.
2.  Go to the **Routes** tab > **Edit routes**.
3.  Add a new route:
    -   **Destination:** `0.0.0.0/0` (All traffic).
    -   **Target:** Select `Internet Gateway` -> `band-up-igw`.
4.  Click **Save changes**.

![Edit Routes](/images/5-Workshop/5.3-Network/create-route-table.png)

**Associate Subnets:**

1.  Go to the **Subnet associations** tab > **Edit subnet associations**.
2.  Select only the **Public Subnets** (`public-subnet-1` and `public-subnet-2`).
3.  Click **Save associations**.

![Subnet Association](/images/5-Workshop/5.3-Network/edit-route-tabe.png)

### 5. Verify Configuration

To verify that the network architecture is correctly established, navigate back to your **VPC Dashboard**, select `band-up-vpc`, and view the **Resource map** tab. You should see a clear structure linking your Public Subnets to the Route Table and the Internet Gateway.

![VPC Resource Map Result](/images/5-Workshop/5.3-Network/vpc-result.png)
