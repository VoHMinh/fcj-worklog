---
title: "Route 53 & ACM"
date:
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

#### Tổng Quan

Cấu hình Route 53 DNS records và AWS Certificate Manager (ACM) cho SSL/TLS certificates.

#### Request ACM Certificate

1. Điều hướng đến **AWS Certificate Manager** → **Request certificate**
2. Chọn **Request a public certificate**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Domain names** | `bandup.bughunters.site`, `*.bandup.bughunters.site` |
| **Validation method** | DNS validation |


#### DNS Validation

1. Click vào certificate
2. Click **Create records in Route 53**
3. Đợi status: **Issued** (có thể mất 5-30 phút)

#### Cấu Hình Route 53

**Tạo Hosted Zone (nếu chưa có):**

1. Điều hướng đến **Route 53** → **Hosted zones** → **Create hosted zone**
2. Domain name: `bughunters.site`

**Tạo A Record cho ALB:**

| Cài Đặt | Giá Trị |
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

# Tạo Route 53 record (sau khi có ALB DNS name)
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

#### Xác Minh DNS

```bash
# Test DNS resolution
nslookup bandup.bughunters.site

# Test HTTPS
curl -I https://bandup.bughunters.site
```

#### Bước Tiếp Theo

Tiến hành đến [Database & Storage Setup](../../5.6-Database-Storage/) để cấu hình RDS và S3.

