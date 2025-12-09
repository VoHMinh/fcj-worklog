---
title: "Lambda Functions"
date:
weight: 3
chapter: false
pre: " <b> 5.7.3. </b> "
---

#### Tổng Quan

Tầng AI Service bao gồm **bốn Lambda functions** cung cấp sức mạnh cho nền tảng học IELTS. Các functions này xử lý yêu cầu bất đồng bộ qua SQS queues và tích hợp với **Google Gemini API** và **Amazon Bedrock** để đánh giá bằng AI.

![AI Service Architecture](/images/5-Workshop/5.7-AI-Service/lambda-flow.png)

---

### Lambda Function 1: Writing Evaluator

Đánh giá bài luận IELTS Writing Task 1 và Task 2 sử dụng Gemini API với điểm band chi tiết.

| Cài Đặt | Giá Trị |
|---------|---------|
| **Function name** | `bandup-writing-evaluator` |
| **Runtime** | Python 3.11 |
| **Memory** | 1024 MB |
| **Timeout** | 5 phút |
| **Trigger** | SQS (`bandup-writing-queue`) |
| **AI Model** | Google Gemini 2.0 Flash |

**Triển Khai Chính:**

```python
import json
import os
import boto3
import logging
from typing import Dict, Any

logger = logging.getLogger()
logger.setLevel(logging.INFO)

# Import từ Lambda layer
from lambda_shared.gemini_client import GeminiClient
from secrets_helper import get_gemini_api_key

def lambda_handler(event: Dict[str, Any], context: Any) -> Dict[str, Any]:
    """Đánh giá bài luận IELTS Writing sử dụng Gemini API."""
    
    # Parse SQS message hoặc API Gateway request
    if is_sqs_event(event):
        request_data, job_id = parse_sqs_message(event)
        update_job_status(job_id, 'processing', 'writing')
    else:
        request_data = json.loads(event.get('body', '{}'))
    
    # Lấy API key an toàn từ Secrets Manager
    gemini_api_key = get_gemini_api_key()  # Lấy từ AWS Secrets Manager
    gemini_client = GeminiClient(api_key=gemini_api_key)
    
    # Trích xuất parameters từ request
    user_id = request_data.get('user_id')
    essay_content = request_data.get('essay_content')
    task_type = request_data.get('task_type', 'TASK_2')
    
    # Xây dựng prompt đánh giá
    prompt = build_writing_prompt(essay_content, task_type)
    
    # Gọi Gemini API để đánh giá
    response = gemini_client.generate_evaluation(
        prompt=prompt,
        feature='writing_task2',
        max_retries=3,
        timeout=60
    )
    
    # Parse và validate band scores
    evaluation = parse_gemini_response(response['content'])
    
    # Xây dựng kết quả với tiêu chí IELTS
    result = {
        'session_id': request_data.get('session_id'),
        'overall_band': evaluation.get('overall_band'),
        'task_achievement_band': evaluation['task_achievement']['band'],
        'coherence_band': evaluation['coherence_cohesion']['band'],
        'lexical_band': evaluation['lexical_resource']['band'],
        'grammar_band': evaluation['grammatical_range_accuracy']['band'],
        'feedback': evaluation
    }
    
    # Lưu vào DynamoDB
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table(os.environ.get('DYNAMODB_EVALUATIONS'))
    table.put_item(Item={
        'evaluation_id': result['session_id'],
        'user_id': user_id,
        'evaluation_type': 'writing',
        'status': 'completed',
        **result
    })
    
    return {'statusCode': 200, 'body': json.dumps(result)}
```

**Mẫu Prompt Gemini:**

