---
title: "NAT Gateway"
date:
weight: 4
chapter: false
pre: " <b> 5.3.4. </b> "
---

#### Tổng Quan

NAT (Network Address Translation) Gateways cho phép resources trong private subnets truy cập internet trong khi ngăn chặn inbound connections từ internet. Điều này cần thiết để ECS tasks pull container images, truy cập external APIs, và download updates.

#### Kiến Trúc NAT Gateway

Trong thiết kế Multi-AZ, chúng ta triển khai một NAT Gateway cho mỗi Availability Zone:

| NAT Gateway | Subnet | Mục Đích |
|-------------|--------|---------|
| `ielts-prod-nat-1a` | Public Subnet 1a | Outbound cho AZ-1 private subnets |
| `ielts-prod-nat-1b` | Public Subnet 1b | Outbound cho AZ-2 private subnets |



#### Tại Sao Hai NAT Gateways?

**High Availability:**
- Nếu AZ-1 fail, resources trong AZ-2 vẫn có thể truy cập internet
- Không có cross-AZ traffic cho outbound requests (giảm latency và cost)

**Cost Consideration:**
- Mỗi NAT Gateway tốn ~$0.045/giờ + data processing fees
- Cho development, bạn có thể dùng một NAT Gateway để giảm chi phí
- Cho production, luôn dùng một cho mỗi AZ cho high availability

#### Tạo NAT Gateway (Nếu Chưa Được Tạo bởi VPC Wizard)

**Bước 1: Allocate Elastic IP**

1. Điều hướng đến **VPC** → **Elastic IPs**
2. Click **Allocate Elastic IP address**
3. Giữ default settings và click **Allocate**
4. Ghi lại Allocation ID

Lặp lại cho NAT Gateway thứ hai.

**Bước 2: Tạo NAT Gateway trong AZ-1**

1. Điều hướng đến **VPC** → **NAT Gateways**
2. Click **Create NAT gateway**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Name** | `ielts-prod-nat-1a` |
| **Subnet** | ielts-prod-public-1a |
| **Connectivity type** | Public |
| **Elastic IP allocation ID** | Chọn EIP đã allocate |

3. Click **Create NAT gateway**

**Bước 3: Tạo NAT Gateway trong AZ-2**

Lặp lại quy trình cho AZ-2:

| Cài Đặt | Giá Trị |
|---------|-------|
| **Name** | `ielts-prod-nat-1b` |
| **Subnet** | ielts-prod-public-1b |
| **Connectivity type** | Public |
| **Elastic IP allocation ID** | Chọn EIP thứ hai đã allocate |



#### Cập Nhật Private Route Tables

Sau khi NAT Gateways được tạo, cập nhật private route tables:

**Private Route Table AZ-1:**

1. Điều hướng đến **VPC** → **Route Tables**
2. Chọn `ielts-prod-private-rt-1a`
3. Vào tab **Routes** → **Edit routes**
4. Thêm route:
   - **Destination**: `0.0.0.0/0`
   - **Target**: NAT Gateway `ielts-prod-nat-1a`
5. Click **Save changes**

**Private Route Table AZ-2:**

Lặp lại cho AZ-2 với `ielts-prod-nat-1b`.



#### AWS CLI Commands

```bash
# Allocate Elastic IPs
EIP1=$(aws ec2 allocate-address --query 'AllocationId' --output text)
EIP2=$(aws ec2 allocate-address --query 'AllocationId' --output text)

# Lấy public subnet IDs
SUBNET_1A=$(aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=ielts-prod-public-1a" \
    --query 'Subnets[0].SubnetId' --output text)

SUBNET_1B=$(aws ec2 describe-subnets \
    --filters "Name=tag:Name,Values=ielts-prod-public-1b" \
    --query 'Subnets[0].SubnetId' --output text)

# Tạo NAT Gateway trong AZ-1
NAT1=$(aws ec2 create-nat-gateway \
    --subnet-id $SUBNET_1A \
    --allocation-id $EIP1 \
    --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=ielts-prod-nat-1a}]' \
    --query 'NatGateway.NatGatewayId' --output text)

# Tạo NAT Gateway trong AZ-2
NAT2=$(aws ec2 create-nat-gateway \
    --subnet-id $SUBNET_1B \
    --allocation-id $EIP2 \
    --tag-specifications 'ResourceType=natgateway,Tags=[{Key=Name,Value=ielts-prod-nat-1b}]' \
    --query 'NatGateway.NatGatewayId' --output text)

# Đợi NAT Gateways available
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT1 $NAT2

# Cập nhật private route tables
# Lấy route table IDs và thêm routes
```

