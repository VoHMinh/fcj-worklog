---
title: "Setup ECR & IAM Role"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

In this step, we prepare the AWS infrastructure required to store our container images. This involves verifying the initial state, creating a necessary IAM Role for ECR replication, and provisioning the repository.

### 1. Verify ECR State

First, verify the current state of the Private Registry. Initially, there are no repositories created.

![Check Empty ECR](/images/5-Workshop/5.4-fe/ecr-check.png)

### 2. Create IAM Role for ECR

We need to create a Service-Linked Role that allows Amazon ECR to perform replication actions across regions and accounts.

1.  Navigate to **IAM** > **Roles** > **Create role**.
2.  **Select trusted entity:** Choose **AWS service**.
3.  **Service or use case:** Select `Elastic Container Registry` from the list.

![Select Trusted Entity](/images/5-Workshop/5.4-fe/iam-ecr-create-1.png)

4.  **Use case:** Select **Elastic Container Registry - Replication** to allow ECR to replicate images.

![Select Use Case](/images/5-Workshop/5.4-fe/iam-ecr-create-3.png)

5.  **Add permissions:** Confirm that the `ECRReplicationServiceRolePolicy` is attached. This managed policy grants the necessary permissions.

![Verify Permissions](/images/5-Workshop/5.4-fe/iam-ecr-create-4.png)

6.  **Name, review, and create:**
    -   The role name is automatically set to `AWSServiceRoleForECRReplication`.
    -   Review the configuration and create the role.

![Review Role](/images/5-Workshop/5.4-fe/iam-ecr-create-5.png)

7.  **Result:** The role is successfully created and listed in the IAM Roles dashboard.

![IAM Role Created](/images/5-Workshop/5.4-fe/iam-ecr-create-2.png)

### 3. Create ECR Repository

Now we create the repository to store the frontend image.

1.  Navigate to **Amazon ECR** > **Create repository**.
2.  **General settings:**
    -   **Repository name:** `band-up-frontend`.
    -   **Visibility settings:** Private.
3.  **Image tag settings:** Keep **Mutable** enabled to allow overwriting image tags.

![Create Repository Settings](/images/5-Workshop/5.4-fe/ecr-create-1.png)

4.  **Result:** The `band-up-frontend` repository is successfully created with AES-256 encryption enabled by default.

![Repository Created](/images/5-Workshop/5.4-fe/ecr-create-result.png)

### 4. Configure CLI Access

To push images from your local machine, you need programmatic access via the AWS CLI. We will generate an Access Key for your IAM User.

1.  Navigate to **IAM Dashboard** > **Users** > Select your user (e.g., `NamDang`).
2.  Open the **Security credentials** tab and click **Create access key**.
3.  **Use case:** Select **Command Line Interface (CLI)**.
4.  **Description tag:** Enter a meaningful description (e.g., `ECR Push Key`) and click **Create access key**.
5.  **Retrieve Keys:** Important! Copy or download the **Access Key ID** and **Secret Access Key** immediately, as you cannot retrieve the Secret Key later.

![Create Access Key Step 1](/images/5-Workshop/5.4-fe/get-key-for-cli-1.png)
![Select CLI Use Case](/images/5-Workshop/5.4-fe/get-key-for-cli-2.png)
![Retrieve Access Keys](/images/5-Workshop/5.4-fe/get-key-for-cli-4.png)

### 5. Configure AWS CLI

Open your terminal and configure the AWS CLI with the credentials you just generated.

```bash
aws configure
```

Enter the following details when prompted:

-   **AWS Access Key ID:** [Paste your key]
-   **AWS Secret Access Key:** [Paste your secret]
-   **Default region name:** `ap-southeast-1`
-   **Default output format:** `json`

![AWS CLI Configure](/images/5-Workshop/5.4-fe/cli-configure.png)

### 6. Push Image to ECR

Now that the CLI is configured, we can authenticate Docker and push our image.

**Step 1: Login to ECR**
Run the login command to authenticate your Docker client with the AWS registry.

```bash
aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin [Account-ID]https://www.google.com/search?q=.dkr.ecr.ap-southeast-1.amazonaws.com
```

_Output: `Login Succeeded`_

![CLI Login Success](/images/5-Workshop/5.4-fe/cli-login.png)

**Step 2: Tag the Image**
We need to tag our local image `band-up-frontend:latest` with the full ECR repository URI and a version tag (e.g., `v1.0.0`).

```bash
docker tag band-up-frontend:latest [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:v1.0.0
```

![Tag Docker Image](/images/5-Workshop/5.4-fe/tag-image.png)

**Step 3: Push the Image**
Execute the push command to upload the layers to AWS.

```bash
docker push [Account-ID].dkr.ecr.ap-southeast-1.amazonaws.com/band-up-frontend:v1.0.0
```

![Push Image Process](/images/5-Workshop/5.4-fe/push-to-ecr.png)

### 7. Final Verification

Return to the **Amazon ECR Console** and open the `band-up-frontend` repository. You should see the image with the tag `v1.0.0` listed successfully.

![Verify Image in Console](/images/5-Workshop/5.4-fe/check-ecr.png)
