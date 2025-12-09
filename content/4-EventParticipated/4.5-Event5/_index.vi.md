---
title: "Event 5"
date: 2025-11-29
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---


# Bài thu hoạch: AWS Cloud Mastery Series #3 — Theo AWS Well-Architected Security Pillar

## Mục đích của sự kiện

- Giới thiệu vai trò của Security Pillar trong AWS Well-Architected Framework.
- Hướng dẫn 5 trụ cột cốt lõi của bảo mật Cloud: Identity & Access Management, Detection, Infrastructure Protection, Data Protection và Incident Response.
- Cung cấp các best practices và kịch bản thực tế (Playbook) để bảo vệ ứng dụng trên Cloud.

## Nội dung nổi bật

### Pillar 1 — Identity & Access Management 

- Nguyên tắc: Least Privilege, Zero Trust, Defense in Depth.
- IAM hiện đại: tránh long-term credentials, ưu tiên Roles và Policies.
- IAM Identity Center: SSO và quản lý Permission Sets.
- Bảo mật multi-account: SCP (Service Control Policies) và Permission Boundaries.
- Mini demo: validate IAM policy và mô phỏng quyền truy cập.

### Pillar 2 — Detection 

- Giám sát liên tục: CloudTrail (org-level), GuardDuty, Security Hub.
- Logging ở mọi tầng: VPC Flow Logs, ALB/S3 logs.
- Tự động hóa cảnh báo: sử dụng EventBridge.

### Pillar 3 — Infrastructure Protection 

- Bảo mật mạng: VPC segmentation (private vs public).
- Phòng thủ: Security Groups vs NACLs; dùng WAF, Shield, Network Firewall.
- Workload security: bảo mật EC2, ECS/EKS cơ bản.

### Pillar 4 — Data Protection 

- Mã hóa: encryption at-rest & in-transit (S3, EBS, RDS, DynamoDB).
- Quản lý khóa và secrets: KMS, Secrets Manager, Parameter Store.
- Phân loại dữ liệu (data classification) và guardrails truy cập.

### Pillar 5 — Incident Response 

- Vòng đời IR: quy trình xử lý sự cố theo AWS.
- IR playbook & automation.
- Kịch bản mẫu: compromised IAM key, S3 public exposure, EC2 malware detection.
- Tự động phản hồi bằng Lambda/Step Functions.

## Những gì học được

- Nắm được 5 trụ cột Security Pillar và nguyên tắc Shared Responsibility Model.
- IAM nâng cao: sử dụng IAM Identity Center, SCPs và tránh long-term credentials.
- Data security: tầm quan trọng của KMS và quản lý secrets.
- Incident Response: xây dựng playbook và tự động hóa phản hồi bằng serverless.

## Trải nghiệm trong event

- Buổi workshop là phần tổng kết của chuỗi, cung cấp kiến thức bảo mật trước khi hoàn thiện dự án.
- Phần trình bày về IAM Identity Center và Secrets Manager giúp giải quyết vấn đề xác thực Sub ID và quản lý khóa API của nhóm.
- Kịch bản IR (ví dụ S3 public exposure) rất hữu ích cho việc củng cố chính sách bảo mật dự án.
- Buổi Q&A giúp định hướng lộ trình học tiếp theo (Security Specialty).

