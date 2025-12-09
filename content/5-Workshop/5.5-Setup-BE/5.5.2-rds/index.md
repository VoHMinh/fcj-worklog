---
title: "Create PostgreSQL RDS"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

In this step, we provision an Amazon RDS for PostgreSQL instance. This will serve as the primary persistent data store for the IELTS BandUp platform. We will configure it for high availability and security within our VPC.

### 1. Configure Security Groups

Before creating the database, we need to define the firewall rules.

**Step 1.1: Create Backend Security Group**
This group is for the ECS Fargate tasks (the application layer) to control outbound traffic.

-   **Name:** `ecs-backend-sg`.
-   **Inbound:** Allow port `8080` (Spring Boot default) from the ALB.

![Backend Security Group](/images/5-Workshop/5.5-be/rds-sg-1.png)

**Step 1.2: Create RDS Security Group**
This group is attached to the database itself.

-   **Name:** `rds-sg`.
-   **Inbound:** Allow PostgreSQL traffic (Port `5432`) **only** from the `ecs-backend-sg` created above (or the VPC CIDR for testing). This ensures only our application can talk to the database.

![RDS Security Group](/images/5-Workshop/5.5-be/rds-sg-2.png)

### 2. Create DB Subnet Group

RDS needs to know which subnets it is allowed to use. We will group our private database subnets together.

1.  Navigate to **Amazon RDS** > **Subnet groups** > **Create DB subnet group**.
2.  **Name:** `bandup-db-subnet-group`.
3.  **VPC:** Select `band-up-vpc`.
4.  **Add subnets:** Select the Availability Zones and choose the dedicated `private-database-subnet-1` and `private-database-subnet-2`.

![DB Subnet Group Created](/images/5-Workshop/5.5-be/rds-db-subnet-group.png)

### 3. Create the Database

Now, we provision the PostgreSQL instance.

1.  Navigate to **Databases** > **Create database**.
2.  **Choose a database creation method:** `Standard create`.
3.  **Engine options:** `PostgreSQL` (Version 17.6 or latest).

![Select Engine](/images/5-Workshop/5.5-be/rds-1.png)

4.  **Availability and durability:** Select **Multi-AZ DB instance**. This creates a primary DB and a synchronous standby replica in a different Availability Zone for automatic failover.
5.  **Settings:**
    -   **DB instance identifier:** `bandup-db`.
    -   **Master username:** `postgres`.
    -   **Credential management:** `Self managed`.
    -   **Master password:** Set a strong password (save this for later).

![Availability and Credentials](/images/5-Workshop/5.5-be/rds-2.png)

6.  **Instance configuration:**
    -   **DB instance class:** `Burstable classes` -> `db.t4g.micro` (Cost-effective for workshops/dev).
7.  **Storage:** `gp3` (General Purpose SSD) with `20 GiB`.

![Instance Size and Storage](/images/5-Workshop/5.5-be/rds-3.png)

8.  **Connectivity:**
    -   **Compute resource:** Don't connect to an EC2 compute resource.
    -   **VPC:** `band-up-vpc`.
    -   **DB subnet group:** `bandup-db-subnet-group` (Created in step 2).
    -   **Public access:** **No** (Critical for security).
    -   **VPC security group:** Select existing -> `rds-sg`.

![Connectivity Settings](/images/5-Workshop/5.5-be/rds-4.png)

9.  **Database authentication:** `Password authentication`.
10. **Monitoring:** Enable `Performance Insights` (retention 7 days).

![Authentication and Monitoring](/images/5-Workshop/5.5-be/rds-5.png)

11. **Additional configuration:**
    -   **Initial database name:** `band_up` (Important: Hibernate will look for this DB name).
    -   **Backup:** Enable automated backups.
    -   **Encryption:** Enable encryption.

![Additional Config](/images/5-Workshop/5.5-be/rds-7.png)

12. Click **Create database**. The provisioning process will take a few minutes.
