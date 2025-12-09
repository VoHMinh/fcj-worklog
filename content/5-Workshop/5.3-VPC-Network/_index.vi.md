---
title: "VPC & Network Setup"
date:
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Tổng Quan

Trong phần này, bạn sẽ tạo hạ tầng mạng nền tảng cho Hệ Thống Tự Học IELTS. Kiến trúc mạng trải rộng trên hai Availability Zones (AZ-1 và AZ-2) để đảm bảo high availability và fault tolerance.

#### Kiến Trúc Mạng

Thiết kế VPC tuân theo các best practices của AWS cho ứng dụng multi-tier:

![Kiến Trúc Mạng](/images/2-Proposal/AWS-Bandup-Architecture.png)

**Cấu Hình VPC:**
- **CIDR Block**: `10.0.0.0/16` (65,536 địa chỉ IP)
- **Region**: ap-southeast-1 (Singapore)
- **Availability Zones**: 2 (AZ-1 và AZ-2)

#### Thiết Kế Subnet

| Loại Subnet | AZ-1 CIDR | AZ-2 CIDR | Mục Đích |
|-------------|-----------|-----------|---------|
| **Public Subnet** | 10.0.1.0/24 | 10.0.2.0/24 | ALB, NAT Gateway, Bastion |
| **Private Subnet (App)** | 10.0.11.0/24 | 10.0.12.0/24 | ECS Fargate Tasks (FE & BE) |
| **Private Subnet (DB)** | 10.0.21.0/24 | 10.0.22.0/24 | RDS, ElastiCache |

#### Các Thành Phần Mạng

**1. Internet Gateway**
- Cho phép truy cập internet cho resources trong public subnets
- Cần thiết để ALB nhận external traffic

**2. NAT Gateways**
- Triển khai trong public subnets (một cho mỗi AZ)
- Cho phép private subnet resources truy cập internet
- Được ECS tasks sử dụng để pull container images, truy cập external APIs

**3. Route Tables**
- **Public Route Table**: Routes `0.0.0.0/0` đến Internet Gateway
- **Private Route Tables**: Routes `0.0.0.0/0` đến NAT Gateway trong AZ tương ứng

**4. Security Groups**
- Defense-in-depth với nhiều layers
- Nguyên tắc least privilege access
- Separate security groups cho ALB, ECS, RDS, ElastiCache

**5. Network ACLs**
- Subnet-level firewall rules
- Layer bảo mật bổ sung ngoài security groups

#### Những Gì Bạn Sẽ Tạo

Trong phần này, bạn sẽ:

1. Tạo VPC với DNS support enabled
2. Tạo public và private subnets trên hai AZs
3. Thiết lập Internet Gateway
4. Triển khai NAT Gateways cho private subnet internet access
5. Cấu hình route tables cho proper traffic flow
6. Tạo security groups cho mỗi tier
7. (Tùy chọn) Thiết lập VPC Flow Logs cho network monitoring

#### Thời Gian Ước Tính

| Tác Vụ | Thời Gian |
|------|------|
| Tạo VPC | 5 phút |
| Cấu hình Subnets | 10 phút |
| Thiết lập Security Groups | 10 phút |
| Cấu hình NAT Gateway | 5 phút |
| **Tổng** | **~30 phút** |

#### Nội Dung

1. [Tạo VPC](5.3.1-Create-VPC/)
2. [Cấu Hình Subnets](5.3.2-Subnets-Configuration/)
3. [Security Groups](5.3.3-Security-Groups/)
4. [NAT Gateway](5.3.4-NAT-Gateway/)

#### Best Practices

{{% notice tip %}}
**Network Design Best Practices:**
- Luôn sử dụng multiple AZs cho production workloads
- Giữ database subnets isolated không có direct internet access
- Sử dụng separate security groups cho mỗi tier (web, app, data)
- Enable VPC Flow Logs cho security analysis và troubleshooting
- Sử dụng descriptive naming conventions cho tất cả resources
{{% /notice %}}

