---
title: "Bedrock Integration"
date:
weight: 5
chapter: false
pre: " <b> 5.6.5. </b> "
---

#### Tổng Quan

Cấu hình Amazon Bedrock cho AI model access bao gồm Gemma 3 12B và Titan Embeddings.

#### Enable Model Access

1. Điều hướng đến **Amazon Bedrock** → **Model access**
2. Request access đến:
   - **Amazon Titan Text Express** (cho assessments)
   - **Amazon Titan Embeddings V2** (cho RAG)
   - **Meta Llama 3** hoặc **Anthropic Claude** (optional)

![AI model](/images/5-Workshop/5.6-AI-Service/bedrock.png)


#### Test Bedrock API

```python
import boto3
import json

bedrock = boto3.client('bedrock-runtime', region_name='ap-southeast-1')

# Test Titan Text
response = bedrock.invoke_model(
    modelId='amazon.titan-text-express-v1',
    body=json.dumps({
        'inputText': 'Hello, how are you?',
        'textGenerationConfig': {
            'maxTokenCount': 100,
            'temperature': 0.7
        }
    })
)

print(json.loads(response['body'].read()))
```

#### Titan Embeddings cho RAG

```python
# Generate embeddings
response = bedrock.invoke_model(
    modelId='amazon.titan-embed-text-v2:0',
    body=json.dumps({
        'inputText': 'Document chunk text here'
    })
)

embedding = json.loads(response['body'].read())['embedding']
# Lưu trong OpenSearch hoặc dùng cho similarity search
```

#### Google Gemini Integration

Cho smart query generation:

```python
import google.generativeai as genai

genai.configure(api_key=os.environ['GEMINI_API_KEY'])
model = genai.GenerativeModel('gemini-2.5-flash')

response = model.generate_content(
    f"""Analyze this document and generate 10 intelligent questions:
    {document_text}
    """
)
```

#### Tối Ưu Chi Phí

- Sử dụng Titan Text Express cho assessments (lower cost)
- Batch embeddings generation khi có thể
- Implement caching cho repeated queries
- Sử dụng Google Gemini free tier cho query generation

#### Bước Tiếp Theo

Tiến hành đến [CI/CD Pipeline](../../5.8-CICD-Pipeline/).

