---
title: "Kết nối repo GitLab & tạo dự án CodeBuild"
date: 
weight: 1
chapter: false
pre: " <b> 5.7.1 </b> "
---

## Mục tiêu
Cấu hình hai dự án CodeBuild (frontend và backend) và trigger từ sự kiện Release của GitLab để khởi chạy CodePipeline. CodePipeline gọi CodeBuild dựa trên `frontend-buildspec.yml` và `backend-buildspec.yml` có sẵn trong repository, sau đó deploy lên ECS.

## Tài nguyên AWS
- Dự án CodeBuild:
	- Frontend: Source = CodePipeline; Buildspec = `frontend-buildspec.yml`
	- Backend: Source = CodePipeline; Buildspec = `backend-buildspec.yml`
- CodePipeline (bước sau) nhận artifact và deploy lên ECS

### Tạo dự án CodeBuild & kết nối repository GitLab

1. Trong phần cấu hình tạo mới CodeBuild Project, chọn Default project.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-1.png)
2. Ở mục Source, chọn GitLab và repository Band-Up.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-2.png)
3. Giữ nguyên cấu hình mặc định cho Environment.
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-3.png)
4. Chỉ định buildspec của dự án Band-Up cho frontend/backend như hình:
![CI/CD overview placeholder](/images/5-Workshop/5.7-CICD/codebuild-4.png)
5. Gửi tạo và lặp lại cho dự án CodeBuild frontend/backend còn lại.

## Tóm tắt
Bạn đã có hai dự án CodeBuild (frontend và backend) sẵn sàng được gọi bởi CodePipeline. Sự kiện Release từ GitLab có thể kích hoạt CodePipeline, sau đó chạy mỗi dự án với `frontend-buildspec.yml` và `backend-buildspec.yml` tương ứng. Ở các bước tiếp theo, CodePipeline sẽ lấy artifact sau build và deploy lên ECS.
