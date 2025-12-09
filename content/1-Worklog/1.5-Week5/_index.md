---
title: "Week 5 Worklog"
date: 2025-10-07
weight: 5
chapter: false
pre: "<b>1.5. </b>"
---

### Week 5 Objectives

- Identify and resolve abnormal AWS costs on the account.
- Design and partition infrastructure architecture for the project.
- Begin initial project configuration and assign team roles.
- Explore AWS Skill Builder and advance learning on optimization topics.
- **Workshop: Complete VPC Network Setup** and begin **Database & Storage Setup**.

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|-----|------|------------|-----------------|---------------------|
| 2 | - Analyze and identify causes of abnormal costs on AWS account.<br>- Complete: **Cost and Usage Management** and **Managing Quotas with Service Quotas**. | 07/10/2025 | 08/10/2025 | [AWS Cost Explorer](https://aws.amazon.com/aws-cost-management/aws-cost-explorer/) |
| 3 | - Design and partition project infrastructure architecture.<br>- Propose basic architecture templates for team reference.<br>- Complete: **Building Highly Available Web Applications**.<br>- **Workshop Activity:** Configure NAT Gateways in public subnets and complete Security Groups setup. | 09/10/2025 | 10/10/2025 | [FCJ Community](https://www.facebook.com/groups/awsstudygroupfcj), [Workshop 5.3](5-Workshop/5.3-VPC-Network/) |
| 4 | - Build code skeleton and configure initial project files.<br>- Set up development environment.<br>- Complete: **Development Environment with AWS Toolkit for VS Code**.<br>- **Workshop Activity:** Begin Database & Storage Setup - plan RDS PostgreSQL and ElastiCache Redis configurations. | 11/10/2025 | 13/10/2025 | VS Code + AWS Toolkit, [Workshop 5.6](5-Workshop/5.6-Database-Storage/) |
| 5 | - Register for AWS Skill Builder and explore courses.<br>- Study EC2 optimization techniques.<br>- Complete: **Right-Sizing with EC2 Resource Optimization**. | 11/10/2025 | 12/10/2025 | [AWS Skill Builder](https://skillbuilder.aws/) |

### AWS Skill Builder Courses Completed

| Course | Category | Status |
|--------|----------|--------|
| Cost and Usage Management | Cost Optimization | ✅ |
| Managing Quotas with Service Quotas | Operations | ✅ |
| Billing Console Delegation | Cost Management | ✅ |
| Right-Sizing with EC2 Resource Optimization | Cost Optimization | ✅ |
| Development Environment with AWS Toolkit for VS Code | Development | ✅ |
| Building Highly Available Web Applications | Architecture | ✅ |
| Database Essentials with Amazon RDS | Database | ✅ |
| NoSQL Database Essentials with Amazon DynamoDB | Database | ✅ |
| In-Memory Caching with Amazon ElastiCache | Database | ✅ |
| Command Line Operations with AWS CLI | Operations | ✅ |

### Week 5 Achievements

**Technical Skills Acquired:**

*Cost Optimization:*
- Identified causes of abnormal AWS costs:
  - Incomplete deletion of EC2 resources (EBS volumes, Elastic IPs)
  - Lack of control over user accounts and IAM permissions
  - Resources left running in unused regions
- Learned AWS cost management best practices:
  - **AWS Budgets** for proactive cost alerts
  - **Cost Explorer** for analyzing spending patterns
  - **Service Quotas** for managing account limits
  - **Billing Console Delegation** for team cost visibility
- Proposed cost optimization measures for the team

*Architecture Design:*
- Successfully designed project infrastructure architecture
- Created reference architecture templates for team adoption
- Applied **High Availability** principles:
  - Multi-AZ deployments
  - Load balancing strategies
  - Database replication patterns
  - Fault-tolerant design patterns

*Development Environment:*
- Set up AWS Toolkit for VS Code for streamlined development
- Mastered AWS CLI for command-line operations
- Built robust code skeleton with initial configuration files
- Established project foundation for team collaboration

*Database Services:*
- Understood **Amazon RDS** for relational database needs
- Learned **DynamoDB** for NoSQL workloads
- Explored **ElastiCache** for in-memory caching (Redis/Memcached)
- Applied database selection criteria based on use cases

**Project Progress:**
- Registered and activated AWS Skill Builder account
- Began exploring advanced courses and learning paths
- Infrastructure architecture finalized and documented
- Development environment configured and ready for coding

**Workshop Progress - Network & Database Setup:**
- Completed NAT Gateway deployment in both public subnets for private subnet internet access
- Configured Security Groups for ALB, ECS, RDS, and ElastiCache tiers with least-privilege access
- Set up route tables: Public routes to Internet Gateway, Private routes to NAT Gateways
- Designed RDS PostgreSQL Multi-AZ configuration for high availability
- Planned ElastiCache Redis cluster for session management and caching
- Configured S3 buckets for static assets and document storage

**Key Takeaways:**
- Cost optimization starts with visibility - use Cost Explorer daily
- Right-sizing EC2 instances can reduce costs by 30-50%
- High availability requires planning across multiple AZs
- AWS Toolkit for VS Code significantly improves developer productivity
- Database selection depends on data model, scale, and access patterns
- Service Quotas prevent unexpected capacity limitations
- NAT Gateways enable private subnet resources to access internet securely
- Security Groups provide defense-in-depth with multiple layers of protection
