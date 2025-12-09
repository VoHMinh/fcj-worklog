---
title: "Setup ECR & Push Image"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

In this step, we will containerize our Next.js Frontend application and push the Docker image to Amazon Elastic Container Registry (ECR). This image will later be used by ECS Fargate to launch the application.

### 1. Prepare Dockerfile

We use a multi-stage `Dockerfile` optimized for **Bun** (a fast JavaScript runtime) and Next.js. This configuration reduces the final image size and improves security.

-   **Base Image:** `oven/bun:1.1.26`
-   **Builder:** Compiles the Next.js application.
-   **Runner:** A lightweight production environment exposing port 3000.

![Dockerfile Content](/images/5-Workshop/5.4-fe/docker-image.png)

### 2. Build Docker Image

Run the following command in your project root to build the image. We tag it as `band-up-frontend`.

```bash
docker build -t band-up-frontend .
```

The build process will install dependencies using `bun install` and compile the project.

![Build Command Start](/images/5-Workshop/5.4-fe/build-1.png)
![Build Process](/images/5-Workshop/5.4-fe/build-2.png)
![Build Success](/images/5-Workshop/5.4-fe/build-3.png)

### 3. Verify Local Image

Once the build is complete, verify that the image exists locally.

```bash
docker image ls
```

You should see `band-up-frontend` with the `latest` tag.

![List Docker Images](/images/5-Workshop/5.4-fe/result-build.png)

**Test Locally:**
You can try running the container locally to ensure it starts up correctly before pushing to AWS.

```bash
docker run -p 3000:3000 band-up-frontend:latest
```

![Run Docker Locally](/images/5-Workshop/5.4-fe/run-image.png)

### 4. Push to Amazon ECR

Now we need to upload this image to AWS.

**Step 1: Create Repository**

1.  Go to **Amazon ECR** > **Repositories**.
2.  Click **Create repository**.
3.  **Visibility settings:** `Private`.
4.  **Repository name:** `band-up-frontend`.
5.  Click **Create repository**.

**Step 2: Push Image**
Use the AWS CLI to authenticate and push the image. Replace `[AWS_ACCOUNT_ID]` and `[REGION]` with your details.

1.  **Login to ECR:**

    ```
    aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin [AWS_ACCOUNT_ID].dkr.ecr.ap-southeast-1.amazonaws.com
    ```

2.  **Tag the Image:**

    ```
    docker tag band-up-frontend:latest [AWS_ACCOUNT_ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:latest
    ```

3.  **Push the Image:**
    ```
    docker push [AWS_ACCOUNT_ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:latest
    ```

Once the push is complete, your image is hosted on AWS ECR and ready for deployment.
