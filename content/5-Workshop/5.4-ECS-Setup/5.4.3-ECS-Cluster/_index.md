---
title: "ECS Cluster"
date:
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Overview

An ECS Cluster is a logical grouping of tasks and services. In this step, you will create an ECS cluster with Fargate capacity providers and configure Service Connect for internal service discovery.

#### Create ECS Cluster

**Step 1: Navigate to ECS Console**

1. Go to **Amazon ECS** → **Clusters**
2. Click **Create cluster**

**Step 2: Configure Cluster Settings**

| Setting | Value |
|---------|-------|
| **Cluster name** | `ielts-prod-cluster` |
| **Infrastructure** | AWS Fargate (serverless) |

**Step 3: Configure Monitoring (Optional)**

| Setting | Value |
|---------|-------|
| **Container Insights** | Enabled |

**Step 4: Configure Service Connect Namespace**

| Setting | Value |
|---------|-------|
| **Namespace** | Create new namespace |
| **Namespace name** | `ielts.local` |

Click **Create**.

#### AWS CLI Command

```bash
# Create cluster with Container Insights
aws ecs create-cluster \
    --cluster-name ielts-prod-cluster \
    --capacity-providers FARGATE FARGATE_SPOT \
    --default-capacity-provider-strategy \
        capacityProvider=FARGATE,weight=1,base=1 \
        capacityProvider=FARGATE_SPOT,weight=1 \
    --settings name=containerInsights,value=enabled \
    --service-connect-defaults namespace=ielts.local

# Create Cloud Map namespace for Service Connect
aws servicediscovery create-private-dns-namespace \
    --name ielts.local \
    --vpc $VPC_ID \
    --region ap-southeast-1
```

#### Configure Capacity Providers

Capacity providers define the infrastructure for running tasks:

| Provider | Use Case | Cost |
|----------|----------|------|
| **FARGATE** | Production workloads | Standard pricing |
| **FARGATE_SPOT** | Fault-tolerant workloads | Up to 70% discount |

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

This ensures:
- At least 1 task runs on FARGATE (production stability)
- Additional tasks can use FARGATE_SPOT for cost savings

#### Service Connect Configuration

Service Connect enables service-to-service communication using DNS names:

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
With Service Connect, the frontend can call the backend using `http://backend.ielts.local:8080` without hardcoding IP addresses.
{{% /notice %}}

#### Verify Cluster Creation

**Console Verification:**

1. Navigate to **ECS** → **Clusters**
2. Click on `ielts-prod-cluster`
3. Verify:
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

# List cluster services (should be empty initially)
aws ecs list-services --cluster ielts-prod-cluster
```

#### Cluster Settings Summary

| Setting | Value |
|---------|-------|
| Cluster Name | ielts-prod-cluster |
| Capacity Providers | FARGATE, FARGATE_SPOT |
| Container Insights | Enabled |
| Service Connect Namespace | ielts.local |
| Default Launch Type | Fargate |

#### Cluster Architecture

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
- Enable Container Insights for detailed monitoring
- Use capacity provider strategy to balance cost and reliability
- Configure Service Connect for internal service discovery
- Use meaningful naming conventions
- Consider FARGATE_SPOT for non-critical workloads
{{% /notice %}}

#### Next Steps

Proceed to [ECS Service](../5.4.4-ECS-Service/) to deploy the Frontend and Backend services.

