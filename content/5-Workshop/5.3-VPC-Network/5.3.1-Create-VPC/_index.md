---
title: "Create VPC"
date:
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Overview

In this step, you will create the Virtual Private Cloud (VPC) that serves as the isolated network environment for all IELTS Self-Learning Web System resources.

#### Create VPC

**Step 1: Navigate to VPC Console**

1. Sign in to the AWS Management Console
2. Navigate to **VPC** service
3. Click **Create VPC**

**Step 2: Configure VPC Settings**

Select **VPC and more** to create VPC with all required components automatically:

| Setting | Value |
|---------|-------|
| **Name tag auto-generation** | `ielts-prod` |
| **IPv4 CIDR block** | `10.0.0.0/16` |
| **IPv6 CIDR block** | No IPv6 CIDR block |
| **Tenancy** | Default |
| **Number of Availability Zones** | 2 |
| **Number of public subnets** | 2 |
| **Number of private subnets** | 4 |
| **NAT gateways** | 1 per AZ |
| **VPC endpoints** | None (we'll add later) |
| **DNS hostnames** | Enable |
| **DNS resolution** | Enable |



**Step 3: Review and Create**

1. Review the **Preview** panel showing the VPC topology
2. Verify all settings are correct
3. Click **Create VPC**

The creation process will automatically create:
- 1 VPC
- 2 Public subnets
- 4 Private subnets (2 for app tier, 2 for database tier)
- 1 Internet Gateway
- 2 NAT Gateways (one per AZ)
- Route tables for each subnet tier



#### Alternative: Create VPC Using AWS CLI

If you prefer using CLI:

```bash
# Create VPC
aws ec2 create-vpc \
    --cidr-block 10.0.0.0/16 \
    --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=ielts-prod-vpc}]' \
    --query 'Vpc.VpcId' \
    --output text

# Enable DNS hostnames
aws ec2 modify-vpc-attribute \
    --vpc-id <vpc-id> \
    --enable-dns-hostnames '{"Value": true}'

# Enable DNS resolution
aws ec2 modify-vpc-attribute \
    --vpc-id <vpc-id> \
    --enable-dns-support '{"Value": true}'
```

#### Verify VPC Creation

After creation, verify the following in the VPC console:

1. **VPC Details**
   - CIDR: `10.0.0.0/16`
   - DNS hostnames: Enabled
   - DNS resolution: Enabled

2. **Resource Map**
   - Shows all created subnets
   - Displays Internet Gateway attachment
   - Shows NAT Gateway placements



#### Naming Convention

We use the following naming convention for all VPC resources:

```
{project}-{environment}-{resource-type}-{az}
```

Examples:
- `ielts-prod-vpc`
- `ielts-prod-public-subnet-1a`
- `ielts-prod-private-app-subnet-1a`
- `ielts-prod-private-db-subnet-1a`
- `ielts-prod-igw`
- `ielts-prod-nat-1a`

#### VPC Configuration Summary

| Resource | Count | Details |
|----------|-------|---------|
| VPC | 1 | CIDR: 10.0.0.0/16 |
| Internet Gateway | 1 | Attached to VPC |
| NAT Gateway | 2 | One per AZ in public subnet |
| Public Subnets | 2 | For ALB, NAT GW |
| Private App Subnets | 2 | For ECS tasks |
| Private DB Subnets | 2 | For RDS, ElastiCache |
| Route Tables | 3+ | Public, Private per AZ |

#### Best Practices Applied

{{% notice tip %}}
- **DNS Support**: Enabled for service discovery and Route 53 integration
- **Multiple AZs**: Resources spread across 2 AZs for high availability
- **CIDR Planning**: Large enough CIDR block for future growth
- **Naming Convention**: Consistent naming for easy management
{{% /notice %}}

#### Troubleshooting

**Issue: VPC creation fails**
- Check IAM permissions for VPC creation
- Verify you haven't reached VPC quota limits (default: 5 VPCs per region)

**Issue: DNS not resolving**
- Ensure DNS hostnames is enabled
- Ensure DNS resolution is enabled

#### Next Steps

Proceed to [Subnets Configuration](../5.3.2-Subnets-Configuration/) to review and configure the subnet settings.

