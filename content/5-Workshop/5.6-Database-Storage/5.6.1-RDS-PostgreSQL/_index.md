---
title: "RDS PostgreSQL"
date:
weight: 1
chapter: false
pre: " <b> 5.6.1. </b> "
---

#### Overview

Create Amazon RDS PostgreSQL with Multi-AZ deployment for high availability.

#### Create DB Subnet Group

1. Navigate to **RDS** → **Subnet groups** → **Create DB subnet group**

| Setting | Value |
|---------|-------|
| **Name** | `ielts-prod-db-subnet-group` |
| **VPC** | ielts-prod-vpc |
| **Subnets** | Private DB subnets (1a, 1b) |

#### Create RDS Instance

1. Navigate to **RDS** → **Create database**

| Setting | Value |
|---------|-------|
| **Engine** | PostgreSQL 14 |
| **Template** | Production |
| **DB instance identifier** | `ielts-prod-db` |
| **Master username** | `ielts_admin` |
| **Master password** | (Store in Secrets Manager) |
| **Instance class** | db.t3.medium |
| **Storage** | 100 GB gp3 |
| **Multi-AZ** | Yes |
| **VPC** | ielts-prod-vpc |
| **Subnet group** | ielts-prod-db-subnet-group |
| **Security group** | ielts-prod-rds-sg |
| **Public access** | No |
| **Database name** | `ielts` |
| **Backup retention** | 7 days |
| **Encryption** | Enabled |


#### AWS CLI Command

```bash
aws rds create-db-instance \
    --db-instance-identifier ielts-prod-db \
    --db-instance-class db.t3.medium \
    --engine postgres \
    --engine-version 14 \
    --master-username ielts_admin \
    --master-user-password $DB_PASSWORD \
    --allocated-storage 100 \
    --storage-type gp3 \
    --multi-az \
    --db-name ielts \
    --vpc-security-group-ids $RDS_SG \
    --db-subnet-group-name ielts-prod-db-subnet-group \
    --backup-retention-period 7 \
    --storage-encrypted \
    --no-publicly-accessible
```

#### Get Connection Endpoint

```bash
aws rds describe-db-instances \
    --db-instance-identifier ielts-prod-db \
    --query 'DBInstances[0].Endpoint.Address' --output text
```

#### Next Steps

Proceed to [ElastiCache Redis](../5.6.2-ElastiCache-Redis/).

