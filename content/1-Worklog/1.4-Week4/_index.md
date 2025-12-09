---
title: "Week 4 Worklog"
date: 2025-09-29
weight: 4
chapter: false
pre: "<b>1.4. </b>"
---

### Week 4 Objectives

- Keep pace with the team's learning progress on AWS services.
- Master AWS Transit Gateway setup and configuration.
- Deepen understanding of Amazon EC2 and related compute services.
- Learn Git fundamentals for effective team collaboration.
- **Workshop: Begin VPC & Network Setup** for the Bandup IELTS infrastructure.

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|-----|------|------------|-----------------|---------------------|
| 2 | - Explore AWS Transit Gateway: concepts, setup process, and required resources.<br>- Compare differences between VPC Peering and Transit Gateway.<br>- Complete: **Centralized Network Management with AWS Transit Gateway**. | 29/09/2025 | 30/09/2025 | [AWS Transit Gateway](https://aws.amazon.com/transit-gateway/) |
| 3 | - Deep dive into Amazon EC2 through Module 3 lectures.<br>- Study EC2 Auto Scaling for automated resource management.<br>- Complete: **Scaling Applications with EC2 Auto Scaling**. | 01/10/2025 | 02/10/2025 | [FCJ Playlist](https://youtube.com/playlist?list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i) |
| 4 | - Learn and practice Git commands (commit, push, pull) for team collaboration.<br>- Explore Amazon Lightsail for simplified compute solutions.<br>- Complete: **Simplified Computing with Amazon Lightsail**. | 03/10/2025 | 04/10/2025 | [Git Tutorial](https://www.youtube.com/watch?v=8O14qT3jdq0) |
| 5 | - Propose ideas and assign tasks to team members for project proposal.<br>- Study migration strategies for AWS.<br>- Complete: **VM Migration with AWS VM Import/Export**.<br>- **Workshop Activity:** Create VPC with CIDR `10.0.0.0/16` and configure DNS support. | 05/10/2025 | 06/10/2025 | Team Meeting, [Workshop 5.3](5-Workshop/5.3-VPC-Network/) |

### AWS Skill Builder Courses Completed

| Course | Category | Status |
|--------|----------|--------|
| Centralized Network Management with AWS Transit Gateway | Networking | ✅ |
| Scaling Applications with EC2 Auto Scaling | Compute | ✅ |
| Simplified Computing with Amazon Lightsail | Compute | ✅ |
| Container Deployment with Amazon Lightsail Containers | Containers | ✅ |
| VM Migration with AWS VM Import/Export | Migration | ✅ |
| Database Migration with AWS DMS and SCT | Migration | ✅ |
| Disaster Recovery with AWS Elastic Disaster Recovery | Reliability | ✅ |
| Monitoring with Amazon CloudWatch | Operations | ✅ |

### Week 4 Achievements

**Technical Skills Acquired:**

*AWS Transit Gateway:*
- Mastered Transit Gateway setup and configuration
- Understood key advantages over VPC Peering:
  - Supports complex multi-VPC topologies (hub-and-spoke model)
  - Enables transitive routing between connected networks
  - Simplifies network management at scale
  - Supports VPN and Direct Connect attachments
- Learned Transit Gateway route table management

*Amazon EC2 Deep Dive:*
- Comprehensive understanding of EC2 key features:
  - **Elasticity**: Scale resources up/down based on demand
  - **Flexible configurations**: Multiple instance types for various workloads
  - **Cost optimization**: On-Demand, Reserved, Spot instance pricing models
- Mastered **EC2 Auto Scaling** for automated resource adjustment
- Understood **Instance Store** as ephemeral block storage for EC2
- Explored **Amazon Lightsail** as a simplified solution for small-scale applications
- Learned about **Lightsail Containers** for easy container deployment

*Migration Services:*
- Understood **AWS Application Migration Service (MGN)** for server migration
- Learned **VM Import/Export** for virtual machine migration to AWS
- Explored **Database Migration Service (DMS)** and **Schema Conversion Tool (SCT)**
- Studied disaster recovery strategies with **AWS Elastic Disaster Recovery**

*DevOps and Monitoring:*
- Proficient in Git commands (commit, push, pull) and team workflows
- Learned CloudWatch fundamentals for monitoring AWS resources

**Team Collaboration:**
- Successfully proposed ideas and assigned tasks for project proposal
- Team is prepared to begin implementation phase
- Established clear roles and responsibilities for each team member

**Workshop Progress - VPC & Network Setup:**
- Created VPC with CIDR `10.0.0.0/16` in ap-southeast-1 region
- Designed subnet architecture: Public subnets (10.0.1.0/24, 10.0.2.0/24) and Private subnets for App (10.0.11.0/24, 10.0.12.0/24) and DB (10.0.21.0/24, 10.0.22.0/24) across two AZs
- Configured Internet Gateway for public subnet internet access
- Set up route tables for proper traffic routing
- Began security group configuration for multi-tier architecture

**Key Takeaways:**
- Transit Gateway is essential for managing complex multi-VPC architectures
- EC2 Auto Scaling ensures applications can handle variable load efficiently
- Lightsail is perfect for simple workloads without AWS complexity
- Migration services provide multiple paths for moving workloads to AWS
- VPC design with separate public/private subnets provides security isolation
- Multi-AZ deployment ensures high availability from the network layer
