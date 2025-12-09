---
title: "SQS Queues"
date:
weight: 2
chapter: false
pre: " <b> 5.6.2. </b> "
---

#### Overview

Create Amazon SQS queues for asynchronous AI processing with Dead Letter Queues for failed messages.

#### Create Writing Assessment Queue

| Setting | Value |
|---------|-------|
| **Name** | `ielts-ai-dev-writing-evaluation` |
| **Type** | Standard |
| **Visibility timeout** | 5 minutes |
| **Message retention** | 14 days |
| **Dead-letter queue** | ielts-ai-dev-writing-evaluation-dlq |
| **Max receives** | 3 |

#### Create Speaking Assessment Queue

| Setting | Value |
|---------|-------|
| **Name** | `ielts-ai-dev-speaking-evaluation` |
| **Visibility timeout** | 15 minutes |
| **Dead-letter queue** | ielts-ai-dev-speaking-evaluation-dlq |

#### Create Flashcard Generation Queue

| Setting | Value |
|---------|-------|
| **Name** | `ielts-ai-dev-flashcard-generation` |
| **Visibility timeout** | 15 minutes |
| **Dead-letter queue** | ielts-ai-dev-flashcard-generation-dlq |

![SQS](/images/5-Workshop/5.6-AI-Service/sqs.png)

#### AWS CLI Commands

```bash
# Create Dead Letter Queue
aws sqs create-queue --queue-name ielts-writing-dlq

# Create main queue with DLQ
aws sqs create-queue \
    --queue-name ielts-writing-queue \
    --attributes '{
        "VisibilityTimeout": "300",
        "MessageRetentionPeriod": "1209600",
        "RedrivePolicy": "{\"deadLetterTargetArn\":\"arn:aws:sqs:ap-southeast-1:{account}:ielts-writing-dlq\",\"maxReceiveCount\":\"3\"}"
    }'

# Repeat for speaking and flashcard queues
aws sqs create-queue --queue-name ielts-speaking-dlq
aws sqs create-queue --queue-name ielts-speaking-queue \
    --attributes '{"VisibilityTimeout": "900"}'

aws sqs create-queue --queue-name ielts-flashcard-dlq
aws sqs create-queue --queue-name ielts-flashcard-queue \
    --attributes '{"VisibilityTimeout": "900"}'
```

#### Queue Summary

| Queue | Visibility Timeout | DLQ | Max Receives |
|-------|-------------------|-----|--------------|
| ielts-writing-queue | 5 min | ielts-writing-dlq | 3 |
| ielts-speaking-queue | 15 min | ielts-speaking-dlq | 3 |
| ielts-flashcard-queue | 15 min | ielts-flashcard-dlq | 3 |

#### Next Steps

Proceed to [Lambda Functions](../5.7.3-Lambda-Functions/).

