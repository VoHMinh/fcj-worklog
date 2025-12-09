---
title: "IAM Roles for ECS"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.3.3. </b> "
---

To allow Amazon ECS to manage your containers, it needs specific permissions. We must create an **IAM Role** that authorizes the ECS agent to pull container images from Amazon ECR and send logs to Amazon CloudWatch on your behalf.

### Create ecsTaskExecutionRole

1.  Navigate to the **IAM Dashboard**.
2.  In the left navigation pane, choose **Roles**.
3.  Click **Create role**.

**Step 1: Trusted Entity**

1.  **Trusted entity type:** Select `AWS service`.
2.  **Service or use case:** Choose `Elastic Container Service`.
3.  Select `Elastic Container Service Task` from the options below.
4.  Click **Next**.

**Step 2: Add Permissions**

1.  In the search bar, type `AmazonECSTaskExecutionRolePolicy`.
2.  Check the box next to the policy name **AmazonECSTaskExecutionRolePolicy**.
    -   _Note: This managed policy grants permissions to pull images from ECR and upload logs to CloudWatch._
3.  Click **Next**.

**Step 3: Name and Review**

1.  **Role name:** Enter `ecsTaskExecutionRole`.
2.  Review the configuration and click **Create role**.

![ECS Task Execution Role Summary](/images/5-Workshop/5.3-Network/executionRole.png)

Once created, this role is ready to be assigned to our ECS Task Definitions in the upcoming sections.
