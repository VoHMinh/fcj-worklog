---
title: "VPC & Network Setup"
date:
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Overview

In this section, you will create the foundational network infrastructure for the IELTS Self-Learning Web System. The network architecture spans two Availability Zones (AZ-1 and AZ-2) to ensure high availability and fault tolerance.

#### Network Architecture

The VPC design follows AWS best practices for a multi-tier application:

![Network Architecture](/images/2-Proposal/AWS-Bandup-Architecture.png)

**VPC Configuration:**
- **CIDR Block**: `10.0.0.0/16` (65,536 IP addresses)
- **Region**: ap-southeast-1 (Singapore)
- **Availability Zones**: 2 (AZ-1 and AZ-2)

#### Subnet Design

| Subnet Type | AZ-1 CIDR | AZ-2 CIDR | Purpose |
|-------------|-----------|-----------|---------|
| **Public Subnet** | 10.0.1.0/24 | 10.0.2.0/24 | ALB, NAT Gateway, Bastion |
| **Private Subnet (App)** | 10.0.11.0/24 | 10.0.12.0/24 | ECS Fargate Tasks (FE & BE) |
| **Private Subnet (DB)** | 10.0.21.0/24 | 10.0.22.0/24 | RDS, ElastiCache |

#### Network Components

**1. Internet Gateway**
- Enables internet access for resources in public subnets
- Required for ALB to receive external traffic

**2. NAT Gateways**
- Deployed in public subnets (one per AZ)
- Allows private subnet resources to access the internet
- Used by ECS tasks for pulling container images, accessing external APIs

**3. Route Tables**
- **Public Route Table**: Routes `0.0.0.0/0` to Internet Gateway
- **Private Route Tables**: Routes `0.0.0.0/0` to NAT Gateway in respective AZ

**4. Security Groups**
- Defense-in-depth with multiple layers
- Least privilege access principle
- Separate security groups for ALB, ECS, RDS, ElastiCache

**5. Network ACLs**
- Subnet-level firewall rules
- Additional layer of security beyond security groups

#### What You Will Create

In this section, you will:

1. Create a VPC with DNS support enabled
2. Create public and private subnets across two AZs
3. Set up an Internet Gateway
4. Deploy NAT Gateways for private subnet internet access
5. Configure route tables for proper traffic flow
6. Create security groups for each tier
7. (Optional) Set up VPC Flow Logs for network monitoring

#### Estimated Time

| Task | Time |
|------|------|
| Create VPC | 5 minutes |
| Configure Subnets | 10 minutes |
| Set up Security Groups | 10 minutes |
| Configure NAT Gateway | 5 minutes |
| **Total** | **~30 minutes** |

#### Content

1. [Create VPC](5.3.1-Create-VPC/)
2. [Subnets Configuration](5.3.2-Subnets-Configuration/)
3. [Security Groups](5.3.3-Security-Groups/)
4. [NAT Gateway](5.3.4-NAT-Gateway/)

#### Best Practices

{{% notice tip %}}
**Network Design Best Practices:**
- Always use multiple AZs for production workloads
- Keep database subnets isolated with no direct internet access
- Use separate security groups for each tier (web, app, data)
- Enable VPC Flow Logs for security analysis and troubleshooting
- Use descriptive naming conventions for all resources
{{% /notice %}}

