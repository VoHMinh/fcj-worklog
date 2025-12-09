---
title: "WAF Configuration"
date:
weight: 3
chapter: false
pre: " <b> 5.9.3. </b> "
---

#### Overview

Configure AWS WAF to protect the application from common web attacks.

#### Create Web ACL

1. Navigate to **AWS WAF** → **Web ACLs** → **Create web ACL**

| Setting | Value |
|---------|-------|
| **Name** | `ielts-prod-waf` |
| **Resource type** | Regional (for ALB) |
| **Region** | ap-southeast-1 |
| **Associated resources** | ielts-prod-alb |

#### Add Managed Rules

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

#### Associate with ALB

```bash
aws wafv2 associate-web-acl \
    --web-acl-arn arn:aws:wafv2:ap-southeast-1:{account}:regional/webacl/ielts-prod-waf/{id} \
    --resource-arn arn:aws:elasticloadbalancing:ap-southeast-1:{account}:loadbalancer/app/ielts-prod-alb/{id}
```

#### Next Steps

Proceed to [Monitoring & Logging](../../5.10-Monitoring/).

