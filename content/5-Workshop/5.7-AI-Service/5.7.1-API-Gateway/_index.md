---
title: "API Gateway"
date:
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

#### Overview

Create Amazon API Gateway as the entry point for AI service requests.

#### Create REST API

1. Navigate to **API Gateway** → **Create API** → **REST API**

| Setting | Value |
|---------|-------|
| **API name** | `ielts-ai-api` |
| **API type** | REST API |
| **Endpoint type** | Regional |

#### Create Resources and Methods

**Endpoints:**

| Method | Path | Description |
|--------|------|-------------|
| POST | `/writing/evaluate` | Submit writing sample |
| POST | `/speaking/evaluate` | Submit audio recording |
| POST | `/flashcards/generate` | Generate flashcard |
| POST | `/upload/audio` | Upload audio |
| POST | `/upload/document` | Upload document |

![API setup](/images/5-Workshop/5.7-AI-Service/api.png)

#### Configure SQS Integration

For each POST endpoint, configure SQS integration:

1. Select method → **Integration Request**
2. Integration type: **AWS Service**
3. AWS Service: **SQS**
4. HTTP method: **POST**
5. Action: **SendMessage**
6. Execution role: API Gateway role with SQS permissions

**Request Mapping Template:**

```velocity
Action=SendMessage&MessageBody=$util.urlEncode($input.body)&QueueUrl=$util.urlEncode('https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-writing-queue')
```

#### Enable CORS

1. Select resource → **Enable CORS**
2. Access-Control-Allow-Origin: `*` (or specific domain)
3. Access-Control-Allow-Methods: `POST, GET, OPTIONS`

#### Deploy API

1. **Actions** → **Deploy API**
2. Stage name: `dev`
3. Note the invoke URL: `https://{api-id}.execute-api.ap-southeast-1.amazonaws.com/dev`

#### AWS CLI Commands

```bash
# Create REST API
API_ID=$(aws apigateway create-rest-api \
    --name ielts-ai-api \
    --endpoint-configuration types=REGIONAL \
    --query 'id' --output text)

# Get root resource
ROOT_ID=$(aws apigateway get-resources \
    --rest-api-id $API_ID \
    --query 'items[?path==`/`].id' --output text)

# Create /ai resource
AI_RESOURCE=$(aws apigateway create-resource \
    --rest-api-id $API_ID \
    --parent-id $ROOT_ID \
    --path-part ai \
    --query 'id' --output text)

# Create /ai/writing-assessment resource
aws apigateway create-resource \
    --rest-api-id $API_ID \
    --parent-id $AI_RESOURCE \
    --path-part writing-assessment
```

#### Next Steps

Proceed to [SQS Queues](../5.7.2-SQS-Queues/).

