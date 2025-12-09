---
title: "Bedrock Integration"
date:
weight: 5
chapter: false
pre: " <b> 5.6.5. </b> "
---

#### Overview

Configure Amazon Bedrock for AI model access including Gemma 3 12B and Titan Embeddings.

#### Enable Model Access

1. Navigate to **Amazon Bedrock** → **Model access**
2. Request access to:
   - **Amazon Titan Text Express** (for assessments)
   - **Amazon Titan Embeddings V2** (for RAG)
   - **Meta Llama 3** or **Anthropic Claude** (optional)

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

#### Titan Embeddings for RAG

```python
# Generate embeddings
response = bedrock.invoke_model(
    modelId='amazon.titan-embed-text-v2:0',
    body=json.dumps({
        'inputText': 'Document chunk text here'
    })
)

embedding = json.loads(response['body'].read())['embedding']
# Store in OpenSearch or use for similarity search
```

#### Google Gemini Integration

For smart query generation:

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

#### Cost Optimization

- Use Titan Text Express for assessments (lower cost)
- Batch embeddings generation where possible
- Implement caching for repeated queries
- Use Google Gemini free tier for query generation

#### Next Steps

Proceed to [CI/CD Pipeline](../../5.8-CICD-Pipeline/).

