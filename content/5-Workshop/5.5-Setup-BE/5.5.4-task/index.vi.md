---
title: "Tạo Service & Task"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.5.4. </b> "
---

Trong bước cuối cùng của quá trình triển khai backend, chúng ta sẽ định nghĩa cấu hình chạy cho ứng dụng Spring Boot và khởi tạo nó dưới dạng một ECS Service ổn định.

### 1. Tạo Task Definition

1.  Truy cập **Amazon ECS** > **Task definitions** > **Create new task definition**.
2.  **Cấu hình Task definition:**
    -   **Family:** `bandup-backend`.
    -   **Launch type:** `AWS Fargate`.
    -   **OS/Architecture:** `Linux/X86_64`.
    -   **Task size:** `1 vCPU` và `2 GB` Memory.
        -   _Lưu ý: Ứng dụng Java (Spring Boot) thường yêu cầu nhiều bộ nhớ hơn Node.js để quản lý JVM heap hiệu quả._
    -   **Task Role & Execution Role:** Chọn `ecsTaskExecutionRole`.

![Tổng quan Task Definition](/images/5-Workshop/5.5-be/be-task-1.png)

3.  **Chi tiết Container:**

    -   **Name:** `bandup-be-container`.
    -   **Image URI:** Nhập ECR URI (`.../band-up-backend:v1.0.0`).
    -   **Container Port:** `8080` (Cổng mặc định của Spring Boot).

4.  **Cấu hình Biến môi trường (Thực hành tốt):**
    Thay vì nhập thủ công các biến nhạy cảm (Database URL, Username, Password) dưới dạng văn bản thuần, chúng ta sẽ tải chúng từ một file bảo mật được lưu trữ trên S3.
    -   **Environment files:** Nhập S3 ARN của file `.env` (ví dụ: `arn:aws:s3:::bandup2025-fcj/.env`).
    -   _Yêu cầu:_ Đảm bảo `ecsTaskExecutionRole` của bạn đã có quyền đọc (GetObjects) đối với file S3 này.

![Cấu hình Container và S3 Env](/images/5-Workshop/5.5-be/be-task-2.png)

### 2. Tạo ECS Service

Triển khai task definition vào cluster.

1.  Truy cập `bandup-cluster` > **Services** > **Create**.
2.  **Cấu hình Deployment:**
    -   **Compute options:** `FARGATE`.
    -   **Family:** `bandup-backend` (Chọn Revision mới nhất).
    -   **Service name:** `bandup-backend-service`.
    -   **Desired tasks:** `1`.

![Cấu hình Deployment Service](/images/5-Workshop/5.5-be/be-service-1.png)

3.  **Mạng (Networking):**
    -   **VPC:** `band-up-vpc`.
    -   **Subnets:** Chọn **Private Subnets** (`private-subnet-1`, `private-subnet-2`).
    -   **Security group:** Chọn `ecs-backend-sg` (Đã tạo ở bước 5.5.2).

![Cấu hình Mạng Service](/images/5-Workshop/5.5-be/be-service-2.png)

4.  **Cân bằng tải (Load Balancing):**
    -   **Load balancer:** Chọn `bandup-public-alb`.
    -   **Listener:** Sử dụng listener có sẵn `80:HTTP`.
    -   **Target group:** Tạo target group mới tên là `target-bandup-be`.
    -   **Thông tin Container:** Đảm bảo lưu lượng được chuyển đến cổng `8080`.

![Cấu hình Load Balancing](/images/5-Workshop/5.5-be/be-service-3.png)

5.  Nhấn **Create**. Dịch vụ sẽ tiến hành cấp phát tài nguyên Fargate, kéo image về, tải biến môi trường từ S3 và đăng ký container vào ALB.
