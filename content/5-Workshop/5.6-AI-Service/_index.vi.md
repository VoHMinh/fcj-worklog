---
title: "AI Service Architecture"
date:
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Tổng Quan

Phần này bao gồm kiến trúc serverless AI service sử dụng API Gateway, SQS, Lambda, DynamoDB, và Amazon Bedrock cho intelligent assessment và content generation.

#### Kiến Trúc AI Service

![Kiến Trúc AI](/images/2-Proposal/AWS-Bandup-Architecture.png)

AI service triển khai theo mô hình fully serverless:

```
User → API Gateway → SQS Queue → Lambda → AI Model → DynamoDB → Response
```

**Ba Lambda Functions:**

| Function | Mục Đích | AI Model |
|----------|---------|----------|
| **Writing Evaluate** | IELTS writing assessment | Gemma 3 12B / Gemini |
| **Speaking Evaluate** | Audio transcription + assessment | Transcribe + Gemma 3 12B |
| **Flashcard Generate** | RAG-based flashcard creation | Titan Embeddings + Gemini |

#### Nội Dung

1. [API Gateway](5.7.1-API-Gateway/)
2. [SQS Queues](5.7.2-SQS-Queues/)
3. [Lambda Functions](5.7.3-Lambda-Functions/)
4. [DynamoDB](5.7.4-DynamoDB/)
5. [Bedrock Integration](5.7.5-Bedrock-Integration/)

#### Request Flow

1. **User submits request** (writing sample, audio, document)
2. **API Gateway** validates và enqueues message đến SQS
3. **SQS** triggers appropriate Lambda function
4. **Lambda** processes với AI model (Bedrock/Gemini)
5. **Results stored** trong DynamoDB
6. **User retrieves** results qua API

#### Thời Gian Ước Tính: ~60 phút

#### Ước Tính Chi Phí

**Tóm Tắt Chi Phí Hàng Tháng:**

| Chi Phí Ban Đầu | Chi Phí Hàng Tháng | Tổng Chi Phí 12 Tháng | Tiền Tệ |
|-----------------|---------------------|----------------------|---------|
| $0.00 | $23.61 | $283.32 | USD |

*Lưu ý: Bao gồm chi phí ban đầu*

**Chi Tiết Phân Tích Chi Phí:**

| Dịch Vụ | Mô Tả | Khu Vực | Chi Phí Hàng Tháng (USD) | Chi Phí Hàng Năm (USD) |
|---------|-------|---------|-------------------------|------------------------|
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
| **Tổng Cộng** | | | **$23.61** | **$283.32** |

*AWS Pricing Calculator chỉ cung cấp ước tính về phí AWS của bạn và không bao gồm bất kỳ loại thuế nào có thể áp dụng. Phí thực tế của bạn phụ thuộc vào nhiều yếu tố, bao gồm việc sử dụng thực tế các dịch vụ AWS của bạn.*

**Xem chi tiết phân tích chi phí:** [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=58240ad1860ed37c90ad0be3ea3654ebce5d91ac)

