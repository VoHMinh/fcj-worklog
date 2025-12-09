---
title: "Worklog Tuần 11"
date: 2024-11-18
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu tuần 11: 

* **Tham gia sự kiện AWS Cloud Mastery Series #2** để tiếp tục giải đáp các vấn đề kỹ thuật chuyên sâu.
* **Tái cấu trúc và thống nhất cấu trúc Frontend** để tăng tính ổn định và dễ bảo trì.
* **Triển khai kiến trúc Multi-Stack** để tối ưu hóa tốc độ triển khai và quản lý dự án Serverless.
* **Tích hợp các chức năng CRUD cơ bản với AI Image Processing** (sử dụng Rekognition) vào trang web.
* **Khắc phục triệt để các lỗi triển khai** (đặc biệt là lỗi CORS) để ổn định hệ thống.

---

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| CN | - **Tham gia sự kiện AWS Cloud Mastery Series #2 (Ngày 17/11):** Tiếp tục nhận hướng dẫn và giải đáp thắc mắc chuyên sâu hơn về lỗi xác thực và luồng AI. | 17/11/2024 | 17/11/2024 | Mentor, AWS Cloud Mastery Series |
| 2 | - **Thống nhất và tái cấu trúc Frontend:** Tiến hành họp nhóm để thống nhất lại cấu trúc code Frontend nhằm đảm bảo tính đồng bộ và dễ bảo trì. <br> - **Nghiên cứu giải pháp Multi-Stack:** Bắt đầu phân tích cách tách file `template.yaml` thành các Stack nhỏ hơn (Multi-Stack) để tối ưu hóa quá trình `sam deploy`. | 18/11/2024 | 18/11/2024 | Tài liệu Kiến trúc Serverless |
| 3 | - **Triển khai kiến trúc Multi-Stack:** Bắt đầu tách và cấu hình các Stack riêng biệt (như Stack cho Backend API, Stack cho Frontend Hosting). <br> - **Tiến hành tích hợp AI Image Processing:** Kết hợp các chức năng CRUD cơ bản với logic xử lý ảnh (ví dụ: gọi API Rekognition/S3 trigger) để chuẩn bị cho chức năng Update. | 19/11/2024 | 19/11/2024 | Codebase Backend, AWS Rekognition |
| 4 | - **Gặp lỗi sau khi tích hợp AI:** Hệ thống tiếp tục gặp lỗi sau khi kết hợp chức năng AI, yêu cầu phải xóa Stack cũ và Deploy lại hoàn toàn. <br> - **Leader phát triển Stack dự phòng:** Leader tạo một Stack Multi-Stack riêng biệt, đã tối ưu hóa, để dự phòng và làm tham chiếu cho việc triển khai tối ưu hóa sau này. | 20/11/2024 | 20/11/2024 | Stack dự phòng của Leader |
| 5 | - **Lỗi CORS tái diễn:** Sau khi deploy lại, hệ thống tiếp tục gặp lỗi CORS. <br> - **Gỡ lỗi CORS chuyên sâu:** Dành thời gian phân tích triệt để nguyên nhân gốc rễ và sửa chữa dứt điểm lỗi CORS, đảm bảo các headers phản hồi được cấu hình chính xác trên cả API Gateway và Lambda. | 21/11/2024 | 21/11/2024 | Cấu hình API Gateway/Lambda |
| 6 | - **Họp bàn và ổn định hóa dự án:** Họp nhóm để kiểm tra cấu trúc Frontend mới, ổn định lại Stack dự án chính và đồng bộ hóa các bản sửa lỗi CORS và Template. <br> - **Tối ưu hóa bảo trì:** Đưa ra giải pháp sử dụng Stack riêng (do leader phát triển) để đảm bảo tính linh hoạt và dễ tối ưu hóa trong quá trình phát triển tiếp theo. | 22/11/2024 | 22/11/2024 | Báo cáo cấu trúc mới |

---

### Kết quả đạt được tuần 11: 

* **Tham gia chuỗi sự kiện AWS Cloud Mastery Series #2**, thu thập thêm kiến thức sâu hơn về Serverless, Rekognition, và giải pháp cho các lỗi xác thực.
* **Tái cấu trúc thành công Frontend** và thống nhất được cấu trúc chung cho dự án, cải thiện khả năng bảo trì.
* **Triển khai kiến trúc Multi-Stack** (hoặc ít nhất là có giải pháp/Stack dự phòng) giúp đẩy nhanh quá trình deploy và dễ dàng quản lý tài nguyên.
* **Khắc phục được triệt để lỗi CORS** sau khi tìm ra nguyên nhân gốc rễ, đảm bảo đường truyền giữa Frontend và Backend ổn định.
* **Lĩnh hội được cách khắc phục các lỗi Template cơ bản** và hiểu rõ hơn về các vấn đề triển khai trên AWS SAM.
* **Phát triển thêm Stack riêng biệt để dự phòng/tối ưu hóa**, tăng tính linh hoạt và an toàn cho dự án trong các lần cập nhật lớn sau này.
* Dự án đã bước vào giai đoạn thử nghiệm chức năng AI, mặc dù vẫn còn lỗi, nhưng đã có hướng đi rõ ràng để gỡ rối.