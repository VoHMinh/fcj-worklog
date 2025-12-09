---
title: "ALB Configuration"
date:
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

#### Tổng Quan

Tạo và cấu hình Application Load Balancer với listener rules cho path-based routing.

#### Tạo Application Load Balancer

1. Điều hướng đến **EC2** → **Load Balancers** → **Create Load Balancer**
2. Chọn **Application Load Balancer**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Name** | `ielts-prod-alb` |
| **Scheme** | Internet-facing |
| **IP address type** | IPv4 |
| **VPC** | ielts-prod-vpc |
| **Subnets** | Public subnets (1a, 1b) |
| **Security group** | ielts-prod-alb-sg |


#### Cấu Hình Listeners

**HTTPS Listener (Port 443):**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Protocol** | HTTPS |
| **Port** | 443 |
| **Default action** | Forward to ielts-frontend-tg |
| **SSL Certificate** | Select ACM certificate |
| **Security policy** | ELBSecurityPolicy-TLS13-1-2-2021-06 |

**HTTP Listener (Port 80) - Redirect:**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Protocol** | HTTP |
| **Port** | 80 |
| **Default action** | Redirect to HTTPS (301) |

#### Cấu Hình Listener Rules

Thêm rule để route API requests đến backend:

| Priority | Condition | Action |
|----------|-----------|--------|
| 1 | Path pattern: `/api/*` | Forward to ielts-backend-tg |
| Default | - | Forward to ielts-frontend-tg |

#### AWS CLI Commands

```bash
# Tạo ALB
ALB_ARN=$(aws elbv2 create-load-balancer \
    --name ielts-prod-alb \
    --subnets $PUBLIC_SUBNET_1A $PUBLIC_SUBNET_1B \
    --security-groups $ALB_SG \
    --scheme internet-facing \
    --type application \
    --query 'LoadBalancers[0].LoadBalancerArn' --output text)

# Tạo HTTPS listener
aws elbv2 create-listener \
    --load-balancer-arn $ALB_ARN \
    --protocol HTTPS \
    --port 443 \
    --ssl-policy ELBSecurityPolicy-TLS13-1-2-2021-06 \
    --certificates CertificateArn=$ACM_CERT_ARN \
    --default-actions Type=forward,TargetGroupArn=$FRONTEND_TG_ARN

# Tạo HTTP to HTTPS redirect
aws elbv2 create-listener \
    --load-balancer-arn $ALB_ARN \
    --protocol HTTP \
    --port 80 \
    --default-actions Type=redirect,RedirectConfig='{Protocol=HTTPS,Port=443,StatusCode=HTTP_301}'

# Thêm API routing rule
aws elbv2 create-rule \
    --listener-arn $HTTPS_LISTENER_ARN \
    --priority 1 \
    --conditions Field=path-pattern,Values='/api/*' \
    --actions Type=forward,TargetGroupArn=$BACKEND_TG_ARN
```

#### Xác Minh ALB

```bash
# Lấy ALB DNS name
aws elbv2 describe-load-balancers \
    --names ielts-prod-alb \
    --query 'LoadBalancers[0].DNSName' --output text
```

#### Bước Tiếp Theo

Tiến hành đến [Route 53 & ACM](../5.5.3-Route53-ACM/) để cấu hình DNS và SSL certificate.

