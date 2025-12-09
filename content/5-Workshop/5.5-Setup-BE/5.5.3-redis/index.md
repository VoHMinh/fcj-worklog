---
title: "Create ElastiCache (Redis/Valkey)"
date: 2025-09-09
weight: 3
chapter: false
pre: " <b> 5.5.3. </b> "
---

In this step, we provision an in-memory data store to handle session management and caching for the backend. We will use **Amazon ElastiCache** with the **Valkey** engine (a high-performance, open-source fork of Redis supported by AWS).

### 1. Configure Security Group

First, create a Security Group to allow the backend to communicate with the cache cluster.

1.  Navigate to **EC2** > **Security Groups** > **Create security group**.
2.  **Name:** `redis-sg`.
3.  **Inbound rules:** Allow **Custom TCP** traffic on port `6379` from the `ecs-backend-sg`.

_(Note: Ensure this is created before proceeding to the ElastiCache console)._

### 2. Create Subnet Group

We need to define which subnets the cache nodes will reside in.

1.  Navigate to **Amazon ElastiCache** > **Subnet groups** > **Create subnet group**.
2.  **Name:** `bandup-cached-subnet-group`.
3.  **VPC:** Select `band-up-vpc`.
4.  **Subnets:** Select `private-database-subnet-1` and `private-database-subnet-2` (Availability Zones `ap-southeast-1a` and `1b`).

![Redis Subnet Group](/images/5-Workshop/5.5-be/redis-subnet-group.png)

### 3. Create ElastiCache Cluster

Now we provision the cache cluster.

1.  Navigate to **ElastiCache** > **Caches** > **Create cache**.
2.  **Engine:** Select `Valkey - recommended` (Compatible with Redis OSS).
3.  **Deployment option:** Select `Node-based cluster` (Gives more control over instance types).
4.  **Creation method:** `Cluster cache`.

![Engine Selection](/images/5-Workshop/5.5-be/redis-1.png)

5.  **Cluster settings:**
    -   **Cluster mode:** `Disabled` (Simple primary-replica structure is sufficient).
    -   **Name:** `bandup-redis`.
    -   **Description:** `in memory db for bandup`.

![Cluster Settings](/images/5-Workshop/5.5-be/redis-2.png)

6.  **Node configuration:**
    -   **Node type:** `cache.t3.micro` (Cost-effective for testing).
    -   **Number of replicas:** `0` (Standalone node for this workshop).

![Node Configuration](/images/5-Workshop/5.5-be/redis-3.png)

7.  **Connectivity:**
    -   **Network type:** `IPv4`.
    -   **Subnet groups:** Select `bandup-cached-subnet-group`.

![Connectivity Settings](/images/5-Workshop/5.5-be/redis-4.png)

8.  **Security & Encryption:**
    -   **Encryption at rest:** Enabled (Default key).
    -   **Encryption in transit:** Enabled.
    -   **Access control:** `No access control` (We rely on Security Groups).
    -   **Security groups:** Select `redis-sg` created earlier.

![Security Settings](/images/5-Workshop/5.5-be/redis-5.png)

9.  **Backup:** Enable automatic backups (Retention: 1 day).
10. Click **Create**.

The cluster status will change to **Creating**. Once **Available**, note down the **Primary Endpoint** (ending in `...cache.amazonaws.com:6379`) to use in the backend configuration.
