---
title: "ECS Cluster"
date:
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Tổng Quan

ECS Cluster là logical grouping của tasks và services. Trong bước này, bạn sẽ tạo ECS cluster với Fargate capacity providers và cấu hình Service Connect cho internal service discovery.

#### Tạo ECS Cluster

**Bước 1: Điều Hướng đến ECS Console**

1. Vào **Amazon ECS** → **Clusters**
2. Click **Create cluster**

**Bước 2: Cấu Hình Cluster Settings**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Cluster name** | `ielts-prod-cluster` |
| **Infrastructure** | AWS Fargate (serverless) |

**Bước 3: Cấu Hình Monitoring (Tùy Chọn)**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Container Insights** | Enabled |

**Bước 4: Cấu Hình Service Connect Namespace**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Namespace** | Create new namespace |
| **Namespace name** | `ielts.local` |

Click **Create**.


#### AWS CLI Command

```bash
# Tạo cluster với Container Insights
aws ecs create-cluster \
    --cluster-name ielts-prod-cluster \
    --capacity-providers FARGATE FARGATE_SPOT \
    --default-capacity-provider-strategy \
        capacityProvider=FARGATE,weight=1,base=1 \
        capacityProvider=FARGATE_SPOT,weight=1 \
    --settings name=containerInsights,value=enabled \
    --service-connect-defaults namespace=ielts.local

# Tạo Cloud Map namespace cho Service Connect
aws servicediscovery create-private-dns-namespace \
    --name ielts.local \
    --vpc $VPC_ID \
    --region ap-southeast-1
```

#### Cấu Hình Capacity Providers

Capacity providers định nghĩa infrastructure cho running tasks:

| Provider | Use Case | Cost |
|----------|----------|------|
| **FARGATE** | Production workloads | Standard pricing |
| **FARGATE_SPOT** | Fault-tolerant workloads | Giảm đến 70% |

**Capacity Provider Strategy:**

```json
{
    "capacityProviderStrategy": [
        {
            "capacityProvider": "FARGATE",
            "weight": 1,
            "base": 1
        },
        {
            "capacityProvider": "FARGATE_SPOT",
            "weight": 1,
            "base": 0
        }
    ]
}
```

Điều này đảm bảo:
- Ít nhất 1 task chạy trên FARGATE (production stability)
- Các tasks bổ sung có thể dùng FARGATE_SPOT để tiết kiệm chi phí

#### Cấu Hình Service Connect

Service Connect cho phép service-to-service communication sử dụng DNS names:

**Namespace Configuration:**
- **Namespace**: `ielts.local`
- **Type**: Private DNS (AWS Cloud Map)
- **VPC**: ielts-prod-vpc

**Service Endpoints:**
| Service | DNS Name | Port |
|---------|----------|------|
| Frontend | `frontend.ielts.local` | 3000 |
| Backend | `backend.ielts.local` | 8080 |

{{% notice tip %}}
Với Service Connect, frontend có thể gọi backend sử dụng `http://backend.ielts.local:8080` mà không cần hardcode IP addresses.
{{% /notice %}}

#### Xác Minh Cluster Creation

**Console Verification:**

1. Điều hướng đến **ECS** → **Clusters**
2. Click vào `ielts-prod-cluster`
3. Xác minh:
   - Status: **Active**
   - Capacity providers: FARGATE, FARGATE_SPOT
   - Container Insights: Enabled
   - Namespace: ielts.local


**CLI Verification:**

```bash
# Describe cluster
aws ecs describe-clusters \
    --clusters ielts-prod-cluster \
    --include ATTACHMENTS SETTINGS

# List cluster services (sẽ trống ban đầu)
aws ecs list-services --cluster ielts-prod-cluster
```

#### Tóm Tắt Cluster Settings

| Cài Đặt | Giá Trị |
|---------|-------|
| Cluster Name | ielts-prod-cluster |
| Capacity Providers | FARGATE, FARGATE_SPOT |
| Container Insights | Enabled |
| Service Connect Namespace | ielts.local |
| Default Launch Type | Fargate |

#### Kiến Trúc Cluster

```
ielts-prod-cluster
├── Capacity Providers
│   ├── FARGATE (base: 1)
│   └── FARGATE_SPOT (weight: 1)
├── Service Connect
│   └── Namespace: ielts.local
│       ├── frontend.ielts.local:3000
│       └── backend.ielts.local:8080
└── Services
    ├── ielts-frontend-service
    └── ielts-backend-service
```

#### Best Practices

{{% notice tip %}}
**ECS Cluster Best Practices:**
- Enable Container Insights cho detailed monitoring
- Sử dụng capacity provider strategy để cân bằng cost và reliability
- Cấu hình Service Connect cho internal service discovery
- Sử dụng meaningful naming conventions
- Xem xét FARGATE_SPOT cho non-critical workloads
{{% /notice %}}

#### Bước Tiếp Theo

Tiến hành đến [ECS Service](../5.4.4-ECS-Service/) để deploy Frontend và Backend services.