```python
def build_writing_prompt(essay_content: str, task_type: str) -> str:
    return f"""You are an experienced IELTS examiner. Evaluate this essay:

Task Type: {task_type}

ESSAY:
{essay_content}

Evaluate using IELTS band descriptors (1-9, 0.5 increments):
1. Task Achievement - Addresses all parts of task
2. Coherence and Cohesion - Logical organization
3. Lexical Resource - Vocabulary range and accuracy
4. Grammatical Range and Accuracy - Sentence structures

RESPOND IN JSON FORMAT:
{{
  "overall_band": <float>,
  "task_achievement": {{"band": <float>, "feedback": "..."}},
  "coherence_cohesion": {{"band": <float>, "feedback": "..."}},
  "lexical_resource": {{"band": <float>, "feedback": "..."}},
  "grammatical_range_accuracy": {{"band": <float>, "feedback": "..."}},
  "quoted_examples": [{{"quote": "...", "issue": "...", "suggestion": "..."}}]
}}"""
```

---

### Lambda Function 2: Speaking Evaluator

Đánh giá IELTS Speaking sử dụng **Gemini native audio processing** - rẻ hơn 72% và nhanh gấp 2 lần so với AWS Transcribe.

| Cài Đặt | Giá Trị |
|---------|---------|
| **Function name** | `bandup-speaking-evaluator` |
| **Runtime** | Python 3.11 |
| **Memory** | 2048 MB |
| **Timeout** | 5 phút |
| **Trigger** | SQS (`bandup-speaking-queue`) |
| **AI Model** | Gemini 2.5 Flash (Native Audio) |

**Triển Khai Chính:**

```python
import json
import os
import boto3
import logging
from typing import Dict, Any, Tuple

logger = logging.getLogger()

# Import từ Lambda layer
from lambda_shared.gemini_client import GeminiClient
from secrets_helper import get_gemini_api_key

def download_audio_from_s3(audio_url: str) -> Tuple[bytes, str]:
    """Tải file audio từ S3 và xác định MIME type."""
    s3_client = boto3.client('s3')
    
    # Parse S3 URL: s3://bucket-name/path/to/file.mp3
    parts = audio_url.replace('s3://', '').split('/', 1)
    bucket, key = parts[0], parts[1]
    
    response = s3_client.get_object(Bucket=bucket, Key=key)
    audio_bytes = response['Body'].read()
    
    # Xác định MIME type từ extension
    mime_types = {'.mp3': 'audio/mp3', '.wav': 'audio/wav', '.m4a': 'audio/m4a'}
    ext = '.' + key.split('.')[-1].lower()
    mime_type = mime_types.get(ext, 'audio/mp3')
    
    return audio_bytes, mime_type

def lambda_handler(event: Dict[str, Any], context: Any) -> Dict[str, Any]:
    """Đánh giá IELTS Speaking sử dụng Gemini native audio."""
    
    # Parse request
    request_data = parse_request(event)
    
    # Lấy API key từ Secrets Manager
    gemini_api_key = get_gemini_api_key()
    gemini_client = GeminiClient(api_key=gemini_api_key)
    
    # Trích xuất parameters
    audio_url = request_data.get('audio_url')
    part = request_data.get('part', 'PART_1')
    questions = request_data.get('questions', [])
    
    # Bước 1: Tải audio từ S3
    audio_bytes, mime_type = download_audio_from_s3(audio_url)
    logger.info(f"Đã tải {len(audio_bytes)} bytes, MIME: {mime_type}")
    
    # Bước 2: Gửi audio trực tiếp đến Gemini (MỘT lần gọi API)
    # Không cần AWS Transcribe - Gemini xử lý audio trực tiếp
    evaluation = gemini_client.evaluate_audio(
        audio_bytes=audio_bytes,
        part=part,
        questions=questions,
        mime_type=mime_type,
        max_retries=3,
        timeout=120
    )
    
    # Bước 3: Xây dựng response với tiêu chí IELTS Speaking
    result = {
        'session_id': request_data.get('session_id'),
        'transcript': evaluation.get('transcript'),
        'duration': evaluation.get('duration_seconds'),
        'overall_band': evaluation.get('overall_band'),
        'fluency_band': evaluation['fluency_coherence']['band'],
        'lexical_band': evaluation['lexical_resource']['band'],
        'grammar_band': evaluation['grammatical_range_accuracy']['band'],
        'pronunciation_band': evaluation['pronunciation']['band'],
        'model_used': 'gemini-2.5-flash-audio',
        'estimated_cost': evaluation['usage']['cost']
    }
    
    # Lưu vào DynamoDB
    save_evaluation(result, request_data.get('user_id'))
    
    return {'statusCode': 200, 'body': json.dumps(result)}
```

