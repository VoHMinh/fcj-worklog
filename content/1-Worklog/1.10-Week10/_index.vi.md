---
title: "Worklog Tuần 10"
date: 2024-11-11
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu tuần 10

- **Ổn định môi trường triển khai** AWS SAM/Serverless và giải quyết các vấn đề quan trọng.
- **Tập trung gỡ lỗi** các vấn đề cốt lõi: Cấu hình CORS, template validation errors, và định dạng API response.
- **Tích hợp Frontend/Backend** để cho phép kiểm thử end-to-end trên giao diện người dùng.
- **Hoàn thiện** các chức năng **Read** và **Delete** cơ bản với error handling phù hợp.
- **Tham gia sự kiện AWS Cloud Mastery Series** để nhận hướng dẫn chuyên gia và giải quyết thách thức dự án.

---

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | - **Gỡ lỗi CORS:** Phân tích cấu hình CORS trong **API Gateway** (CORS headers, preflight OPTIONS requests) và Lambda response headers để cho phép Frontend truy cập. <br> - **Khắc phục template validation errors:** Rà soát và tối ưu file `template.yaml` để tránh deployment loop errors và resource dependency issues trong `sam deploy`. | 11/11/2024 | 11/11/2024 | Tài liệu API Gateway/CORS |
| 3 | - Củng cố chức năng **Read** (Truy xuất flashcard sets): Đảm bảo dữ liệu được query từ DynamoDB chính xác và trả về đúng định dạng JSON cho Frontend consumption. <br> - Triển khai error handling cho missing records và invalid queries. <br> - Thêm logging cho mục đích debugging. | 12/11/2024 | 12/11/2024 | Tài liệu DynamoDB Query |
| 4 | - **Tích hợp Frontend:** Bắt đầu kết hợp Frontend codebase với dự án và test các API endpoints đã deploy. <br> - **Thành công hiển thị** danh sách flashcard sets trên giao diện người dùng. <br> - Kiểm thử kết nối API và data rendering trong React/Vue components. | 13/11/2024 | 13/11/2024 | Tài liệu Frontend Framework |
| 5 | - Triển khai và kiểm thử chức năng **Delete** (Xóa flashcard sets). <br> - **Gặp lỗi:** Xác định vấn đề authorization với **Cognito User Sub ID** khi thực hiện Delete function - Lambda không thể extract/process Sub ID từ Cognito token chính xác. <br> - Bắt đầu troubleshooting authentication flow. | 14/11/2024 | 14/11/2024 | Tài liệu AWS Cognito |
| 6 | - **Tham gia sự kiện AWS Cloud Mastery Series:** <br>&emsp; + Nhận hướng dẫn chuyên gia và giải đáp các câu hỏi về Serverless architecture, Lambda best practices, và authentication patterns. <br> - **Phân tích lỗi Update/Delete:** Áp dụng hướng dẫn từ Mentor để giải quyết vấn đề authorization và Cognito token parsing problems. <br> - Ghi lại các giải pháp để tham khảo trong tương lai. | 15/11/2024 | 15/11/2024 | Mentor, AWS Cloud Mastery Series |

---

### Kết quả đạt được tuần 10

- **Khắc phục thành công** lỗi CORS và ổn định quá trình triển khai SAM (giảm thiểu template validation errors).
- **Tham gia sự kiện AWS Cloud Mastery Series** và thu thập thông tin thiết yếu để giải quyết các blockers lớn của dự án.
- **Hoàn thành tích hợp Frontend và Backend**, đạt được giao diện người dùng chức năng đầu tiên cho kiểm thử end-to-end.
- **Đã triển khai thành công chức năng Read** (Truy xuất flashcard sets) và **Delete** (Xóa flashcard sets), hoạt động trên giao diện web.
- **Xác định và có hướng giải quyết** cho các điểm nghẽn quan trọng:
  - Lỗi authorization: Lambda không thể lấy/xử lý không đúng **Cognito Sub ID** từ JWT token, ảnh hưởng đến các thao tác cần quyền
  - Dependency chức năng Update: Yêu cầu authentication flow phù hợp và token validation
- Dự án đã chuyển sang giai đoạn kiểm thử người dùng cơ bản với các thao tác CRUD hoạt động.
- Thiết lập debugging workflow và error handling patterns cho team.

**Bài học chính:**
- CORS yêu cầu cấu hình phù hợp trong cả API Gateway và Lambda response headers
- Cognito JWT tokens phải được decode đúng cách để extract user identity (Sub ID)
- Tích hợp Frontend-Backend yêu cầu chú ý cẩn thận đến API contracts và data formats
- Error handling và logging là thiết yếu cho debugging production issues
- AWS Cloud Mastery Series cung cấp insights thực tế quý giá từ các practitioners giàu kinh nghiệm
