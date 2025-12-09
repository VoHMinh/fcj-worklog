---
title: "Database & Storage Setup"
date:
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Overview

This section covers setting up the data layer including Amazon RDS PostgreSQL (Multi-AZ), Amazon ElastiCache (Redis), and Amazon S3 with CloudFront CDN.

#### Data Architecture

| Service | Purpose | Configuration |
|---------|---------|---------------|
| **RDS PostgreSQL** | Primary database | Multi-AZ, db.t3.medium |
| **ElastiCache Redis** | Session/cache | cache.t3.micro |
| **S3** | Media storage | Standard + Lifecycle |
| **CloudFront** | CDN | Global edge locations |

#### Content

1. [RDS PostgreSQL](5.6.1-RDS-PostgreSQL/)
2. [ElastiCache Redis](5.6.2-ElastiCache-Redis/)
3. [S3 & CloudFront](5.6.3-S3-CloudFront/)

#### Estimated Time: ~45 minutes

