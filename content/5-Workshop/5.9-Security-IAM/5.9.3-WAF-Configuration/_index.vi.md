---
title: "WAF Configuration"
date:
weight: 3
chapter: false
pre: " <b> 5.9.3. </b> "
---

#### Tổng Quan

Cấu hình AWS WAF để bảo vệ ứng dụng khỏi các web attacks phổ biến.

#### Tạo Web ACL

1. Điều hướng đến **AWS WAF** → **Web ACLs** → **Create web ACL**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Name** | `ielts-prod-waf` |
| **Resource type** | Regional (cho ALB) |
| **Region** | ap-southeast-1 |
| **Associated resources** | ielts-prod-alb |

#### Thêm Managed Rules

| Rule Group | Action | Priority |
|------------|--------|----------|
| AWS-AWSManagedRulesCommonRuleSet | Block | 1 |
| AWS-AWSManagedRulesSQLiRuleSet | Block | 2 |
| AWS-AWSManagedRulesKnownBadInputsRuleSet | Block | 3 |


#### Custom Rate Limiting Rule

```json
{
    "Name": "RateLimitRule",
    "Priority": 0,
    "Action": { "Block": {} },
    "Statement": {
        "RateBasedStatement": {
            "Limit": 2000,
            "AggregateKeyType": "IP"
        }
    },
    "VisibilityConfig": {
        "SampledRequestsEnabled": true,
        "CloudWatchMetricsEnabled": true,
        "MetricName": "RateLimitRule"
    }
}
```

#### Associate với ALB

```bash
aws wafv2 associate-web-acl \
    --web-acl-arn arn:aws:wafv2:ap-southeast-1:{account}:regional/webacl/ielts-prod-waf/{id} \
    --resource-arn arn:aws:elasticloadbalancing:ap-southeast-1:{account}:loadbalancer/app/ielts-prod-alb/{id}
```

#### Bước Tiếp Theo

Tiến hành đến [Monitoring & Logging](../../5.10-Monitoring/).

