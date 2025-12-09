---
title: "Tạo ElastiCache (Redis/Valkey)"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

Trong bước này, chúng ta sẽ khởi tạo một kho dữ liệu in-memory để xử lý việc quản lý session và caching cho backend. Chúng ta sẽ sử dụng **Amazon ElastiCache** với engine **Valkey** (một nhánh mã nguồn mở hiệu năng cao của Redis được AWS hỗ trợ).

### 1. Cấu hình Security Group

Đầu tiên, tạo Security Group để cho phép backend giao tiếp với cache cluster.

1.  Truy cập **EC2** > **Security Groups** > **Create security group**.
2.  **Name:** `redis-sg`.
3.  **Inbound rules:** Cho phép lưu lượng **Custom TCP** tại cổng `6379` từ nguồn là `ecs-backend-sg`.

_(Lưu ý: Hãy đảm bảo tạo SG này trước khi vào giao diện ElastiCache)._

### 2. Tạo Subnet Group

Chúng ta cần xác định các subnet mà cache node sẽ hoạt động.

1.  Truy cập **Amazon ElastiCache** > **Subnet groups** > **Create subnet group**.
2.  **Name:** `bandup-cached-subnet-group`.
3.  **VPC:** Chọn `band-up-vpc`.
4.  **Subnets:** Chọn `private-database-subnet-1` và `private-database-subnet-2` (Availability Zones `ap-southeast-1a` và `1b`).

![Redis Subnet Group](/images/5-Workshop/5.5-be/redis-subnet-group.png)

### 3. Tạo ElastiCache Cluster

Bây giờ chúng ta tiến hành khởi tạo cluster.

1.  Truy cập **ElastiCache** > **Caches** > **Create cache**.
2.  **Engine:** Chọn `Valkey - recommended` (Tương thích với Redis OSS).
3.  **Deployment option:** Chọn `Node-based cluster` (Cho phép kiểm soát loại instance).
4.  **Creation method:** `Cluster cache`.

![Chọn Engine](/images/5-Workshop/5.5-be/redis-1.png)

5.  **Cấu hình Cluster:**
    -   **Cluster mode:** `Disabled` (Cấu trúc primary-replica đơn giản là đủ).
    -   **Name:** `bandup-redis`.
    -   **Description:** `in memory db for bandup`.

![Cấu hình Cluster](/images/5-Workshop/5.5-be/redis-2.png)

6.  **Cấu hình Node:**
    -   **Node type:** `cache.t3.micro` (Tiết kiệm chi phí cho workshop).
    -   **Number of replicas:** `0` (Chạy node đơn lẻ).

![Cấu hình Node](/images/5-Workshop/5.5-be/redis-3.png)

7.  **Kết nối (Connectivity):**
    -   **Network type:** `IPv4`.
    -   **Subnet groups:** Chọn `bandup-cached-subnet-group`.

![Cấu hình Kết nối](/images/5-Workshop/5.5-be/redis-4.png)

8.  **Bảo mật & Mã hóa:**
    -   **Encryption at rest:** Enabled (Default key).
    -   **Encryption in transit:** Enabled.
    -   **Access control:** `No access control` (Chúng ta dựa vào Security Group để bảo mật).
    -   **Security groups:** Chọn `redis-sg` đã tạo trước đó.

![Cấu hình Bảo mật](/images/5-Workshop/5.5-be/redis-5.png)

9.  **Sao lưu:** Bật sao lưu tự động (Retention: 1 ngày).
10. Nhấn **Create**.

Trạng thái của cluster sẽ chuyển sang **Creating**. Sau khi chuyển sang **Available**, hãy ghi lại **Primary Endpoint** (có dạng `...cache.amazonaws.com:6379`) để sử dụng cấu hình cho backend.
