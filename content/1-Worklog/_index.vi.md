---
title: "Nhật ký công việc"
date: 2025-08-11
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

Trang này ghi lại toàn bộ **Nhật ký công việc (Worklog)** được thực hiện trong suốt chương trình thực tập **First Cloud Journey (FCJ)** tại **AWS**. Đây là tài liệu chi tiết hóa quá trình học tập, triển khai dự án **Bandup IELTS**, khắc phục lỗi hệ thống và tham gia các sự kiện chuyên môn trong vòng **12 tuần** (khoảng 3 tháng).

Trong 12 tuần này, tôi đã chuyển đổi từ việc làm quen với các khái niệm Cloud cơ bản sang việc xây dựng và tối ưu hóa một ứng dụng Serverless tích hợp AI hoàn chỉnh trên AWS, hoàn thành **hơn 50 khóa học AWS Skill Builder** trong quá trình này.

### Tóm tắt công việc theo tuần:

| Tuần | Công việc trọng tâm |
| :--- | :--- |
| **Tuần 1** | [Làm quen FCJ, tạo tài khoản AWS, và thiết lập môi trường mạng cơ bản (**VPC, Subnets, Internet Gateway**).](1.1-week1/) |
| **Tuần 2** | [Nắm vững **Amazon EC2 và VPC**, hoàn thành các khóa AWS Skill Builder về **IAM, Budgets, EC2**, và tham gia **sự kiện Cloud Day** để học hỏi về AI/Data.](1.2-week2/) |
| **Tuần 3** | [Khắc phục sự cố tài khoản AWS, cấu hình **Hybrid DNS với Route 53 Resolver** và **VPC Peering**, học **CloudFormation** và **Cloud9** cho phát triển IaC.](1.3-week3/) |
| **Tuần 4** | [Thành thạo **AWS Transit Gateway** cho quản lý mạng tập trung, nghiên cứu sâu **EC2 Auto Scaling**, **Lightsail**, và các **dịch vụ Migration** (DMS, VM Import/Export).](1.4-week4/) |
| **Tuần 5** | [Phân tích và tối ưu chi phí AWS, thiết kế **kiến trúc hạ tầng Serverless**, học **RDS, DynamoDB, ElastiCache**, và thiết lập **AWS Toolkit cho VS Code**.](1.5-week5/) |
| **Tuần 6** | [Thành thạo **dịch vụ Storage AWS** (S3, Glacier, Storage Gateway), nâng cao kỹ năng Python, hoàn thiện kiến trúc dự án, và tham gia webinar về **DevSecOps** và **Amazon Q Developer**.](1.6-week6/) |
| **Tuần 7** | [**Ôn tập toàn diện** và củng cố kiến thức các dịch vụ AWS cơ bản (Compute, Storage, Networking, Database, Security) để chuẩn bị cho **kỳ thi giữa kỳ**.](1.7-week7/) |
| **Tuần 8** | [**Hoàn thành thi giữa kỳ**, bắt đầu triển khai các chức năng **CRUD** nền tảng, nghiên cứu **kiến trúc serverless** (Lambda, API Gateway, DynamoDB), và thiết lập môi trường phát triển.](1.8-week8/) |
| **Tuần 9** | [**Chuyển đổi sang AWS SAM**, tái cấu trúc các chức năng CRUD, tích hợp **Docker** cho môi trường build, và **triển khai thành công** dự án lên AWS vượt qua các thách thức gỡ lỗi Local.](1.9-week9/) |
| **Tuần 10** | [**Gỡ lỗi CORS** và template validation errors, tích hợp **Frontend/Backend**, hoàn thành chức năng **Read/Delete**, giải quyết vấn đề **xác thực Cognito**, và tham gia **AWS Cloud Mastery Series #1**.](1.10-week10/) |
| **Tuần 11** | [Triển khai kiến trúc **Multi-Stack** để tối ưu hóa, **khắc phục triệt để lỗi CORS**, và bắt đầu tích hợp **AI Services** (Lambda, Bedrock).](1.11-week11/) |
| **Tuần 12** | [Hoàn thiện **Lambda functions tích hợp AI**, tích hợp **Gemini API** cho đánh giá IELTS, hoàn thành **RAG pipeline** cho tạo flashcard, và tham gia **AWS Cloud Mastery Series cuối cùng**.](1.12-week12/) |

---

### Lộ trình học AWS Skill Builder (Tuần 2-5)

| Danh mục | Các khóa học đã hoàn thành |
|----------|---------------------------|
| **Networking** | VPC, Route 53, VPC Peering, Transit Gateway, Networking Workshop |
| **Compute** | EC2, EC2 Auto Scaling, Lightsail, Lightsail Containers |
| **Security** | IAM, IAM Roles cho EC2 |
| **Database** | RDS, DynamoDB, ElastiCache |
| **Migration** | VM Import/Export, DMS, SCT, Elastic Disaster Recovery |
| **DevOps** | CloudFormation, Cloud9, AWS CLI, AWS Toolkit cho VS Code |
| **Quản lý chi phí** | AWS Budgets, Cost Explorer, Service Quotas, Right-Sizing |
| **Architecture** | Building Highly Available Web Applications |

### Lộ trình học AWS Skill Builder (Tuần 6-10)

| Danh mục | Các khóa học đã hoàn thành |
|----------|---------------------------|
| **Storage** | Static Website Hosting với S3, AWS Backup, CloudFront |
| **Reliability** | Data Protection với AWS Backup |
| **Development** | AWS Toolkit cho VS Code, Serverless patterns |

---

### Tiến trình Học tập

**Tuần 1-5: Nền tảng & Khám phá**
- Dịch vụ AWS cơ bản (EC2, S3, VPC, IAM)
- Networking fundamentals (VPC, Route 53, Transit Gateway)
- Tối ưu chi phí và thiết kế kiến trúc
- Infrastructure as Code (CloudFormation, Cloud9)

**Tuần 6-7: Củng cố & Đánh giá**
- Thành thạo dịch vụ Storage (S3, Glacier, Storage Gateway)
- Chiến lược disaster recovery và backup
- Ôn tập toàn diện cho kỳ thi
- Hoàn thành thi giữa kỳ

**Tuần 8-10: Triển khai & Deployment**
- Triển khai kiến trúc Serverless (Lambda, API Gateway, DynamoDB)
- Áp dụng framework AWS SAM
- Tích hợp Docker cho builds nhất quán
- Tích hợp Frontend-Backend
- Production deployment và debugging

**Tuần 11-12: Tính năng Nâng cao & Tích hợp AI**
- Tối ưu hóa kiến trúc Multi-stack
- Tích hợp dịch vụ AI (Bedrock, Gemini API)
- Triển khai RAG pipeline
- Hoàn thiện dự án cuối cùng
