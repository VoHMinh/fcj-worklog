---
title: "Load Balancer Configuration"
date:
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Overview

The Application Load Balancer (ALB) distributes incoming traffic across ECS tasks and provides SSL termination. This section covers ALB configuration, target groups, Route 53 DNS, and ACM certificate setup.

#### ALB Architecture

```
Internet → Route 53 → ALB (HTTPS:443) → Target Groups → ECS Tasks
                              │
                              ├── /api/* → Backend Target Group (8080)
                              └── /* → Frontend Target Group (3000)
```

#### Content

1. [Target Groups](5.5.1-Target-Groups/)
2. [ALB Configuration](5.5.2-ALB-Configuration/)
3. [Route 53 & ACM](5.5.3-Route53-ACM/)

#### What You Will Create

- Application Load Balancer in public subnets
- Target groups for Frontend and Backend
- Listener rules for path-based routing
- SSL certificate via ACM
- Route 53 DNS records

#### Estimated Time: ~30 minutes

