---
title: "API Gateway"
date:
weight: 1
chapter: false
pre: " <b> 5.7.1. </b> "
---

#### Tổng Quan

Tạo Amazon API Gateway làm entry point cho AI service requests.

#### Tạo REST API

1. Điều hướng đến **API Gateway** → **Create API** → **REST API**

| Cài Đặt | Giá Trị |
|---------|-------|
| **API name** | `ielts-ai-api` |
| **API type** | REST API |
| **Endpoint type** | Regional |

#### Tạo Resources và Methods

**Endpoints:**

| Method | Path | Description |
|--------|------|-------------|
| POST | `/writing/evaluate` | Submit writing sample |
| POST | `/speaking/evaluate` | Submit audio recording |
| POST | `/flashcards/generate` | Generate flashcard |
| POST | `/upload/audio` | Upload audio |
| POST | `/upload/document` | Upload document |

![API setup](/images/5-Workshop/5.7-AI-Service/api.png)

#### Cấu Hình SQS Integration

Cho mỗi POST endpoint, cấu hình SQS integration:

1. Chọn method → **Integration Request**
2. Integration type: **AWS Service**
3. AWS Service: **SQS**
4. HTTP method: **POST**
5. Action: **SendMessage**
6. Execution role: API Gateway role với SQS permissions

**Request Mapping Template:**

```velocity
Action=SendMessage&MessageBody=$util.urlEncode($input.body)&QueueUrl=$util.urlEncode('https://sqs.ap-southeast-1.amazonaws.com/{account}/ielts-writing-queue')
```

#### Enable CORS

1. Chọn resource → **Enable CORS**
2. Access-Control-Allow-Origin: `*` (hoặc specific domain)
3. Access-Control-Allow-Methods: `POST, GET, OPTIONS`

#### Deploy API

1. **Actions** → **Deploy API**
2. Stage name: `prod`
3. Ghi lại invoke URL: `https://{api-id}.execute-api.ap-southeast-1.amazonaws.com/prod`

#### AWS CLI Commands

```bash
# Tạo REST API
API_ID=$(aws apigateway create-rest-api \
    --name ielts-ai-api \
    --endpoint-configuration types=REGIONAL \
    --query 'id' --output text)

# Lấy root resource
ROOT_ID=$(aws apigateway get-resources \
    --rest-api-id $API_ID \
    --query 'items[?path==`/`].id' --output text)

# Tạo /ai resource
AI_RESOURCE=$(aws apigateway create-resource \
    --rest-api-id $API_ID \
    --parent-id $ROOT_ID \
    --path-part ai \
    --query 'id' --output text)

# Tạo /ai/writing-assessment resource
aws apigateway create-resource \
    --rest-api-id $API_ID \
    --parent-id $AI_RESOURCE \
    --path-part writing-assessment
```

#### Bước Tiếp Theo

Tiến hành đến [SQS Queues](../5.7.2-SQS-Queues/).

