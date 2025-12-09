---
title: "ElastiCache Redis"
date:
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

#### Tổng Quan

Tạo Amazon ElastiCache Redis cho session management và caching.

#### Tạo Cache Subnet Group

1. Điều hướng đến **ElastiCache** → **Subnet groups** → **Create subnet group**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Name** | `ielts-prod-cache-subnet` |
| **VPC** | ielts-prod-vpc |
| **Subnets** | Private DB subnets |

#### Tạo Redis Cluster

| Cài Đặt | Giá Trị |
|---------|-------|
| **Cluster engine** | Redis |
| **Cluster mode** | Disabled |
| **Name** | `ielts-prod-redis` |
| **Node type** | cache.t3.micro |
| **Number of replicas** | 0 |
| **Subnet group** | ielts-prod-cache-subnet |
| **Security group** | ielts-prod-redis-sg |
| **Encryption at-rest** | Enabled |
| **Encryption in-transit** | Enabled |


#### AWS CLI Command

```bash
aws elasticache create-cache-cluster \
    --cache-cluster-id ielts-prod-redis \
    --cache-node-type cache.t3.micro \
    --engine redis \
    --num-cache-nodes 1 \
    --cache-subnet-group-name ielts-prod-cache-subnet \
    --security-group-ids $REDIS_SG
```

#### Lấy Endpoint

```bash
aws elasticache describe-cache-clusters \
    --cache-cluster-id ielts-prod-redis \
    --show-cache-node-info \
    --query 'CacheClusters[0].CacheNodes[0].Endpoint.Address'
```

#### Bước Tiếp Theo

Tiến hành đến [S3 & CloudFront](../5.6.3-S3-CloudFront/).

