---
title: "CodePipeline"
date:
weight: 1
chapter: false
pre: " <b> 5.8.1. </b> "
---

#### Tổng Quan

Tạo AWS CodePipeline cho automated deployment workflow.

#### Tạo Pipeline

1. Điều hướng đến **CodePipeline** → **Create pipeline**

| Cài Đặt | Giá Trị |
|---------|-------|
| **Pipeline name** | `ielts-deployment-pipeline` |
| **Service role** | New service role |

#### Source Stage

| Cài Đặt | Giá Trị |
|---------|-------|
| **Source provider** | GitHub (Version 2) |
| **Connection** | Create new connection |
| **Repository** | your-org/ielts-backend |
| **Branch** | main |
| **Trigger** | Push to branch |


#### Build Stage

| Cài Đặt | Giá Trị |
|---------|-------|
| **Build provider** | AWS CodeBuild |
| **Project name** | ielts-backend-build |

#### Deploy Stage

| Cài Đặt | Giá Trị |
|---------|-------|
| **Deploy provider** | Amazon ECS |
| **Cluster** | ielts-prod-cluster |
| **Service** | ielts-backend-service |

#### Bước Tiếp Theo

Tiến hành đến [CodeBuild](../5.8.2-CodeBuild/).

