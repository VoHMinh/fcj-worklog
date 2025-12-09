---
title: "Tạo Task Definition & Service"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

Trong bước cuối cùng của phần triển khai Frontend, chúng ta sẽ định nghĩa cách ứng dụng chạy (Task Definition) và triển khai nó dưới dạng một dịch vụ (ECS Service) có khả năng mở rộng, được kết nối trực tiếp với Load Balancer.

### 1. Tạo Task Definition

Task Definition đóng vai trò như một bản thiết kế (blueprint) cho ứng dụng.

1.  Truy cập **Amazon ECS** > **Task definitions** > **Create new task definition**.
2.  **Cấu hình Task definition:**
    -   **Task definition family:** `bandup-frontend`.
    -   **Launch type:** `AWS Fargate`.
    -   **OS/Architecture:** `Linux/X86_64`.
    -   **Task size:** `.5 vCPU` và `1 GB` Memory (Đủ cho ứng dụng Next.js).
    -   **Task Role & Task Execution Role:** Chọn `ecsTaskExecutionRole` (Đã tạo ở phần 5.3.3).

![Cấu hình Hạ tầng Task](/images/5-Workshop/5.4-fe/fe-task-1.png)

3.  **Chi tiết Container:**
    -   **Name:** `bandup-fe-container`.
    -   **Image URI:** Nhập ECR URI chúng ta đã push trước đó (ví dụ: `.../band-up-frontend:v1.0.0`).
    -   **Container port:** `3000`.
4.  Nhấn **Create**.

![Cấu hình Container](/images/5-Workshop/5.4-fe/fe-task-2.png)

### 2. Tạo ECS Service

Bây giờ chúng ta sẽ triển khai bản thiết kế này vào Cluster.

1.  Truy cập **Clusters** > Chọn `bandup-cluster`.
2.  Tại tab **Services**, nhấn **Create**.

![Tổng quan Cluster](/images/5-Workshop/5.4-fe/fe-cluster-1.png)

**Bước 1: Môi trường (Environment)**

-   **Compute options:** `Launch type` -> `FARGATE`.
-   **Task definition:** `bandup-frontend` (Revision 1).
-   **Service name:** `bandup-frontend-service`.
-   **Desired tasks:** `1` (Số lượng container muốn chạy).

![Cấu hình Môi trường Service](/images/5-Workshop/5.4-fe/fe-service-1.png)

**Bước 2: Mạng (Networking)**

-   **VPC:** `band-up-vpc`.
-   **Subnets:** Chọn các **Private Subnets** (`private-subnet-1`, `private-subnet-2`).
-   **Security group:** Chọn `ecs-private-sg` (Cho phép traffic từ ALB).

![Cấu hình Mạng Service](/images/5-Workshop/5.4-fe/fe-service-2.png)

**Bước 3: Cân bằng tải (Load Balancing)**

-   **Load balancer type:** Application Load Balancer.
-   **Load balancer:** Chọn `bandup-public-alb`.
-   **Container to load balance:** `bandup-fe-container 3000:3000`.

![Chọn Load Balancer](/images/5-Workshop/5.4-fe/fe-service-3.png)

-   **Listener:** Chọn Create new listener tại Port `80` (HTTP).
-   **Target group:** Chọn Use an existing target group -> `target-bandup-fe`.

![Cấu hình Listener](/images/5-Workshop/5.4-fe/fe-service-5.png)

3.  Nhấn **Create**. Dịch vụ sẽ bắt đầu triển khai container của bạn. Hãy đợi đến khi trạng thái chuyển sang **Active** và Task status là **Running**.

![Service đang chạy thành công](/images/5-Workshop/5.4-fe/fe-service-6.png)

### 3. Kiểm tra kết quả

Khi dịch vụ đã ổn định, hãy mở trình duyệt web và truy cập vào **DNS name** của Application Load Balancer.

Bạn sẽ thấy trang chủ của **IELTS BandUp** tải thành công, được phục vụ từ container nằm an toàn trong private subnet.

![Truy cập qua ALB DNS](/images/5-Workshop/5.4-fe/check-fe-using-alb.png)
![Giao diện BandUp](/images/5-Workshop/5.4-fe/result.png)
