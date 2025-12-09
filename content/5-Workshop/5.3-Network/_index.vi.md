---
title: "Hạ tầng Mạng & Bảo mật"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

### Tổng quan

Trong phần này, chúng ta sẽ xây dựng lớp mạng nền tảng và các thiết lập bảo mật cốt lõi cho **IELTS BandUp**.

Một kiến trúc mạng vững chắc là yếu tố then chốt để bảo vệ dữ liệu người dùng và đảm bảo tính sẵn sàng cao của hệ thống. Thay vì sử dụng các cài đặt mạng mặc định, chúng ta sẽ xây dựng một **Virtual Private Cloud (VPC)** tùy chỉnh, được thiết kế chuyên biệt cho môi trường production. Thiết lập này cho phép kiểm soát chặt chẽ luồng truy cập giữa các thành phần ứng dụng (Frontend, Backend, Database) và Internet.

Ngoài ra, chúng ta sẽ cấu hình các **VPC Endpoints** để cho phép các container trong mạng nội bộ giao tiếp an toàn với các dịch vụ AWS (như ECR và S3) mà không cần đi qua Internet công cộng, giúp tối ưu hóa cả về bảo mật lẫn hiệu năng mạng.



### Các bước thực hiện

Chúng ta sẽ chia quy trình thiết lập hạ tầng thành các nhiệm vụ chính sau:

1.  **VPC & Kết nối:** Khởi tạo môi trường mạng cô lập, phân chia thành các Subnet Public/Private và cấu hình Internet Gateway (IGW) cho kết nối ra bên ngoài.
2.  **Cân bằng tải (ALB):** Thiết lập Application Load Balancer và các Target Group để phân phối lưu lượng truy cập đến các ECS task sau này.
3.  **Bảo mật IAM:** Cấp phát `ecsTaskExecutionRole` để ủy quyền cho Fargate container thực hiện các tác vụ như tải image và ghi log.
4.  **Cấu hình VPC Endpoints:** Thiết lập kết nối riêng tư đến các dịch vụ AWS (ECR, CloudWatch, S3) để bảo mật lưu lượng nội bộ.

### Nội dung

1. [Cấu hình VPC, Subnets & Routing](5.3.1-vpc/)
2. [Cài đặt Application Load Balancer (ALB)](5.3.2-alb/)
3. [IAM Roles cho ECS](5.3.3-iam/)
4. [Thiết lập VPC Endpoints](5.3.4-endpoints/)