**So Sánh Chi Phí:**

| Phương Pháp | Chi Phí / 3-phút Audio | Độ Trễ |
|-------------|------------------------|--------|
| Gemini Native Audio | ~$0.021 | 30-45s |
| AWS Transcribe + LLM | ~$0.076 | 60-90s |
| **Tiết Kiệm** | **72%** | **Nhanh gấp 2x** |

---

### Lambda Function 3: Flashcard Generator (RAG)

Tạo flashcards từ tài liệu PDF sử dụng **RAG pipeline nhẹ với Titan Embeddings** (in-memory vector store, tối ưu cho Lambda package <50MB).

| Cài Đặt | Giá Trị |
|---------|---------|
| **Function name** | `bandup-flashcard-generator` |
| **Runtime** | Python 3.11 |
| **Memory** | 1024 MB |
| **Timeout** | 10 phút |
| **Trigger** | SQS (`bandup-flashcard-queue`) |
| **AI Model** | Gemini + Amazon Titan Embeddings V2 |

**Luồng RAG Pipeline:**

```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│  PDF Upload │ ──▶ │   Chunking   │ ──▶ │ Titan Embeddings│
│     (S3)    │     │ (3000 chars) │     │   (Bedrock)     │
└─────────────┘     └──────────────┘     └────────┬────────┘
                                                  │
                                                  ▼
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│  Flashcards │ ◀── │   Gemini     │ ◀── │ In-Memory Store │
│   (JSON)    │     │  Generation  │     │  (Cosine Sim)   │
└─────────────┘     └──────────────┘     └─────────────────┘
```

**Triển Khai Chính:**

