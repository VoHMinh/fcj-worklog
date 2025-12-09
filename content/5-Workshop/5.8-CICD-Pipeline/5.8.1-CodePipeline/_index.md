---
title: "CodePipeline"
date:
weight: 1
chapter: false
pre: " <b> 5.8.1. </b> "
---

#### Overview

Create AWS CodePipeline for automated deployment workflow.

#### Create Pipeline

1. Navigate to **CodePipeline** → **Create pipeline**

| Setting | Value |
|---------|-------|
| **Pipeline name** | `ielts-deployment-pipeline` |
| **Service role** | New service role |

#### Source Stage

| Setting | Value |
|---------|-------|
| **Source provider** | GitHub (Version 2) |
| **Connection** | Create new connection |
| **Repository** | your-org/ielts-backend |
| **Branch** | main |
| **Trigger** | Push to branch |


#### Build Stage

| Setting | Value |
|---------|-------|
| **Build provider** | AWS CodeBuild |
| **Project name** | ielts-backend-build |

#### Deploy Stage

| Setting | Value |
|---------|-------|
| **Deploy provider** | Amazon ECS |
| **Cluster** | ielts-prod-cluster |
| **Service** | ielts-backend-service |

#### Next Steps

Proceed to [CodeBuild](../5.8.2-CodeBuild/).

