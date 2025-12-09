---
title : "CI/CD with CodeBuild & CodePipeline"
date :   
weight : 7
chapter : false
pre : " <b> 5.7. </b> "
---

## CI/CD Pipeline with AWS CodeBuild & CodePipeline

This guideline describes how to implement a production-ready CI/CD pipeline with AWS CodePipeline and CodeBuild using GitLab as SCM. When a new Release is created in the GitLab repository, CodePipeline is triggered, CodeBuild runs the frontend and backend projects using the existing `frontend-buildspec.yml` and `backend-buildspec.yml`, and then CodePipeline deploys to ECS.

### What you’ll do
- 5.3.1 – Configure CodeBuild projects (frontend/backend) and trigger on GitLab Release
- 5.3.2 – Design CodePipeline for ECS deploy and integrate post-build artifacts

### Prerequisites
- An IAM user/role with permissions for CodeBuild, CodePipeline, S3, ECR (if needed for GitLab token), and IAM pass role.
- An S3 bucket for pipeline artifacts (will be created by CodePipeline wizard or you can pre-create).
- ECR repository created.

### Architecture Overview

![CI/CD overview placeholder](/images/2-Proposal/AWS-Bandup-Architecture.png)
