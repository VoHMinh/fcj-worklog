---
title: "Worklog Tuần 5"
date: 2025-10-07
weight: 5
chapter: false
pre: "<b>1.5. </b>"
---

### Mục tiêu tuần 5

- Xác định và giải quyết chi phí AWS bất thường trên tài khoản.
- Thiết kế và phân chia kiến trúc hạ tầng cho dự án.
- Bắt đầu cấu hình dự án ban đầu và phân bổ vai trò team.
- Khám phá AWS Skill Builder và nâng cao học tập về các chủ đề tối ưu hóa.

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Phân tích và xác định nguyên nhân chi phí bất thường trên tài khoản AWS.<br>- Hoàn thành: **Cost and Usage Management** và **Managing Quotas with Service Quotas**. | 07/10/2025 | 08/10/2025 | [AWS Cost Explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/) |
| 3 | - Thiết kế và phân chia kiến trúc hạ tầng dự án.<br>- Đề xuất các template kiến trúc cơ bản cho team tham khảo.<br>- Hoàn thành: **Building Highly Available Web Applications**. | 09/10/2025 | 10/10/2025 | [FCJ Community](https://www.facebook.com/groups/awsstudygroupfcj) |
| 4 | - Xây dựng code skeleton và cấu hình các file dự án ban đầu.<br>- Thiết lập môi trường phát triển.<br>- Hoàn thành: **Development Environment with AWS Toolkit for VS Code**. | 11/10/2025 | 13/10/2025 | VS Code + AWS Toolkit |
| 5 | - Đăng ký AWS Skill Builder và khám phá các khóa học.<br>- Nghiên cứu kỹ thuật tối ưu EC2.<br>- Hoàn thành: **Right-Sizing with EC2 Resource Optimization**. | 11/10/2025 | 12/10/2025 | [AWS Skill Builder](https://skillbuilder.aws/) |

### Khóa học AWS Skill Builder đã hoàn thành

| Khóa học | Danh mục | Trạng thái |
|----------|----------|------------|
| Cost and Usage Management | Tối ưu chi phí | ✅ |
| Managing Quotas with Service Quotas | Operations | ✅ |
| Billing Console Delegation | Quản lý chi phí | ✅ |
| Right-Sizing with EC2 Resource Optimization | Tối ưu chi phí | ✅ |
| Development Environment with AWS Toolkit for VS Code | Phát triển | ✅ |
| Building Highly Available Web Applications | Kiến trúc | ✅ |
| Database Essentials with Amazon RDS | Database | ✅ |
| NoSQL Database Essentials with Amazon DynamoDB | Database | ✅ |
| In-Memory Caching with Amazon ElastiCache | Database | ✅ |
| Command Line Operations with AWS CLI | Operations | ✅ |

### Kết quả đạt được tuần 5

**Kỹ năng kỹ thuật đã tiếp thu:**

*Tối ưu chi phí:*
- Xác định nguyên nhân chi phí AWS bất thường:
  - Xóa không hoàn toàn tài nguyên EC2 (EBS volumes, Elastic IPs)
  - Thiếu kiểm soát tài khoản người dùng và IAM permissions
  - Tài nguyên chạy ở các regions không sử dụng
- Học các best practices quản lý chi phí AWS:
  - **AWS Budgets** để cảnh báo chi phí chủ động
  - **Cost Explorer** để phân tích patterns chi tiêu
  - **Service Quotas** để quản lý giới hạn tài khoản
  - **Billing Console Delegation** cho visibility chi phí team
- Đề xuất các biện pháp tối ưu chi phí cho team

*Thiết kế kiến trúc:*
- Thiết kế thành công kiến trúc hạ tầng dự án
- Tạo các template kiến trúc tham khảo cho team áp dụng
- Áp dụng nguyên tắc **High Availability**:
  - Triển khai Multi-AZ
  - Chiến lược load balancing
  - Patterns replication database
  - Design patterns fault-tolerant

*Môi trường phát triển:*
- Thiết lập AWS Toolkit cho VS Code để phát triển streamlined
- Thành thạo AWS CLI cho command-line operations
- Xây dựng code skeleton vững chắc với các file cấu hình ban đầu
- Thiết lập nền tảng dự án cho hợp tác team

*Dịch vụ Database:*
- Hiểu **Amazon RDS** cho nhu cầu relational database
- Học **DynamoDB** cho NoSQL workloads
- Khám phá **ElastiCache** cho in-memory caching (Redis/Memcached)
- Áp dụng tiêu chí chọn database dựa trên use cases

**Tiến độ dự án:**
- Đăng ký và kích hoạt tài khoản AWS Skill Builder
- Bắt đầu khám phá các khóa học nâng cao và learning paths
- Kiến trúc hạ tầng đã hoàn thiện và documented
- Môi trường phát triển đã cấu hình và sẵn sàng coding

**Bài học chính:**
- Tối ưu chi phí bắt đầu từ visibility - sử dụng Cost Explorer hàng ngày
- Right-sizing EC2 instances có thể giảm chi phí 30-50%
- High availability yêu cầu planning across multiple AZs
- AWS Toolkit cho VS Code cải thiện đáng kể developer productivity
- Chọn database phụ thuộc vào data model, scale, và access patterns
- Service Quotas ngăn ngừa các giới hạn capacity không mong muốn
