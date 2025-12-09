---
title: "Workshop"
date:
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Hệ Thống Tự Học IELTS - Workshop Hạ Tầng AWS

#### Tổng Quan

Workshop toàn diện này hướng dẫn bạn xây dựng **hạ tầng AWS sẵn sàng cho production** cho Hệ Thống Tự Học IELTS. Bạn sẽ học cách triển khai ứng dụng web có tính sẵn sàng cao, khả năng mở rộng và bảo mật sử dụng các dịch vụ AWS hiện đại và các phương pháp tốt nhất.

Kiến trúc triển khai theo mô hình **active-passive Multi-AZ** trên Amazon ECS, với lớp dịch vụ AI serverless cho việc đánh giá thông minh và tạo nội dung.

![Tổng Quan Kiến Trúc](/images/2-Proposal/AWS-Bandup-Architecture.png)

#### Những Gì Bạn Sẽ Xây Dựng

Sau khi hoàn thành workshop này, bạn sẽ triển khai được:

| Thành Phần | Dịch Vụ AWS | Mục Đích |
|-----------|-------------|---------|
| **Lớp Mạng** | VPC, Subnets, NAT Gateway | Hạ tầng mạng cô lập, bảo mật |
| **Nền Tảng Container** | ECS Fargate, ECR | Điều phối container serverless |
| **Cân Bằng Tải** | ALB, Route 53, ACM | Phân phối lưu lượng và SSL termination |
| **Lớp Dữ Liệu** | RDS PostgreSQL, ElastiCache, S3 | CSDL quan hệ, caching, lưu trữ object |
| **Dịch Vụ AI** | API Gateway, SQS, Lambda, DynamoDB | Pipeline xử lý AI serverless |
| **CI/CD** | CodePipeline, CodeBuild | Pipeline triển khai tự động |
| **Bảo Mật** | IAM, Secrets Manager, WAF | Quản lý danh tính và bảo vệ |
| **Giám Sát** | CloudWatch Logs, Alarms | Quan sát và cảnh báo |

#### Điểm Nổi Bật Kiến Trúc

**Thiết Kế Tính Sẵn Sàng Cao:**
- Triển khai Multi-AZ trên hai Availability Zones
- Failover active-passive cho ECS services
- RDS Multi-AZ với failover tự động
- Application Load Balancer với health checks

**Kiến Trúc AI Serverless:**
- API Gateway cho các RESTful AI endpoints
- SQS cho xử lý message bất đồng bộ
- Lambda functions cho Writing Assessment, Speaking Assessment, và RAG-based Flashcard Generation
- DynamoDB để lưu trữ kết quả AI
- Tích hợp Amazon Bedrock cho các AI models (Gemma 3 12B, Titan Embeddings)
- Google Gemini API cho smart query generation

**Phương Pháp Bảo Mật Tốt Nhất:**
- Private subnets cho application và database tiers
- Security groups với least-privilege access
- AWS WAF cho application-level protection
- Secrets Manager cho quản lý credentials
- IAM roles với quyền tối thiểu cần thiết

#### Điều Kiện Tiên Quyết

Trước khi bắt đầu workshop này, hãy đảm bảo bạn có:
- Tài khoản AWS với quyền phù hợp
- AWS CLI đã cài đặt và cấu hình
- Hiểu biết cơ bản về các dịch vụ AWS (VPC, EC2, ECS)
- Docker đã cài đặt locally cho container builds
- Git cho version control

#### Thời Gian Hoàn Thành

| Phần | Thời Gian Ước Tính |
|---------|---------------|
| Điều kiện tiên quyết | 15 phút |
| VPC & Network Setup | 30 phút |
| ECS & Container Setup | 45 phút |
| Load Balancer Configuration | 30 phút |
| Database & Storage Setup | 45 phút |
| AI Service Architecture | 60 phút |
| CI/CD Pipeline | 30 phút |
| Security & IAM | 30 phút |
| Monitoring Setup | 20 phút |
| **Tổng** | **~5 giờ** |

#### Nội Dung

1. [Tổng Quan Workshop](5.1-Workshop-overview/)
2. [Điều Kiện Tiên Quyết](5.2-Prerequisites/)
3. [VPC & Network Setup](5.3-VPC-Network/)
4. [ECS & Container Setup](5.4-ECS-Setup/)
5. [Load Balancer Configuration](5.5-Load-Balancer/)
6. [Database & Storage Setup](5.6-Database-Storage/)
7. [AI Service Architecture](5.7-AI-Service/)
8. [CI/CD Pipeline](5.8-CICD-Pipeline/)
9. [Security & IAM](5.9-Security-IAM/)
10. [Monitoring & Logging](5.10-Monitoring/)
11. [Clean Up](5.11-Cleanup/)

