---
title: "DynamoDB"
date:
weight: 4
chapter: false
pre: " <b> 5.7.4. </b> "
---

#### Overview

Create **two DynamoDB tables** to store Lambda function results:

| Table | Used By | Purpose |
|-------|---------|---------|
| `bandup-evaluations` | Writing + Speaking Evaluators | Stores IELTS band scores, feedback, transcripts |
| `bandup-flashcard-sets` | Flashcard Generator | Stores generated flashcards and document metadata |

---

### Table 1: Evaluations Table

Stores results from both **Writing Evaluator** and **Speaking Evaluator** Lambda functions.

| Setting | Value |
|---------|-------|
| **Table name** | `bandup-evaluations` |
| **Partition key** | `evaluation_id` (String) |
| **Sort key** | `user_id` (String) |
| **Billing mode** | On-demand (PAY_PER_REQUEST) |

**Table Schema:**

| Attribute | Type | Description |
|-----------|------|-------------|
| `evaluation_id` | String | Unique session ID (PK) |
| `user_id` | String | User identifier (SK) |
| `evaluation_type` | String | `writing` or `speaking` |
| `status` | String | `processing`, `completed`, `failed` |
| `overall_band` | String | Overall IELTS band score (e.g., "7.0") |
| `task_achievement_band` | String | Writing only |
| `coherence_band` | String | Writing only |
| `lexical_band` | String | Both Writing and Speaking |
| `grammar_band` | String | Both Writing and Speaking |
| `fluency_band` | String | Speaking only |
| `pronunciation_band` | String | Speaking only |
| `transcript` | String | Speaking only - transcribed audio |
| `feedback` | String | JSON-encoded detailed feedback |
| `model_used` | String | AI model used for evaluation |
| `created_at` | Number | Unix timestamp |

**Example Item (Writing):**

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

**Example Item (Speaking):**

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

### Table 2: Flashcard Sets Table

Stores results from **Flashcard Generator** Lambda function.

| Setting | Value |
|---------|-------|
| **Table name** | `bandup-flashcard-sets` |
| **Partition key** | `set_id` (String) |
| **Sort key** | `user_id` (String) |
| **Billing mode** | On-demand (PAY_PER_REQUEST) |

**Table Schema:**

| Attribute | Type | Description |
|-----------|------|-------------|
| `set_id` | String | Unique flashcard set ID (PK) |
| `user_id` | String | User identifier (SK) |
| `document_id` | String | Source document S3 key |
| `status` | String | `processing`, `completed`, `failed` |
| `flashcards` | String | JSON-encoded array of flashcards |
| `total_cards` | Number | Number of flashcards generated |
| `page_count` | Number | Number of pages in source PDF |
| `chunk_count` | Number | Number of text chunks indexed |
| `created_at` | Number | Unix timestamp |

**Example Item:**

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

### Create Tables with AWS CLI

```bash
# Create evaluations table (Writing + Speaking)
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

# Create flashcard sets table
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

### Enable Point-in-Time Recovery

Enable PITR for data protection:

```bash
# Enable PITR for evaluations table
aws dynamodb update-continuous-backups \
    --table-name bandup-evaluations \
    --point-in-time-recovery-specification PointInTimeRecoveryEnabled=true

# Enable PITR for flashcard sets table
aws dynamodb update-continuous-backups \
    --table-name bandup-flashcard-sets \
    --point-in-time-recovery-specification PointInTimeRecoveryEnabled=true
```

---

### Query Patterns

**Get user's evaluation history:**

```python
response = table.query(
    IndexName='user_id-created_at-index',  # If GSI exists
    KeyConditionExpression=Key('user_id').eq('user-456'),
    ScanIndexForward=False,  # Most recent first
    Limit=10
)
```

**Get specific evaluation by ID:**

```python
response = table.get_item(
    Key={
        'evaluation_id': 'eval-abc123',
        'user_id': 'user-456'
    }
)
```

**Get user's flashcard sets:**

```python
response = table.query(
    KeyConditionExpression=Key('user_id').eq('user-456'),
    FilterExpression=Attr('status').eq('completed')
)
```

---

### Lambda Environment Variables

Configure Lambda functions to use these tables:

| Lambda Function | Environment Variable | Value |
|-----------------|---------------------|-------|
| Writing Evaluator | `DYNAMODB_EVALUATIONS` | `bandup-evaluations` |
| Speaking Evaluator | `DYNAMODB_EVALUATIONS` | `bandup-evaluations` |
| Flashcard Generator | `DYNAMODB_FLASHCARD_SETS` | `bandup-flashcard-sets` |

---

### IAM Policy for Lambda Access

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

#### Next Steps

Proceed to [Bedrock Integration](../5.7.5-Bedrock-Integration/) to configure Amazon Titan Embeddings.