```python
import json
import os
import boto3
import time
import google.generativeai as genai
from typing import Dict, Any, List
from concurrent.futures import ThreadPoolExecutor, as_completed

logger = logging.getLogger()

# Global instance cho warm starts (tối ưu Lambda)
_rag_instance = None
_s3_client = None

def get_s3_client():
    """Lấy cached S3 client."""
    global _s3_client
    if _s3_client is None:
        _s3_client = boto3.client('s3')
    return _s3_client

def get_rag_instance(api_key: str):
    """Lấy cached RAG instance cho warm starts."""
    global _rag_instance
    if _rag_instance is None:
        _rag_instance = RAG(
            api_key=api_key,
            chunk_size=int(os.environ.get('RAG_CHUNK_SIZE', '500')),
            chunk_overlap=int(os.environ.get('RAG_CHUNK_OVERLAP', '100'))
        )
        logger.info("Cold start: RAG instance created")
    else:
        logger.info("Warm start: Reusing RAG instance")
    return _rag_instance

def download_pdf_from_s3(bucket: str, key: str) -> str:
    """Tải PDF từ S3 về /tmp."""
    s3 = get_s3_client()
    local_path = f"/tmp/{key.split('/')[-1]}"
    s3.download_file(bucket, key, local_path)
    return local_path

def lambda_handler(event: Dict[str, Any], context: Any) -> Dict[str, Any]:
    """Tạo flashcard dựa trên RAG (nhẹ)."""
    
    start_time = time.time()
    is_async = is_sqs_event(event)
    
    # Parse request
    if is_async:
        request, job_id = parse_sqs_message(event)
        update_job_status(job_id, 'processing')
    else:
        request = json.loads(event.get('body', '{}')) if isinstance(event.get('body'), str) else event
    
    # Lấy S3 location
    pdf_url = request.get('pdf_url')
    s3_bucket, s3_key = parse_s3_url(pdf_url)
    
    # Lấy API key từ Secrets Manager
    secret_arn = os.environ.get('GEMINI_API_KEY_SECRET_ARN')
    secrets_client = boto3.client('secretsmanager')
    api_key = secrets_client.get_secret_value(SecretId=secret_arn)['SecretString']
    
    # Lấy parameters
    num_cards = int(request.get('num_cards', 10))
    difficulty = request.get('difficulty', 'MEDIUM')
    question_types = request.get('question_types', ['DEFINITION', 'VOCABULARY', 'COMPREHENSION'])
    
    # Bước 1: Tải PDF từ S3
    local_pdf = download_pdf_from_s3(s3_bucket, s3_key)
    
    # Bước 2: Index document với RAG (Titan Embeddings + in-memory store)
    rag = get_rag_instance(api_key)
    rag._vector_store = None  # Reset cho document mới
    rag._chunks = []
    
    index_result = rag.index_document(local_pdf, document_id=s3_key)
    logger.info(f"Đã index {index_result['chunk_count']} chunks từ {index_result['page_count']} trang")
    
    # Bước 3: Truy xuất chunks liên quan (hybrid approach)
    if index_result['chunk_count'] <= 15:
        # Document nhỏ: dùng representative chunks
        chunks = rag.get_representative_chunks(num_chunks=min(10, index_result['chunk_count']))
        retrieval_method = "representative"
    else:
        # Document lớn: dùng smart keyword-based queries
        chunks = rag.retrieve_with_smart_queries(top_k_per_query=3)
        retrieval_method = "smart_queries"
    
    # Bước 4: Tạo flashcards với Gemini
    prompt = generate_flashcards_prompt(chunks, num_cards, difficulty, question_types)
    flashcard_result = call_gemini(prompt, api_key)
    
    # Dọn dẹp
    os.remove(local_pdf)
    
    # Xây dựng response
    total_time = time.time() - start_time
    response_body = {
        'status': 'success',
        'set_id': request.get('set_id'),
        'user_id': request.get('user_id'),
        'document': {
            's3_bucket': s3_bucket,
            's3_key': s3_key,
            'page_count': index_result['page_count'],
            'chunk_count': index_result['chunk_count']
        },
        'retrieval': {
            'method': retrieval_method,
            'chunks_used': len(chunks),
            'keywords': index_result.get('keywords', [])[:5]
        },
        'flashcards': flashcard_result.get('flashcards', []),
        'total_cards': len(flashcard_result.get('flashcards', [])),
        'metrics': {
            'total_time_ms': round(total_time * 1000)
        }
    }
    
    # Lưu vào DynamoDB (bảng bandup-flashcard-sets)
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table(os.environ.get('DYNAMODB_FLASHCARD_SETS'))
    table.put_item(Item={
        'set_id': request.get('set_id'),
        'user_id': request.get('user_id'),
        'document_id': s3_key,
        'status': 'completed',
        'flashcards': json.dumps(response_body['flashcards']),
        'total_cards': response_body['total_cards'],
        'page_count': index_result['page_count'],
        'chunk_count': index_result['chunk_count'],
        'created_at': int(time.time())
    })
    
    if is_async:
        return {'statusCode': 200, 'body': 'OK'}
    
    return create_response(200, response_body)
```

**Titan Embeddings với Xử Lý Song Song:**

```python
class TitanEmbeddings:
    """Amazon Titan Text Embeddings V2 qua Bedrock với xử lý song song."""
    
    MODEL_ID = "amazon.titan-embed-text-v2:0"
    
    def __init__(self, region: str = None):
        self.region = region or os.environ.get('BEDROCK_REGION', 'us-east-1')
        self._client = None
    
    @property
    def client(self):
        if self._client is None:
            self._client = boto3.client('bedrock-runtime', region_name=self.region)
        return self._client
    
    def embed(self, text: str) -> List[float]:
        """Lấy embedding cho single text sử dụng Titan V2."""
        response = self.client.invoke_model(
            modelId=self.MODEL_ID,
            body=json.dumps({
                "inputText": text[:8000],  # Độ dài input tối đa
                "dimensions": 512,
                "normalize": True
            }),
            contentType="application/json",
            accept="application/json"
        )
        result = json.loads(response['body'].read())
        return result['embedding']
    
    def embed_batch_parallel(self, texts: List[str], max_workers: int = 10) -> List[List[float]]:
        """Embed nhiều texts SONG SONG sử dụng ThreadPoolExecutor."""
        embeddings = [None] * len(texts)
        
        with ThreadPoolExecutor(max_workers=max_workers) as executor:
            futures = {executor.submit(self.embed, t): i for i, t in enumerate(texts)}
            for future in as_completed(futures):
                idx = futures[future]
                embeddings[idx] = future.result()
        
        return embeddings
```

