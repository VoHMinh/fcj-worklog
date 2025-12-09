---
title: "Secrets Manager"
date:
weight: 2
chapter: false
pre: " <b> 5.9.2. </b> "
---

#### Tổng Quan

Lưu trữ sensitive credentials an toàn trong AWS Secrets Manager.

#### Tạo Database Secret

1. Điều hướng đến **Secrets Manager** → **Store a new secret**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Secret type** | Credentials for RDS database |
| **Username** | ielts_admin |
| **Password** | (auto-generated) |
| **Database** | ielts-prod-db |
| **Secret name** | `ielts-prod/db` |


#### Tạo API Keys Secret

```bash
aws secretsmanager create-secret \
    --name ielts-prod/api-keys \
    --secret-string '{
        "GEMINI_API_KEY": "your-gemini-api-key",
        "GOOGLE_CLIENT_ID": "your-google-oauth-client-id",
        "GOOGLE_CLIENT_SECRET": "your-google-oauth-secret"
    }'
```

#### Truy Cập Secrets trong ECS

Trong task definition, reference secrets:

```json
{
    "secrets": [
        {
            "name": "DB_PASSWORD",
            "valueFrom": "arn:aws:secretsmanager:ap-southeast-1:{account}:secret:ielts-prod/db:password::"
        }
    ]
}
```

#### Truy Cập Secrets trong Lambda

```python
import boto3
import json

def get_secret(secret_name):
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId=secret_name)
    return json.loads(response['SecretString'])

# Usage
secrets = get_secret('ielts-prod/api-keys')
gemini_key = secrets['GEMINI_API_KEY']
```

#### Bước Tiếp Theo

Tiến hành đến [WAF Configuration](../5.9.3-WAF-Configuration/).

