---
title: "Triển khai Backend (ECS Fargate)"
date: 2025-09-09
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

### Tổng quan

Trong phần này, chúng ta sẽ triển khai **IELTS BandUp Backend**, một ứng dụng Spring Boot đóng vai trò là lớp xử lý logic cốt lõi của nền tảng.

Khác với Frontend, Backend yêu cầu các cơ chế lưu trữ và bộ nhớ đệm (caching) để hoạt động hiệu quả. Do đó, trước khi khởi chạy các container trên ECS Fargate, chúng ta bắt buộc phải thiết lập hạ tầng dữ liệu (PostgreSQL và Redis). Dịch vụ Backend sẽ được đặt trong các Private Subnet, được bảo vệ nghiêm ngặt bởi Security Group và sẽ giao tiếp với các dịch vụ AI thông qua AWS SDK.



### Các bước thực hiện

Để triển khai hệ thống backend hoàn chỉnh, chúng ta sẽ tuân theo trình tự sau:

1.  **Container Registry (ECR):** Đóng gói ứng dụng Spring Boot và đẩy Docker image lên kho lưu trữ ECR riêng tư.
2.  **Cơ sở dữ liệu quan hệ (RDS):** Khởi tạo Amazon RDS for PostgreSQL để lưu trữ dữ liệu người dùng, kết quả bài thi và nội dung học tập.
3.  **Bộ nhớ đệm (ElastiCache):** Thiết lập cụm Amazon ElastiCache (Redis) để quản lý phiên đăng nhập (session) và tăng tốc độ truy xuất dữ liệu.
4.  **ECS Task & Service:** Định nghĩa cấu hình task (bao gồm các biến môi trường kết nối Database) và khởi chạy dịch vụ.

### Nội dung

1. [Thiết lập ECR & Đẩy Image](5.5.1-ecr/)
2. [Tạo PostgreSQL RDS](5.5.2-rds/)
3. [Tạo ElastiCache (Redis)](5.5.3-redis/)
4. [Tạo Service & Task](5.5.4-task/)
