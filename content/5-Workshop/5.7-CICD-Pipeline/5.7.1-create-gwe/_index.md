---
title: "Connect GitLab repo & create CodeBuild project"
date: 
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

## Objective
Configure two CodeBuild projects (frontend and backend) and a trigger from GitLab Release events that starts CodePipeline. CodePipeline invokes CodeBuild using the repository’s existing `frontend-buildspec.yml` and `backend-buildspec.yml`, and then deploys to ECS.

## AWS Resources
- CodeBuild projects:
	- Frontend: Source = CodePipeline; Buildspec = `frontend-buildspec.yml`
	- Backend: Source = CodePipeline; Buildspec = `backend-buildspec.yml`
- CodePipeline (later step) consuming artifacts and deploying to ECS

### Create CodeBuild Projects & Connect GitLab Repository

1. In the creating new CodeBuild Project configuration section, select Default project.
![CI/CD overview placeholder](images/5-Workshop/5.7-CICD/codebuild-1.png)
1. In the Source section, choose GitLab and Band-Up repository.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-2.png)
1. Leave default configurations for Environment.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-3.png)
1. Specify Band-Up own buildspec for frontend/backend like the following image:
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-4.png)
1. Submit and create the other frontend/backend CodeBuild project likewise.

## Summary
You now have two CodeBuild projects (frontend and backend) ready to be invoked by CodePipeline. A GitLab Release event can trigger CodePipeline, which then runs each project with its corresponding `frontend-buildspec.yml` and `backend-buildspec.yml`. In subsequent steps, CodePipeline will take the build artifacts and deploy to ECS.
