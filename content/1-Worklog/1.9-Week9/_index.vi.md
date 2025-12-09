---
title: "Worklog Tuần 9"
date: 2024-11-04
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu tuần 9

- Hoàn tất quá trình chuyển đổi sang framework phát triển **AWS SAM (Serverless Application Model)**.
- **Tái cấu trúc (Refactor)** và triển khai lại các chức năng CRUD theo patterns kiến trúc SAM.
- Giải quyết các vấn đề liên quan đến môi trường để đạt được trạng thái **triển khai thành công** lên AWS.
- Tích hợp **Docker** cho môi trường build chuẩn hóa và quản lý dependencies.

---

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | - **Nghiên cứu chuyên sâu về AWS SAM:** Hiểu cấu trúc `template.yaml`, SAM CLI commands, và cách các tài nguyên Serverless (Lambda, API Gateway) hoạt động trong mô hình SAM. <br> - Lập kế hoạch chiến lược migration: Chuyển đổi các Lambda functions hiện có sang cấu trúc tương thích SAM. <br> - Nghiên cứu khả năng SAM local testing (`sam local invoke`, `sam local start-api`). | 04/11/2024 | 04/11/2024 | Tài liệu AWS SAM, AWS Study Group |
| 3 | - **Tái cấu trúc mã nguồn:** Viết lại các chức năng CRUD (Create/Read operations) sử dụng SAM patterns (Lambda handlers và API Gateway events). <br> - **Tích hợp Docker:** Cài đặt và cấu hình Docker để đảm bảo môi trường Python runtime nhất quán cho quá trình `sam build`. <br> - Tạo Dockerfile cho Lambda layer dependencies. | 05/11/2024 | 06/11/2024 | Tài liệu Docker, SAM CLI |
| 4 | - **Gỡ lỗi và kiểm thử Local:** Thực hiện `sam local invoke` để test các Lambda functions riêng lẻ. <br> - **Gặp sự cố nghiêm trọng** trong môi trường Local: Dependency conflicts, Python version mismatches, vấn đề kết nối DynamoDB local. <br> - Cố gắng giải quyết các rào cản local testing thông qua điều chỉnh cấu hình. | 06/11/2024 | 07/11/2024 | Báo cáo lỗi SAM CLI, Stack Overflow |
| 5 | - **Ra quyết định chiến lược:** Backend Team quyết định áp dụng chiến lược **deploy-then-test** trên môi trường AWS thực tế để vượt qua các hạn chế gỡ lỗi Local, chấp nhận rủi ro đã tính toán. <br> - Tập trung khắc phục các lỗi cấu hình trong `template.yaml` (resource definitions, IAM permissions, environment variables). <br> - Xác thực SAM template syntax và resource dependencies. | 07/11/2024 | 08/11/2024 | CloudFormation Template Validator |
| 6 | - **Triển khai thành công:** Thực hiện `sam deploy --guided` và triển khai thành công dự án lên môi trường AWS. <br> - **Xác thực cơ bản:** Kiểm tra các API endpoints đã tạo bằng Postman/curl, xác nhận chức năng CRUD đã hoạt động. <br> - Ghi lại quy trình triển khai và cấu hình cho team tham khảo. | 08/11/2024 | 08/11/2024 | Log triển khai AWS CloudFormation |

---

### Kết quả đạt được tuần 9

- **Hoàn thành chuyển đổi công nghệ** sang mô hình phát triển **AWS SAM** cho toàn bộ dự án.
- **Tái cấu trúc thành công** các chức năng CRUD vào cấu trúc Serverless của SAM với tổ chức handler phù hợp.
- Đã giải quyết vấn đề môi trường bằng cách sử dụng **Docker** để đảm bảo quá trình `sam build` sử dụng đúng Python version và dependencies.
- **Đạt được cột mốc quan trọng:** Triển khai thành công dự án lên môi trường AWS, vượt qua các trục trặc gỡ lỗi Local.
- **Dự án Bandup IELTS** hiện có phiên bản API hoạt động trên môi trường Cloud thực tế (mặc dù vẫn cần kiểm thử sâu hơn).
- Thiết lập deployment workflow và best practices cho hợp tác team.
- Tạo `template.yaml` toàn diện với resource definitions và IAM permissions phù hợp.

**Bài học chính:**
- SAM đơn giản hóa phát triển serverless application với infrastructure as code
- Docker đảm bảo môi trường build nhất quán across các máy phát triển khác nhau
- Chiến lược deploy-then-test có thể khả thi khi local testing có vấn đề
- SAM templates cung cấp single source of truth cho serverless infrastructure
- IAM permissions phù hợp trong SAM templates là cực kỳ quan trọng cho Lambda function execution
