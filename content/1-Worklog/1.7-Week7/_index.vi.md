---
title: "Worklog Tuần 7"
date: 2024-10-21
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu tuần 7

- **Tập trung ôn tập toàn diện và củng cố kiến thức** chuẩn bị cho kỳ thi giữa kỳ.
- Luyện tập các bài lab và câu hỏi trắc nghiệm trên các nền tảng **AWS Builders** và **AWSboy** để làm quen với format đề thi.
- Hệ thống hóa các dịch vụ **AWS cơ bản** đã học: EC2, S3, VPC, IAM, RDS, Lambda, DynamoDB.

---

### Các công việc đã hoàn thành trong tuần

| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| :--- | :--- | :--- | :--- | :--- |
| 2 | - Hệ thống hóa kiến thức **dịch vụ Compute** (EC2, Lambda). <br> - **Thực hành:** Hoàn thành bài tập/lab về tạo, cấu hình, quản lý vòng đời của **EC2 Instances**. <br> - Ôn tập tạo Lambda function, triggers, và execution models. | 22/10/2024 | 22/10/2024 | AWS Builders, AWSboy |
| 3 | - Ôn tập kiến thức **dịch vụ Storage** (S3, EBS, EFS). <br> - **Thực hành:** Hoàn thành bài tập về S3 storage classes (Standard, IA, Glacier), EBS volume types, và EFS use cases. <br> - Thực hành S3 bucket policies và access control. | 23/10/2024 | 23/10/2024 | AWS Builders, AWSboy |
| 4 | - Củng cố kiến thức về **Networking** (VPC, Subnets, Route Tables, Internet Gateway, Security Groups, NACLs). <br> - **Thực hành:** Luyện tập câu hỏi về cấu hình VPC, Security Group rules vs NACL rules, và routing principles. <br> - Ôn tập VPC Peering và Transit Gateway concepts. | 24/10/2024 | 24/10/2024 | AWS Builders, AWSboy |
| 5 | - Ôn tập **Database services** (RDS, DynamoDB) và **Security/Identity** (IAM). <br> - **Thực hành:** Tập trung vào các khái niệm **IAM Policies**, **IAM Roles**, và **IAM Users** cơ bản. <br> - Thực hành thiết kế DynamoDB table và cấu hình RDS instance. | 25/10/2024 | 25/10/2024 | AWS Builders, AWSboy |
| 6 | - **Tổng kết và Luyện đề:** Làm các **bài thi thử toàn diện** trên nền tảng AWS Builders và AWSboy. <br> - **Rà soát** các lĩnh vực yếu được xác định trong bài thi thử để nghiên cứu thêm. <br> - Tạo ghi chú tóm tắt để tham khảo nhanh trước kỳ thi. | 26/10/2024 | 26/10/2024 | AWS Builders, AWSboy |

---

### Kết quả đạt được tuần 7

- Hoàn thành **ôn tập toàn diện** các nhóm dịch vụ AWS cơ bản: Compute, Storage, Networking, Database, Security (IAM).
- **Luyện tập thành công** nhiều labs và câu hỏi trắc nghiệm trên các nền tảng **AWS Builders** và **AWSboy**.
- **Nắm vững** các thông số cơ bản của **EC2** (Instance Types, AMI, EBS volumes) và hoạt động của **S3** (Storage Classes, Object/Bucket management).
- **Hiểu rõ** mối quan hệ và cấu hình của các thành phần trong **VPC** (Public/Private Subnets, Routing, Security Groups vs NACLs).
- **Tự tin hơn** với kiến thức đã học, sẵn sàng cho kỳ thi giữa kỳ sắp tới.
- Tạo ghi chú học tập toàn diện bao gồm tất cả các danh mục dịch vụ AWS chính.
- Xác định và giải quyết các khoảng trống kiến thức thông qua các phiên thực hành có mục tiêu.

**Bài học chính:**
- Security Groups là stateful (return traffic tự động được phép), NACLs là stateless (cần rules hai chiều)
- EC2 instance types được tối ưu cho các workloads khác nhau (compute, memory, storage, GPU)
- S3 storage classes cân bằng chi phí vs. tần suất truy cập
- IAM policies tuân theo nguyên tắc explicit deny - policy hạn chế nhất thắng
- VPC routing tuân theo nguyên tắc most specific route match
