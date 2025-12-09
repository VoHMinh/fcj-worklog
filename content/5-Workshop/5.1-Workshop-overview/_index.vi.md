---
title: "Giới thiệu"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

### 1. Kiến trúc tổng quan

Nền tảng **IELTS BandUp** được xây dựng trên một kiến trúc mạnh mẽ, có tính sẵn sàng cao (High Availability) trên AWS. Hệ thống được thiết kế để xử lý lưu lượng người dùng một cách an toàn, đồng thời đảm bảo độ trễ thấp khi truy cập tài liệu học tập và các tính năng AI.

### 2. Các dịch vụ AWS cốt lõi

Để đạt được các mục tiêu về khả năng mở rộng, bảo mật và hiệu năng, chúng tôi sử dụng các nhóm dịch vụ chính sau:

#### Mạng & Phân phối nội dung (Networking)

-   **Amazon VPC (Virtual Private Cloud):** Lớp mạng nền tảng. Chúng tôi sử dụng VPC tùy chỉnh với các **Public Subnet** và **Private Subnet** riêng biệt để kiểm soát chặt chẽ luồng truy cập.
-   **NAT Gateway:** Cho phép các tài nguyên trong Private Subnet (như Backend) kết nối ra Internet (để tải thư viện, gọi API ngoài) mà không để lộ IP ra môi trường Public.
-   **Application Load Balancer (ALB):** Phân phối lưu lượng truy cập ứng dụng đến các container trên nhiều Availability Zones (AZ), đảm bảo khả năng chịu lỗi của hệ thống.
-   **Amazon Route 53:** Dịch vụ DNS giúp định tuyến tên miền và quản lý lưu lượng truy cập người dùng.

#### Tính toán & Container (Compute)

-   **Amazon ECS (Elastic Container Service) với Fargate:** Công cụ điều phối container serverless. Chúng tôi sử dụng Fargate để vận hành cả **Next.js Frontend** và **Spring Boot Backend**, giúp loại bỏ gánh nặng quản lý máy chủ vật lý (EC2).
-   **Amazon ECR (Elastic Container Registry):** Kho lưu trữ container được quản lý hoàn toàn, nơi chứa các Docker image của ứng dụng trước khi deploy.

#### Cơ sở dữ liệu & Lưu trữ (Database & Storage)

-   **Amazon RDS (Relational Database Service):** Sử dụng PostgreSQL với mô hình **Multi-AZ** (Primary và Standby) để đảm bảo an toàn dữ liệu và khả năng khôi phục sau thảm họa.
-   **Amazon ElastiCache (Redis):** Đóng vai trò bộ nhớ đệm (cache) trong bộ nhớ, giúp tăng tốc độ truy vấn và quản lý phiên đăng nhập (session) của người dùng.
-   **Amazon S3 (Simple Storage Service):** Lưu trữ tài nguyên tĩnh, file media (file nghe) và dữ liệu người dùng tải lên với độ bền cao.

#### AI & Tích hợp Serverless

Để vận hành các tính năng thông minh của BandUp (Chấm điểm Writing/Speaking, Tạo Flashcard), chúng tôi áp dụng kiến trúc Serverless:

-   **Amazon Bedrock & Google Gemini API:** Các mô hình Generative AI cốt lõi dùng để phân tích bài làm và đưa ra phản hồi cá nhân hóa.
-   **AWS Lambda:** Các hàm tính toán serverless đóng vai trò điều phối quy trình AI, kết nối ứng dụng với các mô hình ngôn ngữ lớn.
-   **Amazon SQS (Simple Queue Service):** Hàng đợi thông điệp giúp phân tách (decouple) backend và lớp xử lý AI, cho phép xử lý bất đồng bộ và tránh quá tải hệ thống.
-   **Amazon API Gateway:** Cổng giao tiếp bảo mật (RESTful API) để gọi các dịch vụ AI từ ứng dụng chính.

#### DevOps & CI/CD

-   **AWS CodePipeline:** Tự động hóa quy trình phát hành phần mềm, đảm bảo cập nhật nhanh chóng và tin cậy.
-   **AWS CodeBuild:** Biên dịch mã nguồn, chạy kiểm thử và đóng gói phần mềm (Docker images) sẵn sàng cho việc triển khai.

#### Bảo mật (Security)

-   **AWS WAF (Web Application Firewall):** Bảo vệ ứng dụng web khỏi các lỗ hổng bảo mật phổ biến.
-   **AWS Secrets Manager:** Lưu trữ và quản lý an toàn các thông tin nhạy cảm (mật khẩu database, API keys) trong suốt vòng đời ứng dụng.

![Sơ đồ kiến trúc](/images/2-Proposal/AWS-Bandup-Architecture.png)
