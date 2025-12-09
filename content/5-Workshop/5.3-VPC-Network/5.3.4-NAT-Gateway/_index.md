---
title: "NAT Gateway"
date:
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

#### Overview

NAT (Network Address Translation) Gateways allow resources in private subnets to access the internet while preventing inbound connections from the internet. This is essential for ECS tasks to pull container images, access external APIs, and download updates.

#### NAT Gateway Architecture

In our Multi-AZ design, we deploy one NAT Gateway per Availability Zone:

| NAT Gateway | Subnet | Purpose |
|-------------|--------|---------|
| `ielts-prod-nat-1a` | Public Subnet 1a | Outbound for AZ-1 private subnets |
| `ielts-prod-nat-1b` | Public Subnet 1b | Outbound for AZ-2 private subnets |



#### Why Two NAT Gateways?

**High Availability:**
- If AZ-1 fails, resources in AZ-2 can still access the internet
- No cross-AZ traffic for outbound requests (reduces latency and cost)

**Cost Consideration:**
- Each NAT Gateway costs ~$0.045/hour + data processing fees
- For development, you can use a single NAT Gateway to reduce costs
- For production, always use one per AZ for high availability

#### Create NAT Gateway (If Not Created by VPC Wizard)

**Step 1: Allocate Elastic IP**

1. Navigate to **VPC** → **Elastic IPs**
2. Click **Allocate Elastic IP address**
3. Keep default settings and click **Allocate**
4. Note the Allocation ID

Repeat for the second NAT Gateway.

**Step 2: Create NAT Gateway in AZ-1**

1. Navigate to **VPC** → **NAT Gateways**
2. Click **Create NAT gateway**

| Setting | Value |
|---------|-------|
| **Name** | `ielts-prod-nat-1a` |
| **Subnet** | ielts-prod-public-1a |
| **Connectivity type** | Public |
| **Elastic IP allocation ID** | Select the allocated EIP |

3. Click **Create NAT gateway**

**Step 3: Create NAT Gateway in AZ-2**

Repeat the process for AZ-2:

| Setting | Value |
|---------|-------|
| **Name** | `ielts-prod-nat-1b` |
| **Subnet** | ielts-prod-public-1b |
| **Connectivity type** | Public |
| **Elastic IP allocation ID** | Select the second allocated EIP |



#### Update Private Route Tables

After NAT Gateways are created, update the private route tables:

**Private Route Table AZ-1:**

1. Navigate to **VPC** → **Route Tables**
2. Select `ielts-prod-private-rt-1a`
3. Go to **Routes** tab → **Edit routes**
4. Add route:
   - **Destination**: `0.0.0.0/0`
   - **Target**: NAT Gateway `ielts-prod-nat-1a`
5. Click **Save changes**

**Private Route Table AZ-2:**

Repeat for AZ-2 with `ielts-prod-nat-1b`.



#### AWS CLI Commands

```bash
# Allocate Elastic IPs
EIP1=$(aws ec2 allocate-address --query 'AllocationId' --output text)
EIP2=$(aws ec2 allocate-address --query 'AllocationId' --output text)

# Get public subnet IDs
SUBNET_1A=$(aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=ielts-prod-public-1a" \
    --query 'Subnets[0].SubnetId' --output text)

SUBNET_1B=$(aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=ielts-prod-public-1b" \
    --query 'Subnets[0].SubnetId' --output text)

# Create NAT Gateway in AZ-1
NAT1=$(aws ec2 create-nat-gateway \
    --subnet-id $SUBNET_1A \
    --allocation-id $EIP1 \
    --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=ielts-prod-nat-1a}]' \
    --query 'NatGateway.NatGatewayId' --output text)

# Create NAT Gateway in AZ-2
NAT2=$(aws ec2 create-nat-gateway \
    --subnet-id $SUBNET_1B \
    --allocation-id $EIP2 \
    --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=ielts-prod-nat-1b}]' \
    --query 'NatGateway.NatGatewayId' --output text)

# Wait for NAT Gateways to become available
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT1 $NAT2

# Update private route tables
# Get route table IDs and add routes
```

#### Verify NAT Gateway Configuration

**Step 1: Check NAT Gateway Status**

1. Navigate to **VPC** → **NAT Gateways**
2. Verify both NAT Gateways show **Available** status
3. Note the Elastic IP addresses assigned

**Step 2: Verify Route Tables**

1. Navigate to **VPC** → **Route Tables**
2. Select each private route table
3. Verify the `0.0.0.0/0` route points to the correct NAT Gateway

**Step 3: Test Connectivity (After ECS Setup)**

From an ECS task in a private subnet:
```bash
# Test internet connectivity
curl -I https://www.google.com

# Test AWS API access
aws sts get-caller-identity
```

#### NAT Gateway vs NAT Instance

| Feature | NAT Gateway | NAT Instance |
|---------|-------------|--------------|
| **Availability** | Highly available within AZ | Single point of failure |
| **Bandwidth** | Up to 100 Gbps | Depends on instance type |
| **Maintenance** | Managed by AWS | User managed |
| **Cost** | ~$32/month + data | Instance cost + data |
| **Security Groups** | Not supported | Supported |
| **Recommendation** | Production workloads | Dev/test only |

{{% notice tip %}}
For production, always use NAT Gateways. For development environments with tight budgets, consider NAT Instances or a single NAT Gateway.
{{% /notice %}}

#### Cost Optimization

**Development Environment:**
- Use a single NAT Gateway in one AZ
- Schedule NAT Gateway deletion during off-hours (use automation)
- Consider VPC endpoints for AWS services to reduce data transfer

**Production Environment:**
- One NAT Gateway per AZ is mandatory for HA
- Use VPC endpoints for frequently accessed AWS services
- Monitor data transfer costs in CloudWatch

#### VPC Endpoints (Optional Cost Optimization)

Create VPC endpoints to avoid NAT Gateway data charges for AWS services:

```bash
# S3 Gateway Endpoint (free)
aws ec2 create-vpc-endpoint \
    --vpc-id $VPC_ID \
    --service-name com.amazonaws.ap-southeast-1.s3 \
    --route-table-ids $PRIVATE_RT_1A $PRIVATE_RT_1B

# ECR API Interface Endpoint
aws ec2 create-vpc-endpoint \
    --vpc-id $VPC_ID \
    --service-name com.amazonaws.ap-southeast-1.ecr.api \
    --vpc-endpoint-type Interface \
    --subnet-ids $PRIVATE_APP_1A $PRIVATE_APP_1B \
    --security-group-ids $VPC_ENDPOINT_SG

# ECR DKR Interface Endpoint
aws ec2 create-vpc-endpoint \
    --vpc-id $VPC_ID \
    --service-name com.amazonaws.ap-southeast-1.ecr.dkr \
    --vpc-endpoint-type Interface \
    --subnet-ids $PRIVATE_APP_1A $PRIVATE_APP_1B \
    --security-group-ids $VPC_ENDPOINT_SG
```

#### Network Configuration Summary

After completing this section, your VPC is fully configured:

| Component | Count | Status |
|-----------|-------|--------|
| VPC | 1 | ✅ Created |
| Internet Gateway | 1 | ✅ Attached |
| NAT Gateways | 2 | ✅ Available |
| Public Subnets | 2 | ✅ Configured |
| Private App Subnets | 2 | ✅ Configured |
| Private DB Subnets | 2 | ✅ Configured |
| Security Groups | 5 | ✅ Created |
| Route Tables | 3+ | ✅ Configured |

#### Next Steps

The network infrastructure is now complete. Proceed to [ECS & Container Setup](../../5.4-ECS-Setup/) to deploy the application containers.

