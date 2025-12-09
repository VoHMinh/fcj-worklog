---
title: "Event 4"
date: 2025-11-17
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---


# Bài thu hoạch: AWS Cloud Mastery Series #2 — DevOps on AWS

## Mục đích của sự kiện

- Củng cố tư duy và nguyên tắc văn hóa DevOps (DevOps culture and principles).
- Giới thiệu chi tiết các dịch vụ AWS hỗ trợ CI/CD: CodeCommit, CodeBuild, CodeDeploy, CodePipeline.
- Đào sâu về Infrastructure as Code (IaC) với CloudFormation và AWS CDK.
- Khám phá dịch vụ Container (ECS, EKS, App Runner) và công cụ giám sát (CloudWatch, X-Ray).

## Nội dung nổi bật

### Sáng: CI/CD Pipeline & Infrastructure as Code (08:30 – 12:00)
- DevOps Mindset: nhấn mạnh các nguyên tắc cốt lõi và các chỉ số DORA (Deployment Frequency, MTTR...).
- CI/CD Services:
  - Source control: AWS CodeCommit và chiến lược Git (GitFlow vs. Trunk-based).
  - Build & Test: AWS CodeBuild.
  - Deployment: AWS CodeDeploy với các chiến lược Blue/Green, Canary, Rolling.
  - Orchestration: tự động hóa luồng với AWS CodePipeline.
- Demo: walkthrough một CI/CD pipeline hoàn chỉnh.
- IaC:
  - AWS CloudFormation: templates, stacks, drift detection.
  - AWS CDK: constructs, reusable patterns, multi-language support.
- Demo & Discussion: triển khai bằng cả CloudFormation và CDK; thảo luận lựa chọn công cụ phù hợp.

### Chiều: Container, Observability & Best Practices (13:00 – 17:00)
- Container Services:
  - Docker: cơ bản về containerization.
  - Amazon ECR: lưu trữ, quét (scanning), quản lý lifecycle image.
  - ECS & EKS: chiến lược triển khai, scaling, orchestration.
  - App Runner: alternative PaaS for containers.
- Monitoring & Observability:
  - AWS CloudWatch: metrics, logs, alarms, dashboards.
  - AWS X-Ray: distributed tracing cho microservices.
- Best practices:
  - Feature flags, A/B testing.
  - Incident management và postmortems.
- Demo & Case study: so sánh chiến lược deploy cho microservices.

## Những gì học được

- DevOps Culture: nắm vững các chỉ số DORA và tư duy tự động hóa.
- CI/CD: hiểu cách các AWS Code services phối hợp; nắm các chiến lược deployment nâng cao (Blue/Green, Canary).
- IaC: có kiến thức nền tảng về CloudFormation và CDK — hữu ích để tối ưu template và kiến trúc multi-stack.
- Observability: tầm quan trọng của giám sát toàn diện bằng CloudWatch và X-Ray để debug lỗi (CORS, Lambda, v.v.).
- Container services (ECS/EKS) cung cấp định hướng cho kiến trúc phức tạp trong dự án.
- Phần demo CI/CD & IaC rất thực tế, giúp nhóm hiểu cách tối ưu hóa quá trình triển khai.

## Trải nghiệm trong event

- Workshop chi tiết, cung cấp nhiều kiến thức thực tiễn và áp dụng ngay.
- Phần demo và case study hữu ích cho việc áp dụng vào dự án.
- Cơ hội kết nối với cộng đồng và chuyên gia.

