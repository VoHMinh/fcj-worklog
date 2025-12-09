---
title: "CI/CD Pipeline"
date:
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Tổng Quan

Thiết lập automated CI/CD pipeline sử dụng AWS CodePipeline và CodeBuild cho continuous deployment.

#### Kiến Trúc Pipeline

```
GitLab/GitHub → CodePipeline → CodeBuild → ECR → ECS
```

#### Nội Dung

1. [CodePipeline](5.8.1-CodePipeline/)
2. [CodeBuild](5.8.2-CodeBuild/)

#### Pipeline Stages

| Stage | Service | Action |
|-------|---------|--------|
| Source | GitHub/GitLab | Webhook trigger on push |
| Build | CodeBuild | Compile, test, build Docker image |
| Push | ECR | Push image to registry |
| Deploy | ECS | Update service với new image |

#### Thời Gian Ước Tính: ~30 phút

