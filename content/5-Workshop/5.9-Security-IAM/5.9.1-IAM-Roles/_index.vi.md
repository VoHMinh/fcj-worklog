---
title: "IAM Roles"
date:
weight: 1
chapter: false
pre: " <b> 5.9.1. </b> "
---

#### Tổng Quan

Tạo IAM roles cho ECS tasks, Lambda functions, và các services khác.

#### ECS Task Execution Role

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:GetAuthorizationToken",
                "ecr:BatchCheckLayerAvailability",
                "ecr:GetDownloadUrlForLayer",
                "ecr:BatchGetImage",
                "logs:CreateLogStream",
                "logs:PutLogEvents",
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "*"
        }
    ]
}
```

#### Lambda Execution Role

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "bedrock:InvokeModel",
                "dynamodb:PutItem",
                "dynamodb:GetItem",
                "dynamodb:Query",
                "s3:GetObject",
                "sqs:ReceiveMessage",
                "sqs:DeleteMessage",
                "sqs:GetQueueAttributes",
                "logs:*",
                "secretsmanager:GetSecretValue"
            ],
            "Resource": "*"
        }
    ]
}
```


#### Best Practices

- Sử dụng nguyên tắc least privilege
- Sử dụng service-linked roles khi có sẵn
- Regularly audit role permissions
- Sử dụng resource-based policies khi có thể

#### Bước Tiếp Theo

Tiến hành đến [Secrets Manager](../5.9.2-Secrets-Manager/).

