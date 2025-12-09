---
title: "S3 & CloudFront"
date:
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

#### Tổng Quan

Tạo S3 bucket cho media storage và CloudFront distribution cho CDN.

#### Tạo S3 Bucket

1. Điều hướng đến **S3** → **Create bucket**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Bucket name** | `ielts-prod-media-{account-id}` |
| **Region** | ap-southeast-1 |
| **Block public access** | On (all options) |
| **Versioning** | Enabled |
| **Encryption** | SSE-S3 |

#### Cấu Hình Bucket Policy

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::ielts-prod-media-*/*",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::{account}:distribution/{distribution-id}"
                }
            }
        }
    ]
}
```

#### Tạo CloudFront Distribution

| Cài Đặt | Giá Trị |
|---------|-------|
| **Origin domain** | S3 bucket |
| **Origin access** | Origin access control (OAC) |
| **Viewer protocol policy** | Redirect HTTP to HTTPS |
| **Cache policy** | CachingOptimized |
| **Price class** | Use all edge locations |
| **SSL certificate** | ACM certificate |


#### Lifecycle Policy

```json
{
    "Rules": [
        {
            "ID": "MoveToGlacier",
            "Status": "Enabled",
            "Filter": {"Prefix": "backups/"},
            "Transitions": [
                {"Days": 90, "StorageClass": "GLACIER"}
            ]
        }
    ]
}
```

#### Bước Tiếp Theo

Tiến hành đến [AI Service Architecture](../../5.7-AI-Service/) để thiết lập serverless AI processing.

