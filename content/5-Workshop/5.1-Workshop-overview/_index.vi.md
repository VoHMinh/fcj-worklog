---
title: "Tổng Quan Workshop"
date:
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Giới Thiệu Dự Án

**Hệ Thống Tự Học IELTS** là một nền tảng trực tuyến toàn diện được thiết kế để hỗ trợ sinh viên trong hành trình chuẩn bị IELTS tự học. Workshop này tập trung vào việc triển khai toàn bộ hạ tầng AWS cần thiết để chạy ứng dụng này trong môi trường production.

#### Các Thành Phần Hệ Thống

Nền tảng bao gồm năm module chức năng chính:

| Module | Mô Tả |
|--------|-------------|
| **Quản Lý Người Dùng** | Xác thực đa cấp (Guest, Member, Premium, Admin), OAuth integration |
| **Nền Tảng Blog** | Nội dung giáo dục do cộng đồng đóng góp với CRUD, filtering, comments |
| **Phòng Học** | Không gian ảo với WebRTC video/voice, Pomodoro timer, screen sharing |
| **Bài Thi Thử** | Bài thi IELTS mô phỏng với AI đánh giá Speaking/Writing |
| **Hệ Thống Flashcard** | Flashcards thông minh với AI generation dựa trên RAG |

#### Tổng Quan Kiến Trúc

Kiến trúc tuân theo các nguyên tắc AWS Well-Architected Framework:

**1. Lớp Mạng (VPC)**
- VPC tùy chỉnh trải rộng trên hai Availability Zones (AZ-1 và AZ-2)
- Public subnets cho ALB, NAT Gateways
- Private subnets cho ECS tasks, RDS, ElastiCache
- Security Groups và Network ACLs cho defense in depth

**2. Lớp Ứng Dụng (ECS)**
- **Frontend**: Ứng dụng Next.js 14+ được containerized
- **Backend**: Spring Boot 3.x monolithic REST API
- Mô hình triển khai active-passive cho high availability
- Auto Scaling dựa trên CPU/memory utilization

**3. Lớp Dữ Liệu**
- **Amazon RDS PostgreSQL**: Database chính với Multi-AZ standby
- **Amazon ElastiCache (Redis)**: Session management và caching
- **Amazon S3**: Media storage với CloudFront CDN
- **Amazon DynamoDB**: Lưu trữ kết quả AI assessment

**4. Lớp Dịch Vụ AI (Serverless)**
- **Amazon API Gateway**: Entry point cho AI requests
- **Amazon SQS**: Message queue cho xử lý bất đồng bộ
- **AWS Lambda**: Ba functions chuyên biệt:
  - Writing Assessment (đánh giá grammar, vocabulary, coherence)
  - Speaking Assessment (transcription + evaluation)
  - Flashcard Generation (RAG-based với Titan Embeddings)
- **Amazon Bedrock**: Gemma 3 12B model và Titan Embeddings
- **Google Gemini API**: Smart query generation

![Sơ Đồ Kiến Trúc](/images/2-Proposal/AWS-Bandup-Architecture.png)

#### Luồng Traffic

Luồng được đánh số sau tương ứng với sơ đồ kiến trúc:

| Bước | Thành Phần | Hành Động |
|------|-----------|--------|
| **1** | User | Truy cập `https://bandup.bughunters.site` |
| **2** | Route 53 | DNS resolution với health checks |
| **2** | ACM | SSL/TLS certificate validation |
| **3** | AWS WAF | Web Application Firewall inspection |
| **3** | ALB | Application Load Balancer nhận traffic |
| **4** | Traffic Division | ALB route đến active AZ (AZ-1) |
| **5** | ECS Services | Frontend và Backend containers xử lý requests |
| **6** | Service Connect | Internal service discovery giữa FE và BE |
| **7** | ElastiCache | Redis caching layer |
| **8** | RDS Primary | PostgreSQL database queries |
| **9** | API Gateway | AI service requests entry point |
| **10** | Queue | SQS nhận async AI processing requests |
| **11** | SQS Queues | Ba queues riêng cho Writing, Speaking, Flashcard |
| **12** | Lambda Functions | Xử lý Writing/Speaking evaluation, Flashcard generation |
| **13** | Secrets Manager | Secure credential retrieval |
| **14** | Amazon Bedrock | AI model inference (Gemma 3 12B) |
| **15** | DynamoDB | Lưu AI results |
| **16** | CloudWatch | Logs và metrics collection |

#### Chi Tiết Kiến Trúc Dịch Vụ AI

Dịch vụ AI triển khai theo mô hình fully serverless:

```
User Request → API Gateway → SQS Queue → Lambda Function → AI Model → DynamoDB → Response
```

**Ba Lambda Functions:**

1. **Writing Evaluate**: Phân tích bài viết IELTS
   - Đánh giá grammar và vocabulary
   - Task achievement scoring
   - Coherence và cohesion evaluation
   - Band score prediction (0-9)

2. **Speaking Evaluate**: Xử lý audio recordings
   - Speech-to-text transcription
   - Pronunciation analysis
   - Fluency và coherence scoring
   - Lexical resource evaluation

3. **Flashcard Generate**: RAG-based intelligent flashcard creation
   - Document chunking và embedding (Titan V2)
   - Smart query generation (Google Gemini)
   - Context-aware flashcard generation
   - Storage trong DynamoDB

#### CI/CD Pipeline

Pipeline triển khai tự động hóa toàn bộ quy trình release:

```
Developer → GitLab/GitHub → CodePipeline → CodeBuild → ECR → ECS
```

1. Developer tạo tag mới/push code
2. GitLab Webhook trigger AWS CodePipeline
3. CodeBuild compile code và run tests
4. Docker images được build và push lên ECR
5. ECS service cập nhật với task definitions mới
6. Rolling deployment đảm bảo zero-downtime updates

#### Chiến Lược Tối Ưu Chi Phí

Kiến trúc này được thiết kế để tiết kiệm chi phí:

- **ECS Fargate**: Chỉ trả tiền cho containers đang chạy
- **Active-Passive Multi-AZ**: Standby resources tiêu thụ chi phí tối thiểu
- **Lambda**: Pay-per-invocation cho AI processing
- **DynamoDB On-Demand**: Scales với actual usage
- **S3 Lifecycle Policies**: Automatic tiering sang storage rẻ hơn
- **Reserved Capacity**: Available cho predictable workloads

#### Mục Tiêu Học Tập Workshop

Sau khi hoàn thành workshop này, bạn sẽ học được:

1. **Network Design**: Cách tạo secure VPC với public/private subnets
2. **Container Orchestration**: Triển khai ứng dụng trên ECS Fargate
3. **Database Management**: Thiết lập RDS Multi-AZ và ElastiCache
4. **Serverless Architecture**: Xây dựng event-driven AI processing pipelines
5. **CI/CD Best Practices**: Automated deployments với CodePipeline
6. **Security Implementation**: IAM roles, Secrets Manager, WAF configuration
7. **Observability**: CloudWatch monitoring và alerting

#### Bước Tiếp Theo

Tiến hành đến [Prerequisites](../5.2-Prerequisites/) để thiết lập môi trường trước khi bắt đầu triển khai thực hành.