**RAG Pipeline (In-Memory):**

```python
import math
import fitz  # PyMuPDF

class RAG:
    """RAG nhẹ sử dụng Titan Embeddings + in-memory cosine similarity."""
    
    def __init__(self, api_key: str, chunk_size: int = 3000, chunk_overlap: int = 300):
        self.api_key = api_key
        self.chunk_size = chunk_size
        self.chunk_overlap = chunk_overlap
        self._chunks = []
        self._embeddings = []
        self._titan = TitanEmbeddings()
        self._keywords = []
    
    def index_document(self, pdf_path: str, document_id: str = None) -> Dict:
        """Index PDF với Titan V2 embeddings (xử lý song song)."""
        # Tải các trang PDF
        pages = []
        with fitz.open(pdf_path) as doc:
            for page_num, page in enumerate(doc):
                text = page.get_text()
                if text.strip():
                    pages.append({'content': text, 'page': page_num + 1})
        
        # Chunk text với overlap
        self._chunks = []
        for page in pages:
            chunks = self._chunk_text(page['content'])
            for chunk in chunks:
                self._chunks.append({
                    'text': chunk,
                    'page': page['page']
                })
        
        # Trích xuất keywords cho smart query generation
        all_text = " ".join([c['text'] for c in self._chunks])
        self._keywords = self._extract_keywords(all_text, top_n=20)
        
        # Tạo embeddings song song (10 Bedrock calls đồng thời)
        texts = [c['text'] for c in self._chunks]
        self._embeddings = self._titan.embed_batch_parallel(texts, max_workers=10)
        
        return {
            'page_count': len(pages),
            'chunk_count': len(self._chunks),
            'keywords': self._keywords[:10]
        }
    
    def _cosine_similarity(self, a: List[float], b: List[float]) -> float:
        """Tính cosine similarity giữa hai vectors."""
        dot_product = sum(x * y for x, y in zip(a, b))
        norm_a = math.sqrt(sum(x * x for x in a))
        norm_b = math.sqrt(sum(x * x for x in b))
        if norm_a == 0 or norm_b == 0:
            return 0.0
        return dot_product / (norm_a * norm_b)
    
    def similarity_search(self, query: str, top_k: int = 5) -> List[Dict]:
        """Tìm kiếm chunks tương tự sử dụng in-memory cosine similarity."""
        query_embedding = self._titan.embed(query)
        
        # Tính similarities
        similarities = []
        for i, embedding in enumerate(self._embeddings):
            score = self._cosine_similarity(query_embedding, embedding)
            similarities.append((i, score))
        
        # Sắp xếp theo similarity (giảm dần) và trả về top-k
        similarities.sort(key=lambda x: x[1], reverse=True)
        
        results = []
        for rank, (idx, score) in enumerate(similarities[:top_k]):
            chunk = self._chunks[idx]
            results.append({
                'text': chunk['text'],
                'page': chunk['page'],
                'score': score,
                'rank': rank + 1
            })
        return results
    
    def generate_smart_queries(self, num_queries: int = 5) -> List[str]:
        """Tạo queries theo document sử dụng keywords đã trích xuất."""
        kw = self._keywords
        queries = []
        
        if len(kw) >= 2:
            queries.append(f"definition and explanation of {kw[0]} and {kw[1]}")
        if len(kw) >= 4:
            queries.append(f"key concepts about {kw[2]} {kw[3]}")
        if len(kw) >= 6:
            queries.append(f"important information regarding {kw[4]} {kw[5]}")
        
        return queries[:num_queries]
    
    def retrieve_with_smart_queries(self, top_k_per_query: int = 3) -> List[Dict]:
        """Truy xuất chunks sử dụng nhiều smart queries cho coverage tốt hơn."""
        queries = self.generate_smart_queries()
        seen_texts = set()
        all_results = []
        
        for query in queries:
            results = self.similarity_search(query, top_k=top_k_per_query)
            for r in results:
                if r['text'] not in seen_texts:
                    seen_texts.add(r['text'])
                    all_results.append(r)
        
        return sorted(all_results, key=lambda x: x['score'], reverse=True)
    
    def get_representative_chunks(self, num_chunks: int = 10) -> List[Dict]:
        """Lấy chunks phân bố đều trong document."""
        if len(self._chunks) <= num_chunks:
            return [{'text': c['text'], 'page': c['page'], 'score': 1.0} 
                    for c in self._chunks]
        
        step = len(self._chunks) // num_chunks
        return [{'text': self._chunks[i * step]['text'], 
                 'page': self._chunks[i * step]['page'], 
                 'score': 1.0} 
                for i in range(num_chunks)]
```

