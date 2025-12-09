---
title: "Thiết lập ECR & Đẩy Image"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Trong bước này, chúng ta sẽ tiến hành đóng gói ứng dụng Frontend (Next.js) thành Docker container và đẩy (push) image đó lên Amazon Elastic Container Registry (ECR). Image này sẽ được ECS Fargate sử dụng để khởi chạy ứng dụng sau này.

### 1. Chuẩn bị Dockerfile

Chúng tôi sử dụng `Dockerfile` đa tầng (multi-stage) được tối ưu hóa cho **Bun** (một JavaScript runtime tốc độ cao) và Next.js. Cấu hình này giúp giảm kích thước image cuối cùng và tăng cường bảo mật.

-   **Base Image:** `oven/bun:1.1.26`
-   **Builder:** Thực hiện biên dịch ứng dụng Next.js.
-   **Runner:** Môi trường production nhẹ nhàng, mở cổng 3000.

![Nội dung Dockerfile](/images/5-Workshop/5.4-fe/docker-image.png)

### 2. Build Docker Image

Chạy lệnh sau tại thư mục gốc của dự án để build image. Chúng ta sẽ đặt tag là `band-up-frontend`.

```bash
docker build -t band-up-frontend .
```

Quá trình build sẽ cài đặt các thư viện phụ thuộc bằng `bun install` và biên dịch dự án.

![Bắt đầu lệnh Build](/images/5-Workshop/5.4-fe/build-1.png)
![Quá trình Build](/images/5-Workshop/5.4-fe/build-2.png)
![Build Thành công](/images/5-Workshop/5.4-fe/build-3.png)

### 3. Kiểm tra Image tại Local

Sau khi build xong, hãy kiểm tra xem image đã được tạo thành công hay chưa.

```bash
docker image ls
```

Bạn sẽ thấy `band-up-frontend` với tag `latest` trong danh sách.

![Danh sách Docker Images](/images/5-Workshop/5.4-fe/result-build.png)

**Chạy thử nghiệm:**
Bạn có thể chạy thử container trên máy local để đảm bảo ứng dụng khởi động đúng cách trước khi đẩy lên AWS.

```bash
docker run -p 3000:3000 band-up-frontend:latest
```

![Chạy Docker tại Local](/images/5-Workshop/5.4-fe/run-image.png)

### 4. Đẩy Image lên Amazon ECR

Bây giờ chúng ta cần tải image này lên AWS.

**Bước 1: Tạo Repository**

1.  Truy cập **Amazon ECR** > **Repositories**.
2.  Nhấn **Create repository**.
3.  **Visibility settings:** Chọn `Private`.
4.  **Repository name:** Nhập `band-up-frontend`.
5.  Nhấn **Create repository**.

**Bước 2: Push Image**
Sử dụng AWS CLI để xác thực và đẩy image. Hãy thay thế `[AWS_ACCOUNT_ID]` và `[REGION]` bằng thông tin tài khoản của bạn.

1.  **Đăng nhập vào ECR:**

    ```
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin [AWS_ACCOUNT_ID].dkr.ecr.ap-southeast-1.amazonaws.com
    ```

2.  **Gán Tag cho Image:**

    ```
    docker tag band-up-frontend:latest [AWS_ACCOUNT_ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:latest
    ```

3.  **Đẩy Image lên ECR:**
    ```
    docker push [AWS_ACCOUNT_ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:latest
    ```

Sau khi quá trình push hoàn tất, image của bạn đã được lưu trữ an toàn trên AWS ECR và sẵn sàng để triển khai.
