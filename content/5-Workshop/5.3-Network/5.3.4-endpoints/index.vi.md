---
title: "Thiết lập VPC Endpoints"
date: 2025-09-09
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

Để đảm bảo an toàn bảo mật, các dịch vụ backend chạy trong Private Subnet không nên truy cập các dịch vụ AWS quan trọng thông qua Internet công cộng. Thay vào đó, chúng ta sử dụng **AWS PrivateLink** (VPC Endpoints) để giữ lưu lượng này hoàn toàn bên trong mạng lưới của AWS.

Chúng ta sẽ tạo **4 Endpoints**:

-   **Interface Endpoints:** Cho ECR (Docker & API) và CloudWatch Logs.
-   **Gateway Endpoint:** Cho Amazon S3.

### 1. Tạo Interface Endpoints (ECR & CloudWatch)

Chúng ta sẽ bắt đầu tạo endpoint cho **ECR Docker** (`ecr.dkr`). Quy trình này hoàn toàn tương tự cho **ECR API** (`ecr.api`) và **CloudWatch** (`logs`).

**Bước 1: Chọn dịch vụ**

1.  Truy cập **VPC Dashboard** > **Endpoints** > **Create endpoint**.
2.  **Name tag:** `ecr-endpoint` (cho Docker).
3.  **Service category:** Chọn **AWS services**.
4.  **Services:** Tìm kiếm `ecr.dkr` và chọn `com.amazonaws.ap-southeast-1.ecr.dkr`.

![Chọn dịch vụ ECR](/images/5-Workshop/5.3-Network/ecr-endpoint1.png)

**Bước 2: Cấu hình VPC & Subnets**

1.  **VPC:** Chọn `band-up-vpc`.
2.  **Subnets:** Chọn các Availability Zone và tích chọn các **Private Subnets** (`private-app-subnet-1` và `private-app-subnet-2`).
    -   _Lưu ý: Bước này sẽ tạo các Network Interface (ENI) trong private subnet đóng vai trò là cổng kết nối._

![Chọn Subnets](/images/5-Workshop/5.3-Network/ecr-endpoint2.png)

**Bước 3: Chọn Security Group**

1.  **Security groups:** Chọn Security Group cho phép lưu lượng HTTPS (Port 443) từ VPC của bạn.
    -   _Trong workshop này, bạn có thể chọn default security group nếu nó cho phép inbound traffic từ nội bộ VPC._

![Chọn Security Group](/images/5-Workshop/5.3-Network/ecr-endpoint-3.png)

2.  Nhấn **Create endpoint**.

![Tạo Endpoint thành công](/images/5-Workshop/5.3-Network/ecr-endpoint-success.png)

**Bước 4: Lặp lại cho ECR API và CloudWatch**
Lặp lại các bước trên để tạo thêm 2 Interface Endpoints:

-   **ECR API:** Tìm kiếm `ecr.api` -> Đặt tên: `ecr-api-endpoint`.
-   **CloudWatch Logs:** Tìm kiếm `logs` -> Đặt tên: `cloudwatch-endpoint`.

### 2. Tạo Gateway Endpoint (S3)

Đối với Amazon S3, chúng ta sử dụng **Gateway Endpoint**. Loại này tiết kiệm chi phí hơn và sử dụng bảng định tuyến (Route Table) thay vì card mạng ảo.

1.  Nhấn **Create endpoint**.
2.  **Name tag:** `s3-endpoint`.
3.  **Services:** Tìm kiếm `s3` và chọn `com.amazonaws.ap-southeast-1.s3` (Loại: **Gateway**).
4.  **VPC:** Chọn `band-up-vpc`.
5.  **Route tables:** Tích chọn các Route Table được liên kết với **Private Subnets**.
6.  Nhấn **Create endpoint**.

### 3. Kiểm tra kết quả

Sau khi hoàn tất, quay lại danh sách **Endpoints**. Bạn sẽ thấy 4 endpoint đang hoạt động, đảm bảo kết nối bảo mật cho hạ tầng hệ thống.

-   `ecr-endpoint` (Interface)
-   `ecr-api-endpoint` (Interface)
-   `cloudwatch-endpoint` (Interface)
-   `s3-endpoint` (Gateway)

![Danh sách Endpoints hoàn chỉnh](/images/5-Workshop/5.3-Network/more-endpoint.png)
