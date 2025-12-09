---
title: "S3 & CloudFront"
date:
weight: 3
chapter: false
pre: " <b> 5.6.3. </b> "
---

#### Overview

Create S3 bucket for media storage and CloudFront distribution for CDN.

#### Create S3 Bucket

1. Navigate to **S3** → **Create bucket**

| Setting | Value |
|---------|-------|
| **Bucket name** | `ielts-prod-media-{account-id}` |
| **Region** | ap-southeast-1 |
| **Block public access** | On (all options) |
| **Versioning** | Enabled |
| **Encryption** | SSE-S3 |

#### Configure Bucket Policy

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

#### Create CloudFront Distribution

| Setting | Value |
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

#### Next Steps

Proceed to [AI Service Architecture](../../5.7-AI-Service/) to set up serverless AI processing.

