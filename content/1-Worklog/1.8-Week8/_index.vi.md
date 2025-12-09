---
title: "Worklog Tuần 8"
date: 2024-10-28
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu tuần 8

- **Hoàn thành kỳ thi giữa kỳ** (Ngày 31/10) với kết quả tốt.
- Bắt đầu triển khai các chức năng **CRUD (Create, Read, Update, Delete)** nền tảng cho dự án **Bandup IELTS**.
- Nghiên cứu và lập kế hoạch tích hợp **dịch vụ AWS Serverless** (Lambda, API Gateway, DynamoDB) cho kiến trúc dự án.
- Thiết lập môi trường phát triển và xây dựng cấu trúc dự án.

---

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | - **Ôn tập tổng hợp lần cuối** kiến thức chuẩn bị cho kỳ thi giữa kỳ. <br> - Rà soát các câu hỏi khó và các khái niệm thường bị nhầm lẫn (IAM Policies vs Roles, Security Groups vs NACLs, VPC routing). <br> - Thực hành quản lý thời gian cho việc hoàn thành bài thi. | 28/10/2024 | 28/10/2024 | Ghi chú cá nhân, AWS Builders |
| 3 | - Chuẩn bị tâm lý và thiết lập công cụ cho kỳ thi. <br> - **Thực hành:** Bắt đầu thiết lập môi trường phát triển cho dự án **Bandup IELTS**. <br> - Cài đặt và cấu hình Python development tools, AWS CLI, và IDE setup. | 29/10/2024 | 30/10/2024 | AWS CLI Documentation |
| 4 | - **Thi giữa kỳ** (Ngày 31/10) - Hoàn thành mục tiêu quan trọng nhất. <br> - Phản ánh sau kỳ thi về hiệu suất và các lĩnh vực cần cải thiện. | 31/10/2024 | 31/10/2024 | Địa điểm thi |
| 5 | - Bắt đầu triển khai chức năng **CRUD** cơ bản đầu tiên (Create operation: tạo flashcard sets). <br> - Nghiên cứu và triển khai thử nghiệm **AWS Lambda** functions cho serverless compute. <br> - Nghiên cứu thiết kế **DynamoDB** table để lưu trữ dữ liệu flashcard. | 01/11/2024 | 01/11/2024 | Tài liệu AWS Lambda & DynamoDB |
| 6 | - **Lập kế hoạch tích hợp kiến trúc Serverless:** <br> &emsp; + Nghiên cứu **API Gateway** cho RESTful API endpoints. <br> &emsp; + Thiết kế luồng dữ liệu: Frontend → API Gateway → Lambda → DynamoDB. <br> &emsp; + Định nghĩa cấu trúc Lambda function và event handling patterns. <br> - Triển khai chức năng **Read** cơ bản để truy xuất flashcard sets từ DynamoDB. | 02/11/2024 | 02/11/2024 | Tài liệu API Gateway, Serverless patterns |

---

### Kết quả đạt được tuần 8

- **Hoàn thành kỳ thi giữa kỳ** (Ngày 31/10) thành công.
- **Thiết lập thành công** môi trường phát triển cơ bản cho dự án với Python, AWS CLI, và cấu hình IDE.
- **Bắt đầu xây dựng** chức năng **Create/Read** đầu tiên cho dự án **Bandup IELTS** sử dụng **AWS Lambda** và **DynamoDB**.
- **Nghiên cứu và thiết kế** serverless architecture pattern:
  - API Gateway cho HTTP endpoints
  - Lambda functions cho business logic
  - DynamoDB cho NoSQL data storage
- **Củng cố kiến thức** về các dịch vụ Serverless thiết yếu (Lambda, DynamoDB, API Gateway) quan trọng cho phát triển dự án.
- Tạo cấu trúc dự án ban đầu với tổ chức thư mục phù hợp.
- Triển khai Lambda function handler đầu tiên cho Create operation.

**Bài học chính:**
- Kiến trúc Serverless loại bỏ overhead quản lý server
- Lambda functions là event-driven và scale tự động
- DynamoDB cung cấp độ trễ millisecond đơn cho NoSQL workloads
- API Gateway hoạt động như entry point cho serverless APIs
- Cấu trúc dự án phù hợp ngay từ đầu đơn giản hóa phát triển tương lai
