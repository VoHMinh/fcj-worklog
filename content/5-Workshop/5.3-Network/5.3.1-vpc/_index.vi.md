---
title: "Cấu hình VPC, Subnets & Routing"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

Trong bước này, chúng ta sẽ thiết lập môi trường mạng cô lập cho IELTS BandUp. Chúng ta sẽ khởi tạo một Virtual Private Cloud (VPC), phân chia nó thành các subnet trên nhiều Availability Zones, và cấu hình định tuyến để truy cập Internet.

### 1. Khởi tạo VPC

Đầu tiên, chúng ta cần một không gian mạng riêng tư.

1.  Truy cập **VPC Dashboard**.
2.  Chọn **Create VPC**.
3.  Chọn mục **VPC only**.
4.  **Name tag:** Điền `band-up-vpc`.
5.  **IPv4 CIDR block:** Điền `10.0.0.0/16` (Dải mạng này cung cấp 65,536 địa chỉ IP, đủ cho việc mở rộng sau này).
6.  Giữ nguyên các cài đặt khác và nhấn **Create VPC**.

![Cài đặt VPC](/images/5-Workshop/5.3-Network/create-vpc.png)
![Tạo VPC thành công](/images/5-Workshop/5.3-Network/create-vpc-success.png)

### 2. Tạo Subnets (Phân vùng mạng)

Tiếp theo, chúng ta chia VPC thành các mạng nhỏ hơn (Subnets) phân tán trên hai Availability Zones (AZs) để đảm bảo tính sẵn sàng cao (High Availability). Chúng ta sẽ tuân theo sơ đồ IP sau:

| Tên Subnet                  | Loại     | CIDR Block    | Availability Zone |
| :-------------------------- | :------- | :------------ | :---------------- |
| `public-subnet-1`           | Public   | `10.0.0.0/24` | `ap-southeast-1a` |
| `public-subnet-2`           | Public   | `10.0.1.0/24` | `ap-southeast-1b` |
| `private-app-subnet-1`      | Private  | `10.0.2.0/24` | `ap-southeast-1a` |
| `private-app-subnet-2`      | Private  | `10.0.3.0/24` | `ap-southeast-1b` |
| `private-database-subnet-1` | Database | `10.0.4.0/24` | `ap-southeast-1a` |
| `private-database-subnet-2` | Database | `10.0.5.0/24` | `ap-southeast-1b` |

**Các bước thực hiện:**

1.  Vào menu **Subnets** > **Create subnet**.
2.  Chọn **VPC ID**: `band-up-vpc`.
3.  Nhập **Subnet name**, **Availability Zone**, và **IPv4 CIDR block** cho từng subnet theo bảng trên.
4.  Lặp lại quy trình cho đến khi tạo đủ 6 subnets.

![Tạo Public Subnet](/images/5-Workshop/5.3-Network/create-subnet.png)
![Tạo Database Subnet](/images/5-Workshop/5.3-Network/create-subnet-end.png)

### 3. Tạo Internet Gateway (IGW)

Mặc định, một VPC hoàn toàn khép kín. Để các tài nguyên trong Public Subnet có thể giao tiếp với thế giới bên ngoài, ta cần một Internet Gateway.

1.  Vào menu **Internet gateways** > **Create internet gateway**.
2.  **Name tag:** Điền `band-up-igw`.
3.  Nhấn **Create internet gateway**.
4.  Sau khi tạo xong, nhấn **Actions** > **Attach to VPC**.
5.  Chọn `band-up-vpc` và nhấn **Attach internet gateway**.

![Gắn IGW vào VPC](/images/5-Workshop/5.3-Network/create-igw-success.png)

### 4. Cấu hình Route Tables (Bảng định tuyến)

Cuối cùng, ta cần điều hướng lưu lượng từ Public Subnets ra Internet Gateway.

1.  Vào menu **Route tables** > **Create route table**.
2.  **Name:** `public-route-table`.
3.  **VPC:** `band-up-vpc`.
4.  Nhấn **Create route table**.

**Thêm Route ra Internet:**

1.  Chọn `public-route-table` vừa tạo.
2.  Chuyển sang tab **Routes** > **Edit routes**.
3.  Thêm một route mới:
    -   **Destination:** `0.0.0.0/0` (Tất cả lưu lượng).
    -   **Target:** Chọn `Internet Gateway` -> `band-up-igw`.
4.  Nhấn **Save changes**.

![Chỉnh sửa Routes](/images/5-Workshop/5.3-Network/create-route-table.png)

**Liên kết Subnets (Subnet Association):**

1.  Chuyển sang tab **Subnet associations** > **Edit subnet associations**.
2.  Tích chọn các **Public Subnets** (`public-subnet-1` và `public-subnet-2`).
3.  Nhấn **Save associations**.

![Liên kết Subnet](/images/5-Workshop/5.3-Network/edit-route-tabe.png)

### 5. Kiểm tra cấu hình

Để kiểm chứng kiến trúc mạng đã được thiết lập chính xác hay chưa, hãy quay lại **VPC Dashboard**, chọn `band-up-vpc` và xem tab **Resource map**. Bạn sẽ thấy một sơ đồ rõ ràng liên kết các Public Subnet với Route Table và Internet Gateway.

![Kết quả Resource Map VPC](/images/5-Workshop/5.3-Network/vpc-result.png)
