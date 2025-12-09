---
title: "Database & Storage Setup"
date:
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Tổng Quan

Phần này bao gồm thiết lập data layer bao gồm Amazon RDS PostgreSQL (Multi-AZ), Amazon ElastiCache (Redis), và Amazon S3 với CloudFront CDN.

#### Kiến Trúc Dữ Liệu

| Dịch Vụ | Mục Đích | Cấu Hình |
|---------|---------|---------------|
| **RDS PostgreSQL** | Primary database | Multi-AZ, db.t3.medium |
| **ElastiCache Redis** | Session/cache | cache.t3.micro |
| **S3** | Media storage | Standard + Lifecycle |
| **CloudFront** | CDN | Global edge locations |

#### Nội Dung

1. [RDS PostgreSQL](5.6.1-RDS-PostgreSQL/)
2. [ElastiCache Redis](5.6.2-ElastiCache-Redis/)
3. [S3 & CloudFront](5.6.3-S3-CloudFront/)

#### Thời Gian Ước Tính: ~45 phút

