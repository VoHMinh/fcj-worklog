---
title: "Worklog Tuần 3"
date: 2025-09-21
weight: 3
chapter: false
pre: "<b>1.3. </b>"
---

### Mục tiêu tuần 3

- Khắc phục vấn đề tài khoản AWS và tạo tài khoản mới nếu cần.
- Thành thạo cấu hình Hybrid DNS với Route 53 Resolver.
- Triển khai và hiểu VPC Peering cho giao tiếp giữa các VPC.
- Thảo luận kế hoạch dự án và chọn ngôn ngữ lập trình với team.

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Quản trị quyền truy cập với AWS Identity and Access Management (IAM)| 21/09/2025 | 23/09/2025 | [Quản trị quyền truy cập với AWS Identity and Access Management (IAM)](https://000002.awsstudygroup.com/vi) |
| 3 | - Hoàn thành Lab 10: Route 53 và cấu hình Hybrid DNS.<br>- Khởi chạy máy chủ ảo để triển khai và kiểm tra DNS.<br>- Hoàn thành: **Hybrid DNS Management with Amazon Route 53**. | 24/09/2025 | 25/09/2025 | [FCJ Playlist](https://youtube.com/playlist?list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i) |
| 4 | - Triển khai VPC Peering cho giao tiếp private giữa các VPC.<br>- Tạo các tài nguyên cần thiết cho cấu hình VPC Peering.<br>- Dọn dẹp tài nguyên sau khi hoàn thành.<br>- Hoàn thành: **Network Integration with VPC Peering**. | 25/09/2025 | 26/09/2025 | [AWS VPC Peering](https://docs.aws.amazon.com/vpc/latest/peering/) |
| 5 | - Tham gia họp nhóm để thảo luận kế hoạch dự án và chọn ngôn ngữ lập trình.<br>- Đặt deadline cho các thành viên nghiên cứu công nghệ đã chọn. | 28/09/2025 | 28/09/2025 | Họp nhóm |

### Khóa học AWS Skill Builder đã hoàn thành

| Khóa học | Danh mục | Trạng thái |
|----------|----------|------------|
| Hybrid DNS Management with Amazon Route 53 | Mạng | ✅ |
| Network Integration with VPC Peering | Mạng | ✅ |
| Networking on AWS Workshop | Mạng | ✅ |
| Infrastructure as Code with AWS CloudFormation | DevOps | ✅ |
| Cloud Development with AWS Cloud9 | Phát triển | ✅ |
| Static Website Hosting with Amazon S3 | Lưu trữ | ✅ |

### Kết quả đạt được tuần 3

**Kỹ năng kỹ thuật đã tiếp thu:**

*Route 53 và Hybrid DNS:*
- Cấu hình thành công hạ tầng Hybrid DNS với Route 53 Resolver
- Tạo và cấu hình **Outbound Endpoints** để chuyển tiếp DNS queries
- Thiết lập **Route 53 Resolver** rules cho conditional DNS resolution
- Triển khai **Inbound Endpoints** cho DNS queries từ on-premises đến AWS
- Kết nối thành công với RD Gateway Server trong các bài thực hành

*VPC Peering:*
- Nắm vững khái niệm VPC Peering cho giao tiếp private giữa các VPC mà không qua internet công cộng
- Kích hoạt **Cross-Zone and Cross-Region DNS Resolution** trong VPC Peering:
  - EC2 instances có thể phân giải DNS của instances trong VPCs được peering ra địa chỉ IP private
  - Hiểu rằng nếu không có tính năng này, DNS queries trả về public IPs, định tuyến traffic qua internet
- Học quy trình dọn dẹp tài nguyên để tránh chi phí không cần thiết

*Infrastructure as Code:*
- Học cách provision tài nguyên AWS sử dụng CloudFormation templates
- Hiểu nguyên tắc quản lý hạ tầng declarative
- Khám phá AWS Cloud9 như môi trường phát triển trên cloud

**Hợp tác nhóm:**
- Tham gia họp nhóm để xác định hướng đi dự án
- Chọn ngôn ngữ lập trình cho dự án
- Thiết lập deadline cho các thành viên nghiên cứu công nghệ đã chọn
- Tiếp tục hành trình học tập với sự hỗ trợ của FCJ team

**Bài học chính:**
- Hybrid DNS cho phép phân giải DNS liền mạch giữa on-premises và AWS
- VPC Peering hiệu quả về chi phí để kết nối VPCs nhưng có giới hạn (không có transitive peering)
- CloudFormation templates đảm bảo triển khai hạ tầng nhất quán, có thể lặp lại
- AWS Cloud9 loại bỏ sự phức tạp của việc thiết lập môi trường phát triển local
