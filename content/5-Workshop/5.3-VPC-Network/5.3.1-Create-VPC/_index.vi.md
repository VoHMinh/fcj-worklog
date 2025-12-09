---
title: "Tạo VPC"
date:
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Tổng Quan

Trong bước này, bạn sẽ tạo Virtual Private Cloud (VPC) làm môi trường mạng isolated cho tất cả resources của Hệ Thống Tự Học IELTS.

#### Tạo VPC

**Bước 1: Điều Hướng đến VPC Console**

1. Đăng nhập vào AWS Management Console
2. Điều hướng đến dịch vụ **VPC**
3. Click **Create VPC**

**Bước 2: Cấu Hình VPC Settings**

Chọn **VPC and more** để tạo VPC với tất cả components cần thiết tự động:

| Cài Đặt | Giá Trị |
|---------|-------|
| **Name tag auto-generation** | `ielts-prod` |
| **IPv4 CIDR block** | `10.0.0.0/16` |
| **IPv6 CIDR block** | No IPv6 CIDR block |
| **Tenancy** | Default |
| **Number of Availability Zones** | 2 |
| **Number of public subnets** | 2 |
| **Number of private subnets** | 4 |
| **NAT gateways** | 1 per AZ |
| **VPC endpoints** | None (chúng ta sẽ thêm sau) |
| **DNS hostnames** | Enable |
| **DNS resolution** | Enable |



**Bước 3: Review và Create**

1. Review panel **Preview** hiển thị VPC topology
2. Xác minh tất cả settings đúng
3. Click **Create VPC**

Quá trình tạo sẽ tự động tạo:
- 1 VPC
- 2 Public subnets
- 4 Private subnets (2 cho app tier, 2 cho database tier)
- 1 Internet Gateway
- 2 NAT Gateways (một cho mỗi AZ)
- Route tables cho mỗi subnet tier



#### Thay Thế: Tạo VPC Sử Dụng AWS CLI

Nếu bạn muốn sử dụng CLI:

```bash
# Tạo VPC
aws ec2 create-vpc \
    --cidr-block 10.0.0.0/16 \
    --tag-specifications 'ResourceType=vpc,Tags=[{Key=Name,Value=ielts-prod-vpc}]' \
    --query 'Vpc.VpcId' \
    --output text

# Enable DNS hostnames
aws ec2 modify-vpc-attribute \
    --vpc-id <vpc-id> \
    --enable-dns-hostnames '{"Value": true}'

# Enable DNS resolution
aws ec2 modify-vpc-attribute \
    --vpc-id <vpc-id> \
    --enable-dns-support '{"Value": true}'
```

#### Xác Minh VPC Creation

Sau khi tạo, xác minh các điều sau trong VPC console:

1. **VPC Details**
   - CIDR: `10.0.0.0/16`
   - DNS hostnames: Enabled
   - DNS resolution: Enabled

2. **Resource Map**
   - Hiển thị tất cả created subnets
   - Hiển thị Internet Gateway attachment
   - Hiển thị NAT Gateway placements



#### Naming Convention

Chúng ta sử dụng naming convention sau cho tất cả VPC resources:

```
{project}-{environment}-{resource-type}-{az}
```

Ví dụ:
- `ielts-prod-vpc`
- `ielts-prod-public-subnet-1a`
- `ielts-prod-private-app-subnet-1a`
- `ielts-prod-private-db-subnet-1a`
- `ielts-prod-igw`
- `ielts-prod-nat-1a`

#### Tóm Tắt Cấu Hình VPC

| Resource | Số Lượng | Chi Tiết |
|----------|-------|---------|
| VPC | 1 | CIDR: 10.0.0.0/16 |
| Internet Gateway | 1 | Attached to VPC |
| NAT Gateway | 2 | Một mỗi AZ trong public subnet |
| Public Subnets | 2 | Cho ALB, NAT GW |
| Private App Subnets | 2 | Cho ECS tasks |
| Private DB Subnets | 2 | Cho RDS, ElastiCache |
| Route Tables | 3+ | Public, Private mỗi AZ |

#### Best Practices Áp Dụng

{{% notice tip %}}
- **DNS Support**: Enabled cho service discovery và Route 53 integration
- **Multiple AZs**: Resources trải rộng trên 2 AZs cho high availability
- **CIDR Planning**: CIDR block đủ lớn cho future growth
- **Naming Convention**: Naming nhất quán cho dễ quản lý
{{% /notice %}}

#### Troubleshooting

**Vấn đề: VPC creation fails**
- Kiểm tra IAM permissions cho VPC creation
- Xác minh bạn chưa đạt VPC quota limits (mặc định: 5 VPCs mỗi region)

**Vấn đề: DNS không resolve**
- Đảm bảo DNS hostnames enabled
- Đảm bảo DNS resolution enabled

#### Bước Tiếp Theo

Tiến hành đến [Subnets Configuration](../5.3.2-Subnets-Configuration/) để review và cấu hình subnet settings.

