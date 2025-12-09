---
title : "CI/CD với CodeBuild & CodePipeline"
date :   
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

## Quy trình CI/CD với AWS CodeBuild & CodePipeline

Tài liệu này hướng dẫn triển khai quy trình CI/CD dùng AWS CodePipeline và CodeBuild với GitLab làm SCM. Khi tạo một Release mới trong repository GitLab, CodePipeline sẽ được kích hoạt, CodeBuild chạy hai dự án frontend và backend dựa trên `frontend-buildspec.yml` và `backend-buildspec.yml` hiện có, sau đó CodePipeline deploy lên ECS.

### Bạn sẽ làm gì
- 5.3.1 – Cấu hình dự án CodeBuild (frontend/backend) và trigger theo Release GitLab
- 5.3.2 – Thiết kế CodePipeline để deploy ECS và tích hợp artifact sau build

### Điều kiện tiên quyết
- Quyền IAM cho CodeBuild, CodePipeline, S3, Secrets Manager (nếu dùng token GitLab) và IAM pass role.
- Ứng dụng mẫu có `buildspec.yml` (chúng tôi sẽ cung cấp mẫu tối thiểu trong bước).
- S3 bucket cho artifact của pipeline (trình hướng dẫn CodePipeline sẽ tạo/chọn giúp bạn).

### Sơ đồ tổng quan
Luồng chính: Tag push từ GitLab -> API Gateway/Lambda -> upload archive lên S3 -> CodePipeline (S3 Source) kích hoạt -> CodeBuild build -> (Tùy chọn) Deploy.

![CI/CD overview placeholder](/images/5-Workshop/5.3-S3-vpc/ci-overview.png)

Đặt sơ đồ tại: `static/images/5-Workshop/5.3-S3-vpc/ci-overview.png`.

> Mẹo: Dùng đường dẫn ảnh tuyệt đối `/images/...` và lưu screenshot trong `static/images/5-Workshop/5.3-S3-vpc/`.