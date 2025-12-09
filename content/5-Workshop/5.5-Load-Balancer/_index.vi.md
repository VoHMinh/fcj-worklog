---
title: "Load Balancer Configuration"
date:
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Tổng Quan

Application Load Balancer (ALB) phân phối incoming traffic qua ECS tasks và cung cấp SSL termination. Phần này bao gồm ALB configuration, target groups, Route 53 DNS, và ACM certificate setup.

#### Kiến Trúc ALB

```
Internet → Route 53 → ALB (HTTPS:443) → Target Groups → ECS Tasks
                              │
                              ├── /api/* → Backend Target Group (8080)
                              └── /* → Frontend Target Group (3000)
```

#### Nội Dung

1. [Target Groups](5.5.1-Target-Groups/)
2. [ALB Configuration](5.5.2-ALB-Configuration/)
3. [Route 53 & ACM](5.5.3-Route53-ACM/)

#### Những Gì Bạn Sẽ Tạo

- Application Load Balancer trong public subnets
- Target groups cho Frontend và Backend
- Listener rules cho path-based routing
- SSL certificate qua ACM
- Route 53 DNS records

#### Thời Gian Ước Tính: ~30 phút

