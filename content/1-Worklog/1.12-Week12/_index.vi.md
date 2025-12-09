---
title: "Worklog Tuần 12"
date: 2024-11-25
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

### Mục tiêu tuần 12: 

* **Hoàn thiện 100% các chức năng CRUD cơ bản** và **AI xử lý ảnh** (bao gồm chức năng Update).
* **Nâng cấp kiến trúc xử lý ảnh** bằng cách tích hợp **SQS** để phân luồng và xử lý bất đồng bộ.
* **Hoàn thành các tính năng thiết yếu cuối cùng** của dự án: Bảo mật, Ghim Map, và SNS.
* **Hoàn thiện các giao diện chính** của Frontend, chuẩn bị mua tên miền và **tham gia sự kiện AWS Cloud Mastery Series cuối cùng**.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | - **Hoàn thiện chức năng Update và AI:** Khắc phục triệt để các lỗi cuối cùng (Sub ID, Rekognition) để chức năng CRUD và xử lý ảnh hoạt động trọn vẹn. | 25/11/2024 | 25/11/2024 | Hướng dẫn Mentor, Codebase Backend |
| 3 | - **Nâng cấp luồng AI với SQS:** Triển khai **AWS SQS** để tạo hàng đợi xử lý ảnh bất đồng bộ, giúp phân luồng và cải thiện hiệu suất khi lượng ảnh tải lên lớn. <br> - **Tạo luồng xử lý rõ ràng:** Định nghĩa lại luồng đi của dữ liệu (Upload -> S3 -> SQS -> Lambda (AI) -> DynamoDB). | 26/11/2024 | 26/11/2024 | Tài liệu AWS SQS, Kiến trúc Lambda |
| 4 | - **Frontend hoàn thiện giao diện:** Hoàn tất các giao diện cho các trang chính (Homepage, Chi tiết bài viết, Trang quản lý cá nhân). <br> - **Triển khai Ghim Map:** Tích hợp chức năng Ghim Map (Map Pinning) cho các bài viết, sử dụng dữ liệu định vị (geo data) trong DynamoDB hoặc một dịch vụ map phù hợp. | 27/11/2024 | 27/11/2024 | Codebase Frontend, DynamoDB Geo |
| 5 | - **Hoàn thiện Bảo mật (Authorization):** Tối ưu hóa việc xác thực và phân quyền (IAM Policy/Cognito), đặc biệt là việc lấy `Sub` chính xác cho các thao tác của người dùng. <br> - **Triển khai SNS:** Tích hợp **AWS SNS** cho các tính năng thông báo cơ bản (ví dụ: thông báo khi bài viết được xử lý xong/tải lên thành công). | 28/11/2024 | 28/11/2024 | Tài liệu AWS SNS, Cognito/IAM |
| 6 | - **Tham gia sự kiện AWS Cloud Mastery Series cuối cùng:** Nhận hướng dẫn tổng thể về dự án, kiểm tra và hoàn thiện các phần còn thiếu (tên miền, bảo mật, SNS) trước khi demo. <br> - **Tên miền:** Tiến hành nghiên cứu và chuẩn bị mua tên miền cho trang web, cấu hình DNS cơ bản (Route 53) nếu cần thiết (dựa trên hướng dẫn mentor). | 29/11/2024 | 29/11/2024 | Mentor, AWS Cloud Mastery Series, Route 53 |

---

### Kết quả đạt được tuần 12: 

* **Hoàn thành 100% các chức năng CRUD cơ bản** và **AI xử lý ảnh**, đảm bảo tính ổn định của hệ thống.
* **Nâng cấp kiến trúc xử lý ảnh** bằng cách tích hợp **SQS** và xác định các luồng xử lý bất đồng bộ rõ ràng, cải thiện hiệu suất và độ tin cậy.
* **Hoàn thiện các tính năng thiết yếu:** Đã triển khai Bảo mật, Ghim Map và thông báo bằng SNS.
* **Giao diện Frontend cơ bản đã hoàn thiện**, sẵn sàng cho việc trình bày.
* **Tham gia thành công sự kiện AWS Cloud Mastery Series cuối cùng**, nhận được hướng dẫn tổng thể để hoàn thiện dự án.
* **Đã nghiên cứu và lên kế hoạch mua tên miền** cho sản phẩm.
* Dự án đã đạt đến trạng thái **Sẵn sàng trình bày (Demo Readiness)**.