---
title: "IAM Roles"
date:
weight: 1
chapter: false
pre: " <b> 5.9.1. </b> "
---

#### Overview

Create IAM roles for ECS tasks, Lambda functions, and other services.

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

- Use least privilege principle
- Use service-linked roles where available
- Regularly audit role permissions
- Use resource-based policies when possible

#### Next Steps

Proceed to [Secrets Manager](../5.9.2-Secrets-Manager/).

