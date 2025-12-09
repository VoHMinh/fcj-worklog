---
title: "Các bước chuẩn bị"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

Workshop này được thiết kế dành cho Kỹ sư DevOps, Kiến trúc sư Cloud và Lập trình viên Full-stack mong muốn tìm hiểu quy trình triển khai một ứng dụng hiện đại tích hợp AI trên nền tảng AWS.

Để hoàn thành tốt workshop này, người tham gia cần trang bị các kiến thức, kỹ năng và công cụ sau đây.

### 1. Yêu cầu về kiến thức kỹ thuật

#### Kiến thức nền tảng AWS (AWS Fundamentals)

-   **Thao tác trên Console:** Làm quen với giao diện quản trị [AWS Management Console](https://aws.amazon.com/console/).
-   **Dịch vụ cốt lõi:** Hiểu biết cơ bản về dịch vụ tính toán như [Amazon EC2](https://aws.amazon.com/ec2/) và [AWS Fargate](https://aws.amazon.com/fargate/), các khái niệm mạng trong [Amazon VPC](https://aws.amazon.com/vpc/), và lưu trữ với [Amazon S3](https://aws.amazon.com/s3/).
-   **IAM & Bảo mật:** Hiểu về [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/), cụ thể là vai trò (Roles), chính sách (Policies), và nguyên tắc đặc quyền tối thiểu (least privilege).

#### Container & Điều phối (Containerization & Orchestration)

-   **Docker:** Thành thạo trong việc tạo `Dockerfile`, build images và chạy container trên môi trường local. Cần nắm vững các khái niệm như layers, cổng kết nối (ports) và biến môi trường. Tham khảo [Tài liệu Docker](https://docs.docker.com/).
-   **Khái niệm ECS:** Làm quen với các thuật ngữ của [Amazon ECS](https://aws.amazon.com/ecs/) bao gồm _Task Definitions_, _Services_, _Clusters_, và sự khác biệt giữa hai loại hình khởi chạy EC2 và Fargate.

#### DevOps & CI/CD

-   **Git:** Thành thạo quản lý mã nguồn (commit, push, branching) để kích hoạt các quy trình tự động hóa.
-   **Luồng CI/CD:** Hiểu về nguyên lý Tích hợp liên tục (Continuous Integration) và Chuyển giao liên tục (Continuous Delivery) sử dụng các công cụ như [AWS CodePipeline](https://aws.amazon.com/codepipeline/) và [AWS CodeBuild](https://aws.amazon.com/codebuild/).

#### Kiến thức cơ bản về mạng (Networking)

-   **Giao thức:** Hiểu về HTTP/HTTPS, phân giải tên miền DNS với [Amazon Route 53](https://aws.amazon.com/route53/), và các khái niệm cân bằng tải sử dụng [Application Load Balancer](https://aws.amazon.com/elasticloadbalancing/application-load-balancer/).
-   **Bảo mật mạng:** Kiến thức về địa chỉ IP (CIDR blocks) và kiểm soát lưu lượng sử dụng [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html).

### 2. Thiết lập môi trường

Trước khi bắt đầu workshop, hãy đảm bảo môi trường phát triển cục bộ của bạn đã được cài đặt đầy đủ các công cụ sau:

-   **Tài khoản AWS:** Một tài khoản AWS đang hoạt động với **quyền Quản trị viên (Administrator access)** để khởi tạo tài nguyên.
-   **IDE:** Một trình soạn thảo mã nguồn như [Visual Studio Code](https://code.visualstudio.com/) hoặc IntelliJ IDEA.
-   **Công cụ dòng lệnh (Command Line Tools):**
    -   **AWS CLI (v2):** Đã cài đặt và cấu hình với thông tin đăng nhập của bạn. [Hướng dẫn cài đặt](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html).
    -   **Git:** Đã cài đặt để sao chép (clone) kho mã nguồn. [Tải về](https://git-scm.com/downloads).
    -   **Docker Desktop:** Đang chạy trên máy local để kiểm tra hoặc build images nếu cần thiết. [Tải Docker](https://www.docker.com/products/docker-desktop/).

### 3. Hạn mức dịch vụ & Chi phí

{{% notice warning %}}
**Lưu ý về chi phí:** Workshop này sử dụng các tài nguyên **không nằm trong** gói miễn phí (AWS Free Tier), bao gồm:

-   **NAT Gateways** (Phí theo giờ + Phí xử lý dữ liệu)
-   **Application Load Balancers**
-   **ECS Fargate Tasks** (Tính theo vCPU/Memory sử dụng)
-   **Amazon RDS & ElastiCache**
    {{% /notice %}}

Vui lòng đảm bảo dọn dẹp tài nguyên ngay sau khi hoàn thành workshop để tránh phát sinh chi phí không mong muốn. Hướng dẫn dọn dẹp sẽ được cung cấp ở phần cuối của tài liệu này.
