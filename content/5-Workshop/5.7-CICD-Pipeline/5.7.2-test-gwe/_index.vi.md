---
title: "Tạo CodePipeline với trigger theo tag GitLab"
date: 
weight: 2
chapter: false
pre: " <b> 5.7.2 </b> "
---

## Thiết kế CodePipeline (Source → Build → Deploy)

### Build (CodeBuild)
- Project: dự án CodeBuild của dự án này (Source = CodePipeline)
- Environment variables: theo nhu cầu build của dự án
- Buildspec: dùng `buildspec.yml` có sẵn trong repository

### Hướng dẫn tạo CodePipeline

1. Ở phần chọn tùy chọn tạo pipeline, chọn Build custom pipeline.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-1.png)
2. Trong tab pipeline settings, đặt tên pipeline và dùng các thiết lập mặc định.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-2.png)
3. Bật webhook events và thêm bộ lọc theo tag. Đặt mẫu tag là "v*".
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-3.png)
4. Thêm dự án frontend/backend bằng AWS CodeBuild ở bước Build.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-4.png)
5. Ở bước Deploy, chọn Amazon ECS làm nhà cung cấp triển khai. Chỉ định cluster và service để deploy. Điền file image definition như minh họa.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codepipeline-5.png)
6. Gửi tạo và hoàn tất, pipeline sẽ được tạo.