**Prompt Tạo Flashcard:**

```python
def generate_flashcards_prompt(chunks: List[Dict], num_cards: int, difficulty: str, question_types: List[str]) -> str:
    """Xây dựng prompt cho Gemini tạo flashcard."""
    context = "\n\n".join([
        f"[Chunk {i+1}] (Page {c.get('page', '?')}):\n{c['text']}"
        for i, c in enumerate(chunks)
    ])
    
    return f"""Based on the following document excerpts, generate {num_cards} flashcards.

CONTEXT:
{context}

REQUIREMENTS:
- Difficulty: {difficulty}
- Generate exactly {num_cards} flashcards
- Each flashcard should have a clear question and concise answer
- Focus on key concepts, definitions, and important facts
- Use these question types: {", ".join(question_types)}

OUTPUT FORMAT (JSON):
{{
  "flashcards": [
    {{
      "question": "...",
      "answer": "...",
      "type": "DEFINITION",
      "difficulty": "{difficulty}",
      "source_chunk": 1
    }}
  ]
}}

Return ONLY valid JSON."""

def call_gemini(prompt: str, api_key: str) -> Dict:
    """Gọi Gemini API để tạo flashcard."""
    import google.generativeai as genai
    
    genai.configure(api_key=api_key)
    model = genai.GenerativeModel(
        model_name=os.environ.get('GEMINI_MODEL', 'gemini-2.0-flash'),
        generation_config={
            'temperature': 0.3,
            'max_output_tokens': 4096
        }
    )
    
    response = model.generate_content(prompt)
    text = response.text
    
    # Trích xuất JSON nếu được wrap trong markdown
    if '```json' in text:
        text = text.split('```json')[1].split('```')[0]
    
    return json.loads(text.strip())
```

---

### Lambda Function 4: S3 Upload Handler

Tạo **presigned URLs** để upload file an toàn lên S3.

| Cài Đặt | Giá Trị |
|---------|---------|
| **Function name** | `bandup-s3-upload` |
| **Runtime** | Python 3.11 |
| **Memory** | 256 MB |
| **Timeout** | 30 giây |
| **Trigger** | API Gateway (sync) |

**Triển Khai Chính:**

```python
import json
import os
import boto3
from datetime import datetime
from typing import Dict, Any

s3_client = boto3.client('s3')

