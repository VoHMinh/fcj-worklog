---
title: "Thiết lập ECR & Đẩy Image"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

Trong bước này, chúng ta sẽ đóng gói ứng dụng Backend (Spring Boot) và đẩy Docker image đã được tối ưu hóa lên Amazon ECR.

### 1. Chiến lược Dockerfile

Đối với Backend, chúng ta sử dụng chiến lược **Multi-Stage Build** với `eclipse-temurin:21` (Java 21). Điều này giúp tạo ra image cuối cùng nhỏ gọn và bảo mật.

-   **Stage 1 (Deps):** Tải về các thư viện phụ thuộc (dependencies) của Maven.
-   **Stage 2 (Package):** Build ứng dụng và trích xuất **Spring Boot Layered Jar**. Kỹ thuật này tách ứng dụng thành các lớp riêng biệt (dependencies, spring-boot-loader, code ứng dụng), giúp Docker cache hiệu quả các phần ít thay đổi.
-   **Stage 3 (Final):** Sao chép các lớp đã trích xuất vào một JRE image nhẹ. Đồng thời tạo một user không có quyền quản trị (`appuser`) để tăng cường bảo mật.

![Dockerfile Stage 1 - Dependencies](/images/5-Workshop/5.5-be/be-docker-1.png)
![Dockerfile Stage 2 - Trích xuất Layer](/images/5-Workshop/5.5-be/be-docker-2.png)
![Dockerfile Stage 3 - Final Image](/images/5-Workshop/5.5-be/be-docker-3.png)

### 2. Build Docker Image

Chạy lệnh build tại thư mục gốc của dự án backend. Chúng ta đặt tag là `band-up-backend`.

```bash
docker build -t band-up-backend .
```

Docker sẽ thực thi các bước biên dịch và đóng gói như đã định nghĩa.

![Quá trình Build Backend](/images/5-Workshop/5.5-be/docker-build-1.png)

### 3. Tạo ECR Repository

Chúng ta cần một kho chứa cho image này.

1.  Truy cập **Amazon ECR** > **Create repository**.
2.  **Repository name:** Nhập `band-up-backend`.
3.  **Visibility:** `Private`.
4.  **Image tag mutability:** `Mutable`.
5.  Nhấn **Create repository**.

### 4. Đẩy Image lên ECR (Push)

Sau khi build xong và repository đã sẵn sàng, chúng ta tiến hành đẩy image.

**Bước 1: Gán Tag (Tagging)**
Gán tag phiên bản (ví dụ: `v1.0.0`) cho image local.

```bash
docker tag band-up-backend:latest [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-backend:v1.0.0
```

**Bước 2: Push lên ECR**
Tải các layer lên AWS.

```bash
docker push [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-backend:v1.0.0
```

![Push Backend Image](/images/5-Workshop/5.5-be/docker-build-3.png)

### 5. Kiểm tra kết quả

Truy cập **Amazon ECR Console** và chọn repository `bandup-backend`. Bạn sẽ thấy image với tag `v1.0.0` đã được tải lên thành công.

![Kiểm tra Backend Image](/images/5-Workshop/5.5-be/image-check.png)
