---
title: "Secrets Manager"
date:
weight: 2
chapter: false
pre: " <b> 5.9.2. </b> "
---

#### Overview

Store sensitive credentials securely in AWS Secrets Manager.

#### Create Database Secret

1. Navigate to **Secrets Manager** → **Store a new secret**

| Setting | Value |
|---------|-------|
| **Secret type** | Credentials for RDS database |
| **Username** | ielts_admin |
| **Password** | (auto-generated) |
| **Database** | ielts-prod-db |
| **Secret name** | `ielts-prod/db` |


#### Create API Keys Secret

```bash
aws secretsmanager create-secret \
    --name ielts-prod/api-keys \
    --secret-string '{
        "GEMINI_API_KEY": "your-gemini-api-key",
        "GOOGLE_CLIENT_ID": "your-google-oauth-client-id",
        "GOOGLE_CLIENT_SECRET": "your-google-oauth-secret"
    }'
```

#### Accessing Secrets in ECS

In task definition, reference secrets:

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

#### Accessing Secrets in Lambda

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

#### Next Steps

Proceed to [WAF Configuration](../5.9.3-WAF-Configuration/).

