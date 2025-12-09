---
title: "Cấu hình Application Load Balancer (ALB)"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

Application Load Balancer (ALB) đóng vai trò là cổng vào duy nhất cho mọi lưu lượng truy cập đến nền tảng. Nó chịu trách nhiệm phân phối yêu cầu đến các container phù hợp (Frontend hoặc Backend) và xử lý mã hóa SSL.

### 1. Tạo Security Group cho ALB

Trước khi tạo Load Balancer, chúng ta cần một lớp tường lửa cho phép truy cập từ Internet.

1.  Truy cập **EC2 Dashboard** > **Security Groups** > **Create security group**.
2.  **Security group name:** `alb-sg`.
3.  **Description:** `Allow http and https traffic`.
4.  **VPC:** Chọn `band-up-vpc`.
5.  **Inbound rules:** Thêm các quy tắc sau để cho phép truy cập từ bất kỳ đâu:
    -   **Type:** `HTTP` | **Port:** `80` | **Source:** `Anywhere-IPv4` (`0.0.0.0/0`).
    -   **Type:** `HTTPS` | **Port:** `443` | **Source:** `Anywhere-IPv4` (`0.0.0.0/0`).
6.  Nhấn **Create security group**.

![Tạo Security Group cho ALB](/images/5-Workshop/5.3-Network/alb-sg.png)

### 2. Tạo Target Group

ALB cần biết nơi để chuyển tiếp lưu lượng truy cập. Chúng ta sẽ tạo Target Group cho dịch vụ Frontend trước (Backend sẽ cấu hình sau).

1.  Truy cập **EC2 Dashboard** > **Target groups** > **Create target group**.
2.  **Choose a target type:** Chọn **IP addresses** (Bắt buộc cho ECS Fargate).
3.  **Target group name:** `target-bandup-fe`.
4.  **Protocol:** `HTTP`.
5.  **Port:** `3000` (Next.js frontend của chúng ta chạy trên port 3000).
6.  **VPC:** Chọn `band-up-vpc`.
7.  Nhấn **Next**.

![Cấu hình Target Group](/images/5-Workshop/5.3-Network/alb-target-group-1.png)

8.  **Register targets:** Vì chúng ta chưa triển khai ECS task nào, hãy bỏ qua bước này và nhấn **Create target group**.

![Tạo Target Group thành công](/images/5-Workshop/5.3-Network/alb-target-group-result.png)

### 3. Khởi tạo Application Load Balancer

Bây giờ, chúng ta sẽ kết hợp mọi thứ vào Load Balancer.

**Bước 1: Cấu hình cơ bản (Basic Configuration)**

1.  Truy cập **Load Balancers** > **Create load balancer**.
2.  Chọn **Application Load Balancer** và nhấn **Create**.
3.  **Load balancer name:** `bandup-public-alb`.
4.  **Scheme:** `Internet-facing` (Cho phép truy cập công khai).
5.  **IP address type:** `IPv4`.

![Chọn loại ALB](/images/5-Workshop/5.3-Network/create-alb.png)
![Cấu hình tên ALB](/images/5-Workshop/5.3-Network/create-alb-1.png)

**Bước 2: Ánh xạ mạng (Network Mapping)**

1.  **VPC:** Chọn `band-up-vpc`.
2.  **Mappings:** Chọn **hai Availability Zones** (`ap-southeast-1a` và `ap-southeast-1b`).
3.  **Subnets:** QUAN TRỌNG - Phải chọn các **Public Subnets** (`public-subnet-1` và `public-subnet-2`) đã tạo ở phần trước.
    -   _Lưu ý: Nếu chọn nhầm Private subnets, ALB sẽ không thể nhận truy cập từ Internet._

![Ánh xạ mạng ALB](/images/5-Workshop/5.3-Network/create-alb-2.png)

**Bước 3: Security Groups & Listeners**

1.  **Security groups:** Bỏ chọn default và chọn `alb-sg` vừa tạo.
2.  **Listeners and routing:**
    -   **Protocol:** `HTTP` | **Port:** `80`.
    -   **Default action:** Forward to `target-bandup-fe`.
3.  Nhấn **Create load balancer**.

![Cấu hình Listener](/images/5-Workshop/5.3-Network/create-alb-3.png)

ALB của bạn đang được khởi tạo. Sau khi chuyển sang trạng thái Active, nó sẽ sẵn sàng điều hướng lưu lượng đến ứng dụng Frontend.