def lambda_handler(event: Dict[str, Any], context: Any) -> Dict:
    """Tạo presigned URL cho S3 upload."""
    
    request = json.loads(event.get('body', '{}'))
    
    user_id = request.get('user_id')
    filename = request.get('filename')
    content_type = request.get('content_type', 'application/octet-stream')
    upload_type = request.get('upload_type', 'general')
    
    # Xác định bucket dựa trên upload type
    bucket_map = {
        'speaking_audio': os.environ.get('S3_BUCKET_AUDIO'),
        'flashcard_pdf': os.environ.get('S3_BUCKET_DOCUMENTS'),
        'writing_essay': os.environ.get('S3_BUCKET_DOCUMENTS'),
    }
    bucket = bucket_map.get(upload_type)
    
    # Tạo S3 key có tổ chức
    timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
    key = f"uploads/{upload_type}/{user_id}/{timestamp}_{filename}"
    
    # Tạo presigned PUT URL (hết hạn sau 15 phút)
    upload_url = s3_client.generate_presigned_url(
        'put_object',
        Params={'Bucket': bucket, 'Key': key, 'ContentType': content_type},
        ExpiresIn=900
    )
    
    # Tạo presigned GET URL (hết hạn sau 1 giờ)
    get_url = s3_client.generate_presigned_url(
        'get_object',
        Params={'Bucket': bucket, 'Key': key},
        ExpiresIn=3600
    )
    
    return {
        'statusCode': 200,
        'headers': {'Content-Type': 'application/json'},
        'body': json.dumps({
            'upload_url': upload_url,
            'get_url': get_url,
            'file_url': f"s3://{bucket}/{key}",
            'expires_in': 900
        })
    }
```

---

### Quản Lý Secrets An Toàn

Tất cả Lambda functions sử dụng **AWS Secrets Manager** để lấy API keys:

```python
# secrets_helper.py (trong Lambda Layer)
import boto3
import os
from functools import lru_cache

@lru_cache(maxsize=1)
def get_gemini_api_key() -> str:
    """Lấy Gemini API key từ Secrets Manager (có cache)."""
    client = boto3.client('secretsmanager')
    secret_arn = os.environ.get('GEMINI_API_KEY_SECRET_ARN')
    
    response = client.get_secret_value(SecretId=secret_arn)
    return response['SecretString']
```

{{% notice warning %}}
**Best Practices Bảo Mật:**
- Không bao giờ hardcode API keys trong Lambda code
- Sử dụng AWS Secrets Manager cho tất cả credentials nhạy cảm
- Rotate secrets định kỳ sử dụng automatic rotation
- Sử dụng IAM roles với least-privilege permissions
{{% /notice %}}

---

### IAM Role cho Lambda Functions

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "BedrockAccess",
      "Effect": "Allow",
      "Action": ["bedrock:InvokeModel"],
      "Resource": "arn:aws:bedrock:*:*:foundation-model/amazon.titan-embed-text-v2*"
    },
    {
      "Sid": "DynamoDBAccess",
      "Effect": "Allow",
      "Action": ["dynamodb:PutItem", "dynamodb:GetItem", "dynamodb:Query"],
      "Resource": [
        "arn:aws:dynamodb:*:*:table/bandup-evaluations",
        "arn:aws:dynamodb:*:*:table/bandup-flashcard-sets"
      ]
    },
    {
      "Sid": "S3Access",
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject"],
      "Resource": "arn:aws:s3:::bandup-*/*"
    },
    {
      "Sid": "SQSAccess",
      "Effect": "Allow",
      "Action": ["sqs:ReceiveMessage", "sqs:DeleteMessage"],
      "Resource": "arn:aws:sqs:*:*:bandup-*-queue"
    },
    {
      "Sid": "SecretsAccess",
      "Effect": "Allow",
      "Action": ["secretsmanager:GetSecretValue"],
      "Resource": "arn:aws:secretsmanager:*:*:secret:bandup/*"
    },
    {
      "Sid": "CloudWatchLogs",
      "Effect": "Allow",
      "Action": ["logs:CreateLogStream", "logs:PutLogEvents"],
      "Resource": "arn:aws:logs:*:*:log-group:/aws/lambda/bandup-*"
    }
  ]
}
```

---

### Bảng DynamoDB

Các Lambda functions lưu kết quả vào **hai bảng DynamoDB**:

