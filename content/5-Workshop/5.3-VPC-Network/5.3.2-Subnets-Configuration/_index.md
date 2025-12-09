---
title: "Subnets Configuration"
date:
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Overview

In this step, you will review and configure the subnets created by the VPC wizard. The subnet design follows a three-tier architecture pattern with proper isolation between layers.

#### Subnet Architecture

The VPC contains six subnets across two Availability Zones:

| Subnet Name | CIDR | AZ | Type | Purpose |
|-------------|------|-----|------|---------|
| `ielts-prod-public-1a` | 10.0.1.0/24 | ap-southeast-1a | Public | ALB, NAT Gateway |
| `ielts-prod-public-1b` | 10.0.2.0/24 | ap-southeast-1b | Public | ALB (standby) |
| `ielts-prod-private-app-1a` | 10.0.11.0/24 | ap-southeast-1a | Private | ECS Frontend & Backend (Active) |
| `ielts-prod-private-app-1b` | 10.0.12.0/24 | ap-southeast-1b | Private | ECS Frontend & Backend (Standby) |
| `ielts-prod-private-db-1a` | 10.0.21.0/24 | ap-southeast-1a | Private | RDS Primary, ElastiCache |
| `ielts-prod-private-db-1b` | 10.0.22.0/24 | ap-southeast-1b | Private | RDS Standby |



#### Configure Public Subnets

Public subnets must have **Auto-assign public IPv4 address** enabled:

**Step 1: Select Public Subnet**
1. Navigate to **VPC** → **Subnets**
2. Select `ielts-prod-public-1a`
3. Click **Actions** → **Edit subnet settings**

**Step 2: Enable Auto-assign Public IP**
1. Check **Enable auto-assign public IPv4 address**
2. Click **Save**

**Step 3: Repeat for Second Public Subnet**
1. Repeat steps for `ielts-prod-public-1b`



#### Configure Private Subnets

Private subnets should NOT have public IP auto-assignment:

1. Verify `ielts-prod-private-app-1a` has auto-assign disabled
2. Verify `ielts-prod-private-app-1b` has auto-assign disabled
3. Verify `ielts-prod-private-db-1a` has auto-assign disabled
4. Verify `ielts-prod-private-db-1b` has auto-assign disabled

#### Create Route Tables

If not created by wizard, create route tables manually:

**Public Route Table:**

```bash
# Create public route table
aws ec2 create-route-table \
    --vpc-id <vpc-id> \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=ielts-prod-public-rt}]'

# Add route to Internet Gateway
aws ec2 create-route \
    --route-table-id <rtb-id> \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id <igw-id>

# Associate with public subnets
aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <public-subnet-1a-id>

aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <public-subnet-1b-id>
```

**Private Route Tables (per AZ):**

```bash
# Create private route table for AZ-1
aws ec2 create-route-table \
    --vpc-id <vpc-id> \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=ielts-prod-private-rt-1a}]'

# Add route to NAT Gateway in AZ-1
aws ec2 create-route \
    --route-table-id <rtb-id> \
    --destination-cidr-block 0.0.0.0/0 \
    --nat-gateway-id <nat-1a-id>

# Associate with private subnets in AZ-1
aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <private-app-1a-id>

aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <private-db-1a-id>
```

#### Verify Route Table Configuration

**Public Route Table should have:**

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | igw-xxx |

**Private Route Table (AZ-1) should have:**

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | nat-xxx (in AZ-1) |

**Private Route Table (AZ-2) should have:**

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | nat-xxx (in AZ-2) |



#### Subnet Tagging for ECS and ALB

Add required tags for ECS and ALB subnet discovery:

**For Public Subnets (ALB):**
```bash
aws ec2 create-tags \
    --resources <public-subnet-1a-id> <public-subnet-1b-id> \
    --tags Key=kubernetes.io/role/elb,Value=1
```

**For Private Subnets (ECS):**
```bash
aws ec2 create-tags \
    --resources <private-app-1a-id> <private-app-1b-id> \
    --tags Key=kubernetes.io/role/internal-elb,Value=1
```

#### IP Address Capacity

Each /24 subnet provides:
- 256 total IP addresses
- 251 usable IP addresses (AWS reserves 5 IPs per subnet)

| Subnet | Usable IPs | Reserved for |
|--------|------------|--------------|
| Public 1a | 251 | NAT GW, ALB, Bastion |
| Public 1b | 251 | ALB, Bastion |
| Private App 1a | 251 | ECS Tasks (Active) |
| Private App 1b | 251 | ECS Tasks (Standby) |
| Private DB 1a | 251 | RDS Primary, ElastiCache |
| Private DB 1b | 251 | RDS Standby |

{{% notice tip %}}
With 251 usable IPs per app subnet, you can run approximately 100+ ECS tasks per AZ (accounting for ENI attachments and reserved IPs).
{{% /notice %}}

#### Network ACLs (Optional)

For additional security, configure Network ACLs:

**Public Subnet NACL:**
- Allow inbound HTTP/HTTPS (80, 443)
- Allow inbound ephemeral ports (1024-65535)
- Allow all outbound

**Private Subnet NACL:**
- Allow inbound from VPC CIDR
- Allow inbound ephemeral ports
- Allow outbound to VPC and internet (via NAT)

{{% notice warning %}}
Network ACLs are stateless. Ensure you configure both inbound and outbound rules for return traffic.
{{% /notice %}}

#### Subnet Configuration Summary

| Configuration | Public | Private App | Private DB |
|---------------|--------|-------------|------------|
| Auto-assign public IP | Yes | No | No |
| Internet access | Direct (IGW) | Via NAT | Via NAT |
| Route to internet | igw-xxx | nat-xxx | nat-xxx |
| Purpose | ALB, NAT | ECS Tasks | RDS, Cache |

#### Next Steps

Proceed to [Security Groups](../5.3.3-Security-Groups/) to configure firewall rules for each tier.

