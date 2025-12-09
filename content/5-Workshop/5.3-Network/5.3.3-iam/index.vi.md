---
title: "IAM Roles cho ECS"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

Để Amazon ECS có thể quản lý các container, dịch vụ này cần được cấp một số quyền hạn nhất định. Chúng ta phải tạo một **IAM Role** để ủy quyền cho ECS agent thực hiện các tác vụ như kéo (pull) container image từ Amazon ECR và gửi log đến Amazon CloudWatch thay mặt cho bạn.

### Tạo ecsTaskExecutionRole

1.  Truy cập **IAM Dashboard**.
2.  Ở thanh điều hướng bên trái, chọn **Roles**.
3.  Nhấn **Create role**.

**Bước 1: Thực thể tin cậy (Trusted Entity)**

1.  **Trusted entity type:** Chọn `AWS service`.
2.  **Service or use case:** Chọn `Elastic Container Service`.
3.  Chọn `Elastic Container Service Task` trong các tùy chọn hiện ra.
4.  Nhấn **Next**.

**Bước 2: Thêm quyền (Permissions)**

1.  Trong thanh tìm kiếm, gõ `AmazonECSTaskExecutionRolePolicy`.
2.  Tích vào ô vuông cạnh tên chính sách **AmazonECSTaskExecutionRolePolicy**.
    -   _Lưu ý: Đây là chính sách được AWS quản lý, cung cấp đủ quyền để kéo image từ ECR và tải log lên CloudWatch._
3.  Nhấn **Next**.

**Bước 3: Đặt tên và Xem lại**

1.  **Role name:** Nhập `ecsTaskExecutionRole`.
2.  Kiểm tra lại cấu hình và nhấn **Create role**.

![Tổng quan ECS Task Execution Role](/images/5-Workshop/5.3-Network/executionRole.png)

Sau khi tạo xong, Role này đã sẵn sàng để gán cho các ECS Task Definition trong các phần tiếp theo của workshop.
