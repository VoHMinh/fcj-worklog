---
title: "AI Service Architecture"
date:
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
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

