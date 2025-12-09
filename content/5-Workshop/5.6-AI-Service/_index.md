---
title: "AI Service Architecture"
date:
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Overview

This section covers the serverless AI service architecture using API Gateway, SQS, Lambda, DynamoDB, and Amazon Bedrock for intelligent assessment and content generation.

#### AI Service Architecture

![AI Architecture](/images/2-Proposal/AWS-Bandup-Architecture.png)

The AI service implements a fully serverless pattern:

```
User → API Gateway → SQS Queue → Lambda → AI Model → DynamoDB → Response
```

**Three Lambda Functions:**

| Function | Purpose | AI Model |
|----------|---------|----------|
| **Writing Evaluate** | IELTS writing assessment | Gemma 3 12B / Gemini |
| **Speaking Evaluate** | Audio transcription + assessment | Transcribe + Gemma 3 12B |
| **Flashcard Generate** | RAG-based flashcard creation | Titan Embeddings + Gemini |

#### Content

1. [API Gateway](5.7.1-API-Gateway/)
2. [SQS Queues](5.7.2-SQS-Queues/)
3. [Lambda Functions](5.7.3-Lambda-Functions/)
4. [DynamoDB](5.7.4-DynamoDB/)
5. [Bedrock Integration](5.7.5-Bedrock-Integration/)

#### Request Flow

1. **User submits request** (writing sample, audio, document)
2. **API Gateway** validates and enqueues message to SQS
3. **SQS** triggers appropriate Lambda function
4. **Lambda** processes with AI model (Bedrock/Gemini)
5. **Results stored** in DynamoDB
6. **User retrieves** results via API

#### Estimated Time: ~60 minutes

#### Cost Estimate

**Monthly Cost Summary:**

| Upfront Cost | Monthly Cost | Total 12 Months Cost | Currency |
|--------------|--------------|----------------------|----------|
| $0.00 | $23.61 | $283.32 | USD |

*Note: Includes upfront cost*

**Detailed Cost Breakdown:**

| Service | Description | Region | Monthly Cost (USD) | Annual Cost (USD) |
|---------|-------------|--------|---------------------|-------------------|
| **AWS Lambda** | Writing Evaluator | Asia Pacific (Singapore) | $0.00 | $0.00 |
| **AWS Lambda** | Speaking Evaluator | Asia Pacific (Singapore) | $0.00 | $0.00 |
| **AWS Lambda** | Evaluation Status | Asia Pacific (Singapore) | $0.00 | $0.00 |
| **AWS Lambda** | S3 Upload | Asia Pacific (Singapore) | $0.00 | $0.00 |
| **Amazon API Gateway** | HTTP API | Asia Pacific (Singapore) | $0.42 | $5.04 |
| **S3 Standard** | Audio bucket | Asia Pacific (Singapore) | $0.51 | $6.12 |
| **S3 Standard** | Documents Bucket | Asia Pacific (Singapore) | $0.53 | $6.36 |
| **DynamoDB** | Evaluations Table | Asia Pacific (Singapore) | $0.37 | $4.44 |
| **DynamoDB** | Flashcard Sets Table | Asia Pacific (Singapore) | $0.52 | $6.24 |
| **Amazon SQS** | Writing/Speaking/Flashcard queues | Asia Pacific (Singapore) | $0.00 | $0.00 |
| **AWS Secrets Manager** | Secrets management | Asia Pacific (Singapore) | $0.45 | $5.40 |
| **Amazon CloudWatch** | RAG Lambda logs | Asia Pacific (Singapore) | $0.01 | $0.08 |
| **Amazon Bedrock** | Bedrock inference | US East (N. Virginia) | $0.50 | $6.00 |
| **OpenAI** | GPT inference | US East (N. Virginia) | $20.30 | $243.65 |
| **Total** | | | **$23.61** | **$283.32** |

*AWS Pricing Calculator provides only an estimate of your AWS fees and doesn't include any taxes that might apply. Your actual fees depend on a variety of factors, including your actual usage of AWS services.*

**View detailed cost breakdown:** [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=58240ad1860ed37c90ad0be3ea3654ebce5d91ac)

