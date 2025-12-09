---
title: "Thiết lập ECR & IAM Role"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

Trong bước này, chúng ta sẽ chuẩn bị hạ tầng AWS cần thiết để lưu trữ các container image. Quá trình này bao gồm việc kiểm tra trạng thái ban đầu, tạo IAM Role cần thiết cho tính năng sao chép của ECR, và khởi tạo repository.

### 1. Kiểm tra trạng thái ECR

Đầu tiên, kiểm tra trạng thái hiện tại của Private Registry. Ban đầu, chưa có repository nào được tạo.

![Kiểm tra ECR trống](/images/5-Workshop/5.4-fe/ecr-check.png)

### 2. Tạo IAM Role cho ECR

Chúng ta cần tạo một Service-Linked Role cho phép Amazon ECR thực hiện các hành động sao chép (replication) qua các region hoặc tài khoản khác.

1.  Truy cập **IAM** > **Roles** > **Create role**.
2.  **Select trusted entity:** Chọn **AWS service**.
3.  **Service or use case:** Chọn `Elastic Container Registry` từ danh sách.

![Chọn thực thể tin cậy](/images/5-Workshop/5.4-fe/iam-ecr-create-1.png)

4.  **Use case:** Chọn **Elastic Container Registry - Replication** để cho phép ECR sao chép image.

![Chọn Use Case](/images/5-Workshop/5.4-fe/iam-ecr-create-3.png)

5.  **Add permissions:** Xác nhận rằng chính sách `ECRReplicationServiceRolePolicy` đã được đính kèm. Đây là policy mặc định cấp các quyền cần thiết.

![Kiểm tra quyền hạn](/images/5-Workshop/5.4-fe/iam-ecr-create-4.png)

6.  **Name, review, and create:**
    -   Tên role được đặt tự động là `AWSServiceRoleForECRReplication`.
    -   Xem lại cấu hình và tạo role.

![Xem lại Role](/images/5-Workshop/5.4-fe/iam-ecr-create-5.png)

7.  **Kết quả:** Role đã được tạo thành công và xuất hiện trong danh sách IAM Roles.

![IAM Role đã tạo](/images/5-Workshop/5.4-fe/iam-ecr-create-2.png)

### 3. Tạo ECR Repository

Tiếp theo, chúng ta tạo kho lưu trữ cho image frontend.

1.  Truy cập **Amazon ECR** > **Create repository**.
2.  **General settings:**
    -   **Repository name:** `band-up-frontend`.
    -   **Visibility settings:** Private.
3.  **Image tag settings:** Giữ chế độ **Mutable** để cho phép ghi đè các image tag.

![Cấu hình Repository](/images/5-Workshop/5.4-fe/ecr-create-1.png)

4.  **Kết quả:** Repository `band-up-frontend` đã được tạo thành công với mã hóa mặc định AES-256.

![Repository đã tạo](/images/5-Workshop/5.4-fe/ecr-create-result.png)

### 4. Cấu hình Truy cập CLI (CLI Access)

Để đẩy image từ máy local lên AWS, bạn cần quyền truy cập thông qua AWS CLI. Chúng ta sẽ tạo Access Key cho IAM User của bạn.

1.  Truy cập **IAM Dashboard** > **Users** > Chọn user của bạn (ví dụ: `NamDang`).
2.  Chuyển sang tab **Security credentials** và nhấn **Create access key**.
3.  **Use case:** Chọn **Command Line Interface (CLI)**.
4.  **Description tag:** Nhập mô tả (ví dụ: `ECR Push Key`) và nhấn **Create access key**.
5.  **Lấy khóa:** Quan trọng! Hãy sao chép hoặc tải về **Access Key ID** và **Secret Access Key** ngay lập tức, vì bạn sẽ không thể xem lại Secret Key sau này.

![Tạo Access Key Bước 1](/images/5-Workshop/5.4-fe/get-key-for-cli-1.png)
![Chọn Use Case CLI](/images/5-Workshop/5.4-fe/get-key-for-cli-2.png)
![Lấy thông tin Key](/images/5-Workshop/5.4-fe/get-key-for-cli-4.png)

### 5. Cấu hình AWS CLI

Mở terminal trên máy của bạn và cấu hình AWS CLI với thông tin xác thực vừa tạo.

```bash
aws configure
```

Nhập các thông tin sau khi được hỏi:

-   **AWS Access Key ID:** [Dán Key ID của bạn]
-   **AWS Secret Access Key:** [Dán Secret Key của bạn]
-   **Default region name:** `ap-southeast-1`
-   **Default output format:** `json`

![Cấu hình AWS CLI](/images/5-Workshop/5.4-fe/cli-configure.png)

### 6. Đẩy Image lên ECR (Push)

Khi CLI đã được cấu hình, chúng ta tiến hành xác thực Docker và đẩy image lên.

**Bước 1: Đăng nhập vào ECR**
Chạy lệnh đăng nhập để xác thực Docker client với registry của AWS.

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin [Account-ID]https://www.google.com/search?q=.dkr.ecr.ap-southeast-1.amazonaws.com
```

_Kết quả mong đợi: `Login Succeeded`_

![Đăng nhập CLI thành công](/images/5-Workshop/5.4-fe/cli-login.png)

**Bước 2: Gán Tag cho Image**
Gán tag cho image local `band-up-frontend:latest` với đường dẫn URI đầy đủ của ECR và phiên bản (ví dụ: `v1.0.0`).

```bash
docker tag band-up-frontend:latest [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:v1.0.0
```

![Gán Tag cho Image](/images/5-Workshop/5.4-fe/tag-image.png)

**Bước 3: Thực hiện Push**
Chạy lệnh push để tải các layer của image lên AWS.

```bash
docker push [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:v1.0.0
```

![Quá trình Push Image](/images/5-Workshop/5.4-fe/push-to-ecr.png)

### 7. Kiểm tra kết quả cuối cùng

Quay lại **Amazon ECR Console** và mở repository `band-up-frontend`. Bạn sẽ thấy image với tag `v1.0.0` đã xuất hiện trong danh sách.

![Kiểm tra Image trên Console](/images/5-Workshop/5.4-fe/check-ecr.png)
