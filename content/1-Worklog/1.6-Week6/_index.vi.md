---
title: "Worklog Tuần 6"
date: 2025-10-14
weight: 6
chapter: false
pre: "<b>1.6. </b>"
---

### Mục tiêu tuần 6

- Nắm vững các dịch vụ lưu trữ AWS cơ bản và use cases của chúng.
- Nâng cao kỹ năng lập trình Python thông qua các bài tập thực hành.
- Thiết kế và hoàn thiện kiến trúc hạ tầng dự án.
- Tham gia webinar "Reinventing DevSecOps with AWS Generative AI" để khám phá thực hành DevSecOps và Amazon Q Developer.

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|----------|--------------|-----------------|----------------|
| 2 | - Nghiên cứu Amazon S3 cơ bản: Kiến trúc Bucket, đảm bảo độ bền, và khả năng host static website.<br>- Khám phá S3 Storage Classes (Standard, Standard-IA) và Amazon Glacier cho giải pháp cold storage.<br>- Hoàn thành: **Static Website Hosting with Amazon S3**. | 14/10/2025 | 15/10/2025 | [AWS S3 Documentation](https://aws.amazon.com/s3/) |
| 3 | - Học các loại AWS Storage Gateway (File, Volume, Tape Gateway) và patterns tích hợp.<br>- Hiểu Object Lifecycle Management policies để tối ưu chi phí.<br>- Thực hành Python cơ bản: data structures, functions, và error handling.<br>- **Tham gia webinar:** "Reinventing DevSecOps with AWS Generative AI" với sự góp mặt của anh Hoàng Kha. | 16/10/2025 | 17/10/2025 | [AWS Storage Gateway](https://aws.amazon.com/storagegateway/)<br>[AWS Events](https://aws.amazon.com/events/) |
| 4 | - Nghiên cứu khái niệm disaster recovery: RTO, RPO, và các chiến lược Backup & Restore.<br>- Khám phá dịch vụ AWS Backup cho quản lý backup tập trung.<br>- Thực hành: Tạo S3 buckets, upload files, cấu hình static website hosting, và test lifecycle policies.<br>- Nghiên cứu phương pháp DevSecOps: CI/CD pipelines, SAST/DAST tools, Infrastructure as Code. | 18/10/2025 | 19/10/2025 | [AWS Backup](https://aws.amazon.com/backup/) |
| 5 | - Hoàn thiện sơ đồ kiến trúc hạ tầng dự án với các mối quan hệ component chi tiết.<br>- Tái cấu trúc code skeleton để phù hợp với thiết kế kiến trúc đã cập nhật.<br>- Chuẩn hóa lựa chọn ngôn ngữ lập trình và framework cho tính nhất quán của team.<br>- Khám phá khả năng Amazon Q Developer: AI-powered code generation, testing, và vulnerability scanning. | 20/10/2025 | 21/10/2025 | [Amazon Q Developer](https://aws.amazon.com/q/developer/) |

### Khóa học AWS Skill Builder đã hoàn thành

| Khóa học | Danh mục | Trạng thái |
|----------|----------|------------|
| Static Website Hosting with Amazon S3 | Lưu trữ | ✅ |
| Data Protection with AWS Backup | Reliability | ✅ |
| Content Delivery with Amazon CloudFront | Mạng | ✅ |

### Kết quả đạt được tuần 6

**Thành thạo Dịch vụ Lưu trữ:**
- Hiểu toàn diện về kiến trúc Amazon S3: Buckets, độ bền (99.999999999%), và static website hosting
- Nắm vững S3 Storage Classes: Standard, Standard-IA, Glacier cho các access patterns khác nhau
- Học patterns tích hợp AWS Storage Gateway cho hybrid cloud storage
- Hiểu Object Lifecycle Management cho automated data tiering và tối ưu chi phí

**Disaster Recovery & Backup:**
- Nắm bắt fundamentals disaster recovery: RTO (Recovery Time Objective) và RPO (Recovery Point Objective)
- Học dịch vụ AWS Backup cho quản lý backup tập trung across services
- Hiểu các chiến lược Backup & Restore cho business continuity

**Kỹ năng Phát triển:**
- Nâng cao lập trình Python thông qua các bài tập thực hành
- Tạo thành công S3 buckets, cấu hình static websites, và test lifecycle policies
- Cải thiện hiểu biết về data structures và error handling

**Lập kế hoạch Dự án:**
- Hoàn thiện sơ đồ kiến trúc hạ tầng toàn diện
- Tái cấu trúc code skeleton với cấu trúc thư mục phù hợp
- Chuẩn hóa technology stack cho hợp tác team

**Insights DevSecOps:**
- Tham gia webinar "Reinventing DevSecOps with AWS Generative AI" (16/10/2025)
- Học tích hợp DevSecOps: Security trong SDLC sử dụng Jenkins (CI/CD), SonarQube (SAST), OWASP ZAP (DAST), Terraform (IaC)
- Khám phá Amazon Q Developer: AI assistant cho code generation, testing, vulnerability scanning, và AWS optimization

**Bài học chính:**
- S3 là nền tảng cho object storage trên AWS - hiểu storage classes là cực kỳ quan trọng cho tối ưu chi phí
- Lifecycle policies tự động hóa quản lý dữ liệu và giảm chi phí lưu trữ đáng kể
- AWS Backup cung cấp quản lý backup thống nhất across multiple AWS services
- DevSecOps tích hợp security xuyên suốt development lifecycle, không phải là afterthought
