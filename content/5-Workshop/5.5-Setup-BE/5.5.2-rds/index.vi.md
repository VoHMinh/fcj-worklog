---
title: "Tạo PostgreSQL RDS"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

Trong bước này, chúng ta sẽ khởi tạo một Amazon RDS cho PostgreSQL. Đây sẽ là nơi lưu trữ dữ liệu bền vững chính cho nền tảng IELTS BandUp. Chúng ta sẽ cấu hình nó với tính sẵn sàng cao (High Availability) và bảo mật trong VPC.

### 1. Cấu hình Security Group

Trước khi tạo cơ sở dữ liệu, chúng ta cần xác định các quy tắc tường lửa.

**Bước 1.1: Tạo Backend Security Group**
Nhóm này dành cho ECS Fargate tasks (lớp ứng dụng).

-   **Tên:** `ecs-backend-sg`.
-   **Inbound:** Cho phép cổng `8080` (Mặc định của Spring Boot) từ ALB.

![Backend Security Group](/images/5-Workshop/5.5-be/rds-sg-1.png)

**Bước 1.2: Tạo RDS Security Group**
Nhóm này được gắn vào chính database.

-   **Tên:** `rds-sg`.
-   **Inbound:** Cho phép lưu lượng PostgreSQL (Cổng `5432`) **chỉ từ** `ecs-backend-sg` vừa tạo ở trên. Điều này đảm bảo chỉ ứng dụng của chúng ta mới có thể giao tiếp với database.

![RDS Security Group](/images/5-Workshop/5.5-be/rds-sg-2.png)

### 2. Tạo DB Subnet Group

RDS cần biết những subnet nào nó được phép sử dụng. Chúng ta sẽ gom nhóm các subnet database riêng tư lại với nhau.

1.  Truy cập **Amazon RDS** > **Subnet groups** > **Create DB subnet group**.
2.  **Tên:** `bandup-db-subnet-group`.
3.  **VPC:** Chọn `band-up-vpc`.
4.  **Add subnets:** Chọn các Availability Zone và chọn `private-database-subnet-1` cùng `private-database-subnet-2`.

![Tạo DB Subnet Group](/images/5-Workshop/5.5-be/rds-db-subnet-group.png)

### 3. Khởi tạo Database

Bây giờ, chúng ta sẽ khởi tạo instance PostgreSQL.

1.  Truy cập **Databases** > **Create database**.
2.  **Phương thức tạo:** `Standard create`.
3.  **Engine options:** `PostgreSQL` (Phiên bản 17.6 hoặc mới nhất).

![Chọn Engine](/images/5-Workshop/5.5-be/rds-1.png)

4.  **Availability and durability:** Chọn **Multi-AZ DB instance**. Tùy chọn này tạo một DB chính và một bản sao đồng bộ (standby) ở một Availability Zone khác để tự động chuyển đổi khi có sự cố.
5.  **Settings:**
    -   **DB instance identifier:** `bandup-db`.
    -   **Master username:** `postgres`.
    -   **Credential management:** `Self managed`.
    -   **Master password:** Đặt mật khẩu mạnh (hãy lưu lại để dùng sau này).

![Cấu hình Availability và Credential](/images/5-Workshop/5.5-be/rds-2.png)

6.  **Instance configuration:**
    -   **DB instance class:** `Burstable classes` -> `db.t4g.micro` (Tiết kiệm chi phí cho workshop).
7.  **Storage:** `gp3` (General Purpose SSD) với dung lượng `20 GiB`.

![Cấu hình Instance và Storage](/images/5-Workshop/5.5-be/rds-3.png)

8.  **Connectivity:**
    -   **Compute resource:** Chọn Don't connect to an EC2 compute resource.
    -   **VPC:** `band-up-vpc`.
    -   **DB subnet group:** `bandup-db-subnet-group` (Đã tạo ở bước 2).
    -   **Public access:** **No** (Rất quan trọng để bảo mật).
    -   **VPC security group:** Chọn existing -> `rds-sg`.

![Cấu hình Connectivity](/images/5-Workshop/5.5-be/rds-4.png)

9.  **Database authentication:** `Password authentication`.
10. **Monitoring:** Bật `Performance Insights` (lưu trữ 7 ngày).

![Xác thực và Giám sát](/images/5-Workshop/5.5-be/rds-5.png)

11. **Additional configuration:**
    -   **Initial database name:** `band_up` (Quan trọng: Hibernate sẽ tìm tên DB này khi khởi động).
    -   **Backup:** Bật sao lưu tự động.
    -   **Encryption:** Bật mã hóa.

![Cấu hình bổ sung](/images/5-Workshop/5.5-be/rds-7.png)

12. Nhấn **Create database**. Quá trình khởi tạo sẽ mất vài phút để hoàn tất.
