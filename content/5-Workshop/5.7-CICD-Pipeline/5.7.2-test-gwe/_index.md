---
title: "Create CodePipeline with GitLab tag trigger"
date: 
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

## CodePipeline Design (Source -> Build → Deploy)

### Build (CodeBuild)
- Project: the project’s CodeBuild project (Source = CodePipeline)
- Environment variables: as needed for this project’s build
- Buildspec: use the repository’s existing `buildspec.yml`

### CodePipeline Set up guidelines

1. On choosing pipeline creation option section, choose Build custom pipeline.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-1.png)
2. In pipeline settings tab, specify the pipeline name and use default settings for the pipeline.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-2.png)
3. Turn on webhook events and add filter type of tags. Make sure to set the tag patterns of "v*".
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-3.png)
4. Add frontend/backend project using AWS CodeBuild in build stage.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-4.png)
5. For the deploy stage, choose Amazon ECS as deploy provider. Specify its cluster and service to deploy. Also make sure to fill in the image definition file form like the image.
![CI/CD overview placeholder](/images/codepipeline-5.png)
6. Submit and then the pipeline is now created.