| Bảng | Sử Dụng Bởi | Mục Đích |
|------|-------------|----------|
| `bandup-evaluations` | Writing + Speaking Evaluators | Lưu điểm band IELTS, feedback, transcripts |
| `bandup-flashcard-sets` | Flashcard Generator | Lưu flashcards và document metadata |

**Schema Bảng Evaluations (Writing & Speaking):**

```python
# Sử dụng bởi Writing Evaluator
table.put_item(Item={
    'evaluation_id': session_id,      # Partition Key
    'user_id': user_id,               # Sort Key
    'evaluation_type': 'writing',     # 'writing' hoặc 'speaking'
    'status': 'completed',
    'overall_band': '7.0',
    'task_achievement_band': '7.0',   # Chỉ Writing
    'fluency_band': '6.5',            # Chỉ Speaking
    'pronunciation_band': '7.0',      # Chỉ Speaking
    'transcript': '...',              # Chỉ Speaking
    'feedback': json.dumps(feedback),
    'created_at': timestamp
})
```

**Schema Bảng Flashcard Sets:**

```python
# Sử dụng bởi Flashcard Generator
table.put_item(Item={
    'set_id': set_id,                 # Partition Key
    'user_id': user_id,               # Sort Key
    'document_id': document_id,
    'status': 'completed',
    'flashcards': json.dumps(flashcards),
    'total_cards': 10,
    'page_count': 5,
    'chunk_count': 12,
    'created_at': timestamp
})
```

---

### Biến Môi Trường

| Biến | Mô Tả | Ví Dụ |
|------|-------|-------|
| `GEMINI_API_KEY_SECRET_ARN` | Secrets Manager ARN | `arn:aws:secretsmanager:...:secret:bandup/gemini-api-key` |
| `DYNAMODB_EVALUATIONS` | Bảng evaluations (Writing + Speaking) | `bandup-evaluations` |
| `DYNAMODB_FLASHCARD_SETS` | Bảng flashcard sets | `bandup-flashcard-sets` |
| `S3_BUCKET_AUDIO` | Audio bucket | `bandup-audio-bucket` |
| `S3_BUCKET_DOCUMENTS` | Documents bucket | `bandup-documents-bucket` |
| `BEDROCK_REGION` | Bedrock region cho Titan | `us-east-1` |
| `RAG_CHUNK_SIZE` | Chunk size cho RAG | `3000` |
| `RAG_CHUNK_OVERLAP` | Chunk overlap | `300` |
| `GEMINI_MODEL` | Tên Gemini model | `gemini-2.0-flash` |

---

### Deploy Lambda Functions

```bash
# Đóng gói với dependencies
cd rag_flashcard
pip install -r requirements.txt -t package/
cp lambda_handler.py rag_pipeline.py package/
cd package && zip -r ../function.zip . && cd ..

# Tạo Lambda function
aws lambda create-function \
    --function-name bandup-flashcard-generator \
    --runtime python3.11 \
    --handler lambda_handler.lambda_handler \
    --role arn:aws:iam::${AWS_ACCOUNT_ID}:role/bandup-lambda-role \
    --timeout 600 \
    --memory-size 1024 \
    --zip-file fileb://function.zip \
    --environment Variables="{
        GEMINI_API_KEY_SECRET_ARN=arn:aws:secretsmanager:${AWS_REGION}:${AWS_ACCOUNT_ID}:secret:bandup/gemini-api-key,
        BEDROCK_REGION=us-east-1,
        RAG_CHUNK_SIZE=3000
    }"

# Thêm SQS trigger
aws lambda create-event-source-mapping \
    --function-name bandup-flashcard-generator \
    --event-source-arn arn:aws:sqs:${AWS_REGION}:${AWS_ACCOUNT_ID}:bandup-flashcard-queue \
    --batch-size 1
```


---

#### Bước Tiếp Theo

Tiến hành đến [DynamoDB](../5.7.4-DynamoDB/) để cấu hình các bảng database.
