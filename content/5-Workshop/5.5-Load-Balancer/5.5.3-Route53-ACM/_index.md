---
title: "Route 53 & ACM"
date:
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### Overview

Configure Route 53 DNS records and AWS Certificate Manager (ACM) for SSL/TLS certificates.

#### Request ACM Certificate

1. Navigate to **AWS Certificate Manager** → **Request certificate**
2. Select **Request a public certificate**

| Setting | Value |
|---------|-------|
| **Domain names** | `bandup.bughunters.site`, `*.bandup.bughunters.site` |
| **Validation method** | DNS validation |


#### DNS Validation

1. Click on the certificate
2. Click **Create records in Route 53**
3. Wait for status: **Issued** (may take 5-30 minutes)

#### Configure Route 53

**Create Hosted Zone (if not exists):**

1. Navigate to **Route 53** → **Hosted zones** → **Create hosted zone**
2. Domain name: `bughunters.site`

**Create A Record for ALB:**

| Setting | Value |
|---------|-------|
| **Record name** | `bandup` |
| **Record type** | A |
| **Alias** | Yes |
| **Route traffic to** | ALB in ap-southeast-1 |
| **Routing policy** | Simple |


#### AWS CLI Commands

```bash
# Request ACM certificate
aws acm request-certificate \
    --domain-name bandup.bughunters.site \
    --subject-alternative-names "*.bandup.bughunters.site" \
    --validation-method DNS \
    --region ap-southeast-1

# Create Route 53 record (after getting ALB DNS name)
aws route53 change-resource-record-sets \
    --hosted-zone-id $HOSTED_ZONE_ID \
    --change-batch '{
        "Changes": [{
            "Action": "CREATE",
            "ResourceRecordSet": {
                "Name": "bandup.bughunters.site",
                "Type": "A",
                "AliasTarget": {
                    "HostedZoneId": "Z1LMS91P8CMLE5",
                    "DNSName": "ielts-prod-alb-xxx.ap-southeast-1.elb.amazonaws.com",
                    "EvaluateTargetHealth": true
                }
            }
        }]
    }'
```

#### Verify DNS

```bash
# Test DNS resolution
nslookup bandup.bughunters.site

# Test HTTPS
curl -I https://bandup.bughunters.site
```

#### Next Steps

Proceed to [Database & Storage Setup](../../5.6-Database-Storage/) to configure RDS and S3.

