---
title: "DynamoDB"
date:
weight: 4
chapter: false
pre: " <b> 5.6.4. </b> "
---

#### Tổng Quan

Tạo **hai bảng DynamoDB** để lưu kết quả Lambda function:

| Bảng | Sử Dụng Bởi | Mục Đích |
|------|-------------|----------|
| `bandup-evaluations` | Writing + Speaking Evaluators | Lưu điểm band IELTS, feedback, transcripts |
| `bandup-flashcard-sets` | Flashcard Generator | Lưu flashcards và document metadata |

---

### Bảng 1: Evaluations Table

Lưu kết quả từ cả hai Lambda functions **Writing Evaluator** và **Speaking Evaluator**.

| Cài Đặt | Giá Trị |
|---------|---------|
| **Table name** | `bandup-evaluations` |
| **Partition key** | `evaluation_id` (String) |
| **Sort key** | `user_id` (String) |
| **Billing mode** | On-demand (PAY_PER_REQUEST) |

**Schema Bảng:**

| Attribute | Type | Mô Tả |
|-----------|------|-------|
| `evaluation_id` | String | Session ID duy nhất (PK) |
| `user_id` | String | User identifier (SK) |
| `evaluation_type` | String | `writing` hoặc `speaking` |
| `status` | String | `processing`, `completed`, `failed` |
| `overall_band` | String | Điểm band IELTS tổng (ví dụ: "7.0") |
| `task_achievement_band` | String | Chỉ Writing |
| `coherence_band` | String | Chỉ Writing |
| `lexical_band` | String | Cả Writing và Speaking |
| `grammar_band` | String | Cả Writing và Speaking |
| `fluency_band` | String | Chỉ Speaking |
| `pronunciation_band` | String | Chỉ Speaking |
| `transcript` | String | Chỉ Speaking - audio đã transcribe |
| `feedback` | String | JSON-encoded detailed feedback |
| `model_used` | String | AI model sử dụng để đánh giá |
| `created_at` | Number | Unix timestamp |

**Ví Dụ Item (Writing):**

```json
{
  "evaluation_id": "eval-abc123",
  "user_id": "user-456",
  "evaluation_type": "writing",
  "status": "completed",
  "overall_band": "7.0",
  "task_achievement_band": "7.0",
  "coherence_band": "6.5",
  "lexical_band": "7.0",
  "grammar_band": "6.5",
  "feedback": "{\"strengths\":[...],\"weaknesses\":[...]}",
  "model_used": "gemini-writing_task2",
  "created_at": 1733644800
}
```

**Ví Dụ Item (Speaking):**

```json
{
  "evaluation_id": "speak-xyz789",
  "user_id": "user-456",
  "evaluation_type": "speaking",
  "status": "completed",
  "overall_band": "7.0",
  "fluency_band": "7.0",
  "lexical_band": "6.5",
  "grammar_band": "7.0",
  "pronunciation_band": "6.5",
  "transcript": "Well, I'd like to talk about...",
  "duration": "120.5",
  "word_count": 185,
  "feedback": "{\"fluency\":{...},\"pronunciation\":{...}}",
  "model_used": "gemini-2.5-flash-audio",
  "created_at": 1733644800
}
```

---

### Bảng 2: Flashcard Sets Table

Lưu kết quả từ Lambda function **Flashcard Generator**.

| Cài Đặt | Giá Trị |
|---------|---------|
| **Table name** | `bandup-flashcard-sets` |
| **Partition key** | `set_id` (String) |
| **Sort key** | `user_id` (String) |
| **Billing mode** | On-demand (PAY_PER_REQUEST) |

**Schema Bảng:**

| Attribute | Type | Mô Tả |
|-----------|------|-------|
| `set_id` | String | ID bộ flashcard duy nhất (PK) |
| `user_id` | String | User identifier (SK) |
| `document_id` | String | S3 key của tài liệu nguồn |
| `status` | String | `processing`, `completed`, `failed` |
| `flashcards` | String | JSON-encoded array của flashcards |
| `total_cards` | Number | Số lượng flashcards được tạo |
| `page_count` | Number | Số trang trong PDF nguồn |
| `chunk_count` | Number | Số text chunks đã index |
| `created_at` | Number | Unix timestamp |

