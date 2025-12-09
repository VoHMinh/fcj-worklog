---
title: "Cấu Hình Subnets"
date:
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Tổng Quan

Trong bước này, bạn sẽ review và cấu hình các subnets được tạo bởi VPC wizard. Thiết kế subnet tuân theo three-tier architecture pattern với proper isolation giữa các layers.

#### Kiến Trúc Subnet

VPC chứa sáu subnets trên hai Availability Zones:

| Tên Subnet | CIDR | AZ | Loại | Mục Đích |
|-------------|------|-----|------|---------|
| `ielts-prod-public-1a` | 10.0.1.0/24 | ap-southeast-1a | Public | ALB, NAT Gateway |
| `ielts-prod-public-1b` | 10.0.2.0/24 | ap-southeast-1b | Public | ALB (standby) |
| `ielts-prod-private-app-1a` | 10.0.11.0/24 | ap-southeast-1a | Private | ECS Frontend & Backend (Active) |
| `ielts-prod-private-app-1b` | 10.0.12.0/24 | ap-southeast-1b | Private | ECS Frontend & Backend (Standby) |
| `ielts-prod-private-db-1a` | 10.0.21.0/24 | ap-southeast-1a | Private | RDS Primary, ElastiCache |
| `ielts-prod-private-db-1b` | 10.0.22.0/24 | ap-southeast-1b | Private | RDS Standby |


#### Cấu Hình Public Subnets

Public subnets phải có **Auto-assign public IPv4 address** enabled:

**Bước 1: Chọn Public Subnet**
1. Điều hướng đến **VPC** → **Subnets**
2. Chọn `ielts-prod-public-1a`
3. Click **Actions** → **Edit subnet settings**

**Bước 2: Enable Auto-assign Public IP**
1. Check **Enable auto-assign public IPv4 address**
2. Click **Save**

**Bước 3: Lặp Lại cho Public Subnet Thứ Hai**
1. Lặp lại các bước cho `ielts-prod-public-1b`



#### Cấu Hình Private Subnets

Private subnets KHÔNG nên có public IP auto-assignment:

1. Xác minh `ielts-prod-private-app-1a` có auto-assign disabled
2. Xác minh `ielts-prod-private-app-1b` có auto-assign disabled
3. Xác minh `ielts-prod-private-db-1a` có auto-assign disabled
4. Xác minh `ielts-prod-private-db-1b` có auto-assign disabled

#### Tạo Route Tables

Nếu chưa được tạo bởi wizard, tạo route tables thủ công:

**Public Route Table:**

```bash
# Tạo public route table
aws ec2 create-route-table \
    --vpc-id <vpc-id> \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=ielts-prod-public-rt}]'

# Thêm route đến Internet Gateway
aws ec2 create-route \
    --route-table-id <rtb-id> \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id <igw-id>

# Associate với public subnets
aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <public-subnet-1a-id>

aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <public-subnet-1b-id>
```

**Private Route Tables (mỗi AZ):**

```bash
# Tạo private route table cho AZ-1
aws ec2 create-route-table \
    --vpc-id <vpc-id> \
    --tag-specifications 'ResourceType=route-table,Tags=[{Key=Name,Value=ielts-prod-private-rt-1a}]'

# Thêm route đến NAT Gateway trong AZ-1
aws ec2 create-route \
    --route-table-id <rtb-id> \
    --destination-cidr-block 0.0.0.0/0 \
    --nat-gateway-id <nat-1a-id>

# Associate với private subnets trong AZ-1
aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <private-app-1a-id>

aws ec2 associate-route-table \
    --route-table-id <rtb-id> \
    --subnet-id <private-db-1a-id>
```

#### Xác Minh Route Table Configuration

**Public Route Table nên có:**

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | igw-xxx |

**Private Route Table (AZ-1) nên có:**

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | nat-xxx (trong AZ-1) |

**Private Route Table (AZ-2) nên có:**

| Destination | Target |
|-------------|--------|
| 10.0.0.0/16 | local |
| 0.0.0.0/0 | nat-xxx (trong AZ-2) |



#### Subnet Tagging cho ECS và ALB

Thêm required tags cho ECS và ALB subnet discovery:

**Cho Public Subnets (ALB):**
```bash
aws ec2 create-tags \
    --resources <public-subnet-1a-id> <public-subnet-1b-id> \
    --tags Key=kubernetes.io/role/elb,Value=1
```

**Cho Private Subnets (ECS):**
```bash
aws ec2 create-tags \
    --resources <private-app-1a-id> <private-app-1b-id> \
    --tags Key=kubernetes.io/role/internal-elb,Value=1
```

#### Dung Lượng Địa Chỉ IP

Mỗi /24 subnet cung cấp:
- 256 tổng địa chỉ IP
- 251 địa chỉ IP khả dụng (AWS reserve 5 IPs mỗi subnet)

| Subnet | IPs Khả Dụng | Dành Cho |
|--------|------------|--------------|
| Public 1a | 251 | NAT GW, ALB, Bastion |
| Public 1b | 251 | ALB, Bastion |
| Private App 1a | 251 | ECS Tasks (Active) |
| Private App 1b | 251 | ECS Tasks (Standby) |
| Private DB 1a | 251 | RDS Primary, ElastiCache |
| Private DB 1b | 251 | RDS Standby |

{{% notice tip %}}
Với 251 IPs khả dụng mỗi app subnet, bạn có thể chạy khoảng 100+ ECS tasks mỗi AZ (tính cả ENI attachments và reserved IPs).
{{% /notice %}}

#### Network ACLs (Tùy Chọn)

Cho bảo mật bổ sung, cấu hình Network ACLs:

**Public Subnet NACL:**
- Allow inbound HTTP/HTTPS (80, 443)
- Allow inbound ephemeral ports (1024-65535)
- Allow all outbound

**Private Subnet NACL:**
- Allow inbound từ VPC CIDR
- Allow inbound ephemeral ports
- Allow outbound đến VPC và internet (qua NAT)

{{% notice warning %}}
Network ACLs là stateless. Đảm bảo bạn cấu hình cả inbound và outbound rules cho return traffic.
{{% /notice %}}

#### Tóm Tắt Cấu Hình Subnet

| Configuration | Public | Private App | Private DB |
|---------------|--------|-------------|------------|
| Auto-assign public IP | Yes | No | No |
| Internet access | Direct (IGW) | Via NAT | Via NAT |
| Route to internet | igw-xxx | nat-xxx | nat-xxx |
| Purpose | ALB, NAT | ECS Tasks | RDS, Cache |

#### Bước Tiếp Theo

Tiến hành đến [Security Groups](../5.3.3-Security-Groups/) để cấu hình firewall rules cho mỗi tier.

