---
title: "AI Service Architecture"
date:
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
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

