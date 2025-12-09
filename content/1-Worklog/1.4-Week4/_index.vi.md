---
title: "Worklog Tuần 4"
date: 2025-09-29
weight: 4
chapter: false
pre: "<b>1.4. </b>"
---

### Mục tiêu tuần 4

- Theo kịp tiến độ học tập của nhóm về các dịch vụ AWS.
- Thành thạo thiết lập và cấu hình AWS Transit Gateway.
- Hiểu sâu về Amazon EC2 và các dịch vụ compute liên quan.
- Học Git cơ bản để hợp tác nhóm hiệu quả.
- **Workshop: Bắt đầu VPC & Network Setup** cho hạ tầng Bandup IELTS.

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
|-----|-----------|--------------|-----------------|----------------|
| 2 | - Khám phá AWS Transit Gateway: khái niệm, quy trình thiết lập và tài nguyên cần thiết.<br>- So sánh sự khác biệt giữa VPC Peering và Transit Gateway.<br>- Hoàn thành: **Centralized Network Management with AWS Transit Gateway**. | 29/09/2025 | 30/09/2025 | [AWS Transit Gateway](https://aws.amazon.com/transit-gateway/) |
| 3 | - Nghiên cứu sâu về Amazon EC2 qua các bài giảng Module 3.<br>- Học EC2 Auto Scaling cho quản lý tài nguyên tự động.<br>- Hoàn thành: **Scaling Applications with EC2 Auto Scaling**. | 01/10/2025 | 02/10/2025 | [FCJ Playlist](https://youtube.com/playlist?list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i) |
| 4 | - Học và thực hành các lệnh Git (commit, push, pull) cho hợp tác nhóm.<br>- Khám phá Amazon Lightsail cho các giải pháp compute đơn giản.<br>- Hoàn thành: **Simplified Computing with Amazon Lightsail**. | 03/10/2025 | 04/10/2025 | [Git Tutorial](https://www.youtube.com/watch?v=8O14qT3jdq0) |
| 5 | - Đề xuất ý tưởng và phân công nhiệm vụ cho team để chuẩn bị proposal.<br>- Nghiên cứu các chiến lược migration cho AWS.<br>- Hoàn thành: **VM Migration with AWS VM Import/Export**.<br>- **Hoạt động Workshop:** Tạo VPC với CIDR `10.0.0.0/16` và cấu hình DNS support. | 05/10/2025 | 06/10/2025 | Họp nhóm, [Workshop 5.3](5-Workshop/5.3-VPC-Network/) |

### Khóa học AWS Skill Builder đã hoàn thành

| Khóa học | Danh mục | Trạng thái |
|----------|----------|------------|
| Centralized Network Management with AWS Transit Gateway | Mạng | ✅ |
| Scaling Applications with EC2 Auto Scaling | Compute | ✅ |
| Simplified Computing with Amazon Lightsail | Compute | ✅ |
| Container Deployment with Amazon Lightsail Containers | Containers | ✅ |
| VM Migration with AWS VM Import/Export | Migration | ✅ |
| Database Migration with AWS DMS and SCT | Migration | ✅ |
| Disaster Recovery with AWS Elastic Disaster Recovery | Reliability | ✅ |
| Monitoring with Amazon CloudWatch | Operations | ✅ |

### Kết quả đạt được tuần 4

**Kỹ năng kỹ thuật đã tiếp thu:**

*AWS Transit Gateway:*
- Thành thạo thiết lập và cấu hình Transit Gateway
- Hiểu các ưu điểm chính so với VPC Peering:
  - Hỗ trợ topology multi-VPC phức tạp (mô hình hub-and-spoke)
  - Cho phép transitive routing giữa các mạng được kết nối
  - Đơn giản hóa quản lý mạng ở quy mô lớn
  - Hỗ trợ VPN và Direct Connect attachments
- Học quản lý route table của Transit Gateway

*Amazon EC2 Deep Dive:*
- Hiểu toàn diện các tính năng chính của EC2:
  - **Elasticity**: Scale tài nguyên lên/xuống theo nhu cầu
  - **Cấu hình linh hoạt**: Nhiều instance types cho các workloads khác nhau
  - **Tối ưu chi phí**: Mô hình On-Demand, Reserved, Spot instances
- Thành thạo **EC2 Auto Scaling** cho điều chỉnh tài nguyên tự động
- Hiểu **Instance Store** như block storage tạm thời cho EC2
- Khám phá **Amazon Lightsail** như giải pháp đơn giản cho ứng dụng quy mô nhỏ
- Học về **Lightsail Containers** để triển khai container dễ dàng

*Dịch vụ Migration:*
- Hiểu **AWS Application Migration Service (MGN)** cho migration server
- Học **VM Import/Export** để migration máy ảo lên AWS
- Khám phá **Database Migration Service (DMS)** và **Schema Conversion Tool (SCT)**
- Nghiên cứu chiến lược disaster recovery với **AWS Elastic Disaster Recovery**

*DevOps và Monitoring:*
- Thành thạo các lệnh Git (commit, push, pull) và workflows nhóm
- Học CloudWatch cơ bản để monitor tài nguyên AWS

**Hợp tác nhóm:**
- Đề xuất thành công ý tưởng và phân công nhiệm vụ cho proposal
- Team sẵn sàng bắt đầu giai đoạn implementation
- Thiết lập vai trò và trách nhiệm rõ ràng cho từng thành viên

**Tiến độ Workshop - VPC & Network Setup:**
- Tạo VPC với CIDR `10.0.0.0/16` trong region ap-southeast-1
- Thiết kế kiến trúc subnet: Public subnets (10.0.1.0/24, 10.0.2.0/24) và Private subnets cho App (10.0.11.0/24, 10.0.12.0/24) và DB (10.0.21.0/24, 10.0.22.0/24) across hai AZs
- Cấu hình Internet Gateway cho public subnet internet access
- Thiết lập route tables cho proper traffic routing
- Bắt đầu cấu hình security groups cho multi-tier architecture

**Bài học chính:**
- Transit Gateway thiết yếu cho quản lý kiến trúc multi-VPC phức tạp
- EC2 Auto Scaling đảm bảo ứng dụng xử lý được tải biến động hiệu quả
- Lightsail hoàn hảo cho workloads đơn giản mà không cần sự phức tạp của AWS
- Dịch vụ migration cung cấp nhiều đường dẫn để di chuyển workloads lên AWS
- Thiết kế VPC với public/private subnets riêng biệt cung cấp security isolation
- Triển khai Multi-AZ đảm bảo high availability từ network layer
