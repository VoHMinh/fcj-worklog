---
title: "Cấu hình Security Group"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

Các container Frontend của chúng ta hoạt động trong Private Subnet. Để cho phép chúng nhận lưu lượng truy cập, ta cần cấu hình Security Group đóng vai trò như một bức tường lửa ảo.

Tuân thủ nguyên tắc bảo mật (best practices), chúng ta sẽ **chỉ cho phép truy cập từ Application Load Balancer (ALB)** vào cổng 3000. Mọi truy cập trực tiếp từ Internet hoặc các nguồn khác đều sẽ bị chặn.

### 1. Tạo Security Group

1.  Truy cập **EC2 Dashboard** > **Security Groups** > **Create security group**.
2.  **Basic details (Thông tin cơ bản):**
    -   **Security group name:** `ecs-private-sg`.
    -   **Description:** `security group for ecs`.
    -   **VPC:** Chọn `band-up-vpc`.

![Thông tin cơ bản Security Group](/images/5-Workshop/5.4-fe/fe-sg-1.png)

### 2. Cấu hình Inbound Rules (Quy tắc chiều vào)

Đây là bước quan trọng nhất. Chúng ta cần cho phép ALB giao tiếp với ứng dụng Next.js.

1.  **Inbound rules:** Nhấn **Add rule**.
    -   **Type:** `Custom TCP`.
    -   **Port range:** `3000` (Cổng mà ứng dụng Next.js đang lắng nghe).
    -   **Source:** Chọn **Custom** và tìm chọn Security Group ID của ALB (ví dụ: `alb-sg`).
        -   _Lưu ý: Bằng cách chọn ID của Security Group thay vì dải IP, chúng ta đảm bảo rằng chỉ có lưu lượng xuất phát từ Load Balancer mới được chấp nhận._

![Cấu hình Inbound Rules](/images/5-Workshop/5.4-fe/fe-sg-2.png)

2.  **Outbound rules (Quy tắc chiều ra):** Giữ nguyên mặc định (Allow all traffic) để container có thể tải các gói tin hoặc gọi API bên ngoài.
3.  Nhấn **Create security group**.

Security Group hiện đã sẵn sàng để được gắn vào ECS Task trong bước tiếp theo.
