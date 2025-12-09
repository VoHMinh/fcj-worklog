---
title: "Triển khai Frontend (ECS Fargate)"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

### Tổng quan

Trong phần này, chúng ta sẽ tiến hành triển khai **Frontend của IELTS BandUp** (ứng dụng Next.js) lên hạ tầng AWS Cloud.

Chúng ta sẽ sử dụng **Amazon Elastic Container Service (ECS)** với loại hình khởi chạy **Fargate**. Cách tiếp cận serverless này cho phép vận hành các container mà không cần quản lý các máy chủ EC2 vật lý bên dưới. Dịch vụ Frontend sẽ được đặt trong các Private Subnet để đảm bảo bảo mật, nhưng vẫn cho phép người dùng truy cập thông qua Application Load Balancer (ALB) đã cấu hình ở phần trước.

### Các bước thực hiện

Để triển khai thành công Frontend, chúng ta sẽ tuân theo quy trình sau:

1.  **Container Registry (ECR):** Tạo kho lưu trữ (repository) để chứa các Docker image và đẩy mã nguồn từ máy local lên AWS.
2.  **Cấu hình Bảo mật (Security Group):** Thiết lập các quy tắc tường lửa, đảm bảo Frontend container chỉ nhận lưu lượng truy cập từ ALB.
3.  **ECS Task & Service:** Thiết lập bản thiết kế (Task Definition) cho container (CPU, RAM, Biến môi trường) và khởi chạy nó dưới dạng một Service ổn định.

### Nội dung

1. [Đóng gói ứng dụng với Docker](5.4.1-docker/)
2. [Thiết lập ECR & Đẩy Image](5.4.2-ecr/)
3. [Cấu hình Security Group](5.4.3-sg/)
4. [Tạo Task Definition & Service](5.4.4-task/)