**Ví Dụ Item:**

```json
{
  "set_id": "flashcard-set-123",
  "user_id": "user-456",
  "document_id": "uploads/documents/user-456/vocab-guide.pdf",
  "status": "completed",
  "flashcards": "[{\"question\":\"What is...\",\"answer\":\"...\"}]",
  "total_cards": 15,
  "page_count": 8,
  "chunk_count": 24,
  "created_at": 1733644800
}
```

---

### Tạo Tables với AWS CLI

```bash
# Tạo bảng evaluations (Writing + Speaking)
aws dynamodb create-table \
    --table-name bandup-evaluations \
    --attribute-definitions \
        AttributeName=evaluation_id,AttributeType=S \
        AttributeName=user_id,AttributeType=S \
    --key-schema \
        AttributeName=evaluation_id,KeyType=HASH \
        AttributeName=user_id,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --tags Key=Project,Value=bandup Key=Environment,Value=production

# Tạo bảng flashcard sets
aws dynamodb create-table \
    --table-name bandup-flashcard-sets \
    --attribute-definitions \
        AttributeName=set_id,AttributeType=S \
        AttributeName=user_id,AttributeType=S \
    --key-schema \
        AttributeName=set_id,KeyType=HASH \
        AttributeName=user_id,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --tags Key=Project,Value=bandup Key=Environment,Value=production
```

---

### Bật Point-in-Time Recovery

Bật PITR để bảo vệ dữ liệu:

```bash
# Bật PITR cho bảng evaluations
aws dynamodb update-continuous-backups \
    --table-name bandup-evaluations \
    --point-in-time-recovery-specification PointInTimeRecoveryEnabled=true

# Bật PITR cho bảng flashcard sets
aws dynamodb update-continuous-backups \
    --table-name bandup-flashcard-sets \
    --point-in-time-recovery-specification PointInTimeRecoveryEnabled=true
```

---

### Query Patterns

**Lấy lịch sử đánh giá của user:**

```python
response = table.query(
    IndexName='user_id-created_at-index',  # Nếu có GSI
    KeyConditionExpression=Key('user_id').eq('user-456'),
    ScanIndexForward=False,  # Mới nhất trước
    Limit=10
)
```

**Lấy đánh giá cụ thể theo ID:**

```python
response = table.get_item(
    Key={
        'evaluation_id': 'eval-abc123',
        'user_id': 'user-456'
    }
)
```

**Lấy flashcard sets của user:**

```python
response = table.query(
    KeyConditionExpression=Key('user_id').eq('user-456'),
    FilterExpression=Attr('status').eq('completed')
)
```

---

### Biến Môi Trường Lambda

Cấu hình Lambda functions để sử dụng các bảng này:

| Lambda Function | Biến Môi Trường | Giá Trị |
|-----------------|-----------------|---------|
| Writing Evaluator | `DYNAMODB_EVALUATIONS` | `bandup-evaluations` |
| Speaking Evaluator | `DYNAMODB_EVALUATIONS` | `bandup-evaluations` |
| Flashcard Generator | `DYNAMODB_FLASHCARD_SETS` | `bandup-flashcard-sets` |

---

### IAM Policy cho Lambda Access

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DynamoDBAccess",
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:UpdateItem",
        "dynamodb:Query"
      ],
      "Resource": [
        "arn:aws:dynamodb:${AWS_REGION}:${AWS_ACCOUNT_ID}:table/bandup-evaluations",
        "arn:aws:dynamodb:${AWS_REGION}:${AWS_ACCOUNT_ID}:table/bandup-flashcard-sets"
      ]
    }
  ]
}
```

---

#### Bước Tiếp Theo

Tiến hành đến [Bedrock Integration](../5.7.5-Bedrock-Integration/) để cấu hình Amazon Titan Embeddings.
