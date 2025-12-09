---
title: "Setup ECR & Push Image"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

In this step, we will containerize the Spring Boot Backend and push the optimized Docker image to Amazon ECR.

### 1. Dockerfile Strategy

For the Backend, we utilize a **Multi-Stage Build** strategy with `eclipse-temurin:21` (Java 21). This ensures a small and secure final image.

-   **Stage 1 (Deps):** Resolves and downloads Maven dependencies.
-   **Stage 2 (Package):** Builds the application and extracts the **Spring Boot Layered Jar**. This splits the application into layers (dependencies, spring-boot-loader, application code), allowing Docker to cache unchanged layers (like dependencies) effectively.
-   **Stage 3 (Final):** Copies the extracted layers into a lightweight JRE image. It also creates a non-privileged user `appuser` for security.

![Dockerfile Stage 1 - Dependencies](/images/5-Workshop/5.5-be/be-docker-1.png)
![Dockerfile Stage 2 - Layer Extraction](/images/5-Workshop/5.5-be/be-docker-2.png)
![Dockerfile Stage 3 - Final Image](/images/5-Workshop/5.5-be/be-docker-3.png)

### 2. Build Docker Image

Run the build command in the backend project root. We tag the image as `band-up-backend`.

```bash
docker build -t band-up-backend .
```

Docker will execute the stages defined above.

![Build Backend Process](/images/5-Workshop/5.5-be/docker-build-1.png)

### 3. Create ECR Repository

We need a repository to store this image.

1.  Navigate to **Amazon ECR** > **Create repository**.
2.  **Repository name:** `band-up-backend` (Ensure this matches your push command).
3.  **Visibility:** `Private`.
4.  **Image tag mutability:** `Mutable`.
5.  Click **Create repository**.

### 4. Push Image to ECR

Once the image is built and the repository is ready, proceed to push.

**Step 1: Tag the Image**
Tag the local image with a version number (e.g., `v1.0.0`).

```bash
docker tag band-up-backend:latest [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-backend:v1.0.0
```

**Step 2: Push to ECR**
Upload the layers to AWS.

```bash
docker push [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-backend:v1.0.0
```

![Push Backend Image](/images/5-Workshop/5.5-be/docker-build-3.png)

### 5. Verify

Navigate to the **Amazon ECR Console** and select the `bandup-backend` repository. You should see the image tagged `v1.0.0`.

![Verify Backend Image](/images/5-Workshop/5.5-be/image-check.png)
