---
title: "CI/CD Pipeline"
date:
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Overview

Set up automated CI/CD pipeline using AWS CodePipeline and CodeBuild for continuous deployment.

#### Pipeline Architecture

```
GitLab/GitHub → CodePipeline → CodeBuild → ECR → ECS
```

#### Content

1. [CodePipeline](5.8.1-CodePipeline/)
2. [CodeBuild](5.8.2-CodeBuild/)

#### Pipeline Stages

| Stage | Service | Action |
|-------|---------|--------|
| Source | GitHub/GitLab | Webhook trigger on push |
| Build | CodeBuild | Compile, test, build Docker image |
| Push | ECR | Push image to registry |
| Deploy | ECS | Update service with new image |

#### Estimated Time: ~30 minutes