#### Xác Minh NAT Gateway Configuration

**Bước 1: Kiểm Tra NAT Gateway Status**

1. Điều hướng đến **VPC** → **NAT Gateways**
2. Xác minh cả hai NAT Gateways hiển thị status **Available**
3. Ghi lại Elastic IP addresses được assign

**Bước 2: Xác Minh Route Tables**

1. Điều hướng đến **VPC** → **Route Tables**
2. Chọn mỗi private route table
3. Xác minh route `0.0.0.0/0` trỏ đến NAT Gateway đúng

**Bước 3: Test Connectivity (Sau ECS Setup)**

Từ ECS task trong private subnet:
```bash
# Test internet connectivity
curl -I https://www.google.com

# Test AWS API access
aws sts get-caller-identity
```

#### NAT Gateway vs NAT Instance

| Tính Năng | NAT Gateway | NAT Instance |
|---------|-------------|--------------|
| **Availability** | Highly available trong AZ | Single point of failure |
| **Bandwidth** | Lên đến 100 Gbps | Phụ thuộc instance type |
| **Maintenance** | Managed bởi AWS | User managed |
| **Cost** | ~$32/tháng + data | Instance cost + data |
| **Security Groups** | Không hỗ trợ | Hỗ trợ |
| **Recommendation** | Production workloads | Dev/test only |

{{% notice tip %}}
Cho production, luôn dùng NAT Gateways. Cho development environments với budget hạn chế, xem xét NAT Instances hoặc một NAT Gateway duy nhất.
{{% /notice %}}

#### Tối Ưu Chi Phí

**Development Environment:**
- Dùng một NAT Gateway trong một AZ
- Schedule NAT Gateway deletion trong off-hours (dùng automation)
- Xem xét VPC endpoints cho AWS services để giảm data transfer

**Production Environment:**
- Một NAT Gateway mỗi AZ là bắt buộc cho HA
- Dùng VPC endpoints cho frequently accessed AWS services
- Monitor data transfer costs trong CloudWatch

#### VPC Endpoints (Tối Ưu Chi Phí Tùy Chọn)

Tạo VPC endpoints để tránh NAT Gateway data charges cho AWS services:

```bash
# S3 Gateway Endpoint (miễn phí)
aws ec2 create-vpc-endpoint \
    --vpc-id $VPC_ID \
    --service-name com.amazonaws.ap-southeast-1.s3 \
    --route-table-ids $PRIVATE_RT_1A $PRIVATE_RT_1B

# ECR API Interface Endpoint
aws ec2 create-vpc-endpoint \
    --vpc-id $VPC_ID \
    --service-name com.amazonaws.ap-southeast-1.ecr.api \
    --vpc-endpoint-type Interface \
    --subnet-ids $PRIVATE_APP_1A $PRIVATE_APP_1B \
    --security-group-ids $VPC_ENDPOINT_SG

# ECR DKR Interface Endpoint
aws ec2 create-vpc-endpoint \
    --vpc-id $VPC_ID \
    --service-name com.amazonaws.ap-southeast-1.ecr.dkr \
    --vpc-endpoint-type Interface \
    --subnet-ids $PRIVATE_APP_1A $PRIVATE_APP_1B \
    --security-group-ids $VPC_ENDPOINT_SG
```

#### Tóm Tắt Cấu Hình Network

Sau khi hoàn thành phần này, VPC của bạn đã được cấu hình đầy đủ:

| Thành Phần | Số Lượng | Trạng Thái |
|-----------|-------|--------|
| VPC | 1 | ✅ Created |
| Internet Gateway | 1 | ✅ Attached |
| NAT Gateways | 2 | ✅ Available |
| Public Subnets | 2 | ✅ Configured |
| Private App Subnets | 2 | ✅ Configured |
| Private DB Subnets | 2 | ✅ Configured |
| Security Groups | 5 | ✅ Created |
| Route Tables | 3+ | ✅ Configured |

#### Bước Tiếp Theo

Hạ tầng network giờ đã hoàn thành. Tiến hành đến [ECS & Container Setup](../../5.4-ECS-Setup/) để triển khai application containers.

