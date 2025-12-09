---
title: "Week 6 Worklog"
date: 2025-10-14
weight: 6
chapter: false
pre: "<b>1.6. </b>"
---

### Week 6 Objectives

- Master fundamental AWS storage services and their use cases.
- Enhance Python programming skills through practical exercises.
- Design and refine the project's infrastructure architecture.
- Attend the "Reinventing DevSecOps with AWS Generative AI" webinar to explore DevSecOps practices and Amazon Q Developer.
- **Workshop: Complete Database & Storage Setup** and configure S3 for static assets.

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|-----|------|------------|-----------------|---------------------|
| 2 | - Study Amazon S3 fundamentals: Bucket architecture, durability guarantees, and static website hosting capabilities.<br>- Explore S3 Storage Classes (Standard, Standard-IA) and Amazon Glacier for cold storage solutions.<br>- Complete: **Static Website Hosting with Amazon S3**. | 14/10/2025 | 15/10/2025 | [AWS S3 Documentation](https://aws.amazon.com/s3/) |
| 3 | - Learn AWS Storage Gateway types (File, Volume, Tape Gateway) and their integration patterns.<br>- Understand Object Lifecycle Management policies for cost optimization.<br>- Practice Python fundamentals: data structures, functions, and error handling.<br>- **Attend webinar:** "Reinventing DevSecOps with AWS Generative AI" featuring Hoàng Kha. | 16/10/2025 | 17/10/2025 | [AWS Storage Gateway](https://aws.amazon.com/storagegateway/)<br>[AWS Events](https://aws.amazon.com/events/) |
| 4 | - Study disaster recovery concepts: RTO, RPO, and Backup & Restore strategies.<br>- Explore AWS Backup service for centralized backup management.<br>- Hands-on: Create S3 buckets, upload files, configure static website hosting, and test lifecycle policies.<br>- Research DevSecOps methodologies: CI/CD pipelines, SAST/DAST tools, Infrastructure as Code.<br>- **Workshop Activity:** Deploy RDS PostgreSQL Multi-AZ instance and ElastiCache Redis cluster. | 18/10/2025 | 19/10/2025 | [AWS Backup](https://aws.amazon.com/backup/), [Workshop 5.6](5-Workshop/5.6-Database-Storage/) |
| 5 | - Finalize project infrastructure architecture diagram with detailed component relationships.<br>- Restructure code skeleton to align with the updated architecture design.<br>- Standardize programming language and framework selection for team consistency.<br>- Explore Amazon Q Developer capabilities: AI-powered code generation, testing, and vulnerability scanning. | 20/10/2025 | 21/10/2025 | [Amazon Q Developer](https://aws.amazon.com/q/developer/) |

### AWS Skill Builder Courses Completed

| Course | Category | Status |
|--------|----------|--------|
| Static Website Hosting with Amazon S3 | Storage | ✅ |
| Data Protection with AWS Backup | Reliability | ✅ |
| Content Delivery with Amazon CloudFront | Networking | ✅ |

### Week 6 Achievements

**Storage Services Mastery:**
- Comprehensive understanding of Amazon S3 architecture: Buckets, durability (99.999999999%), and static website hosting
- Mastered S3 Storage Classes: Standard, Standard-IA, Glacier for different access patterns
- Learned AWS Storage Gateway integration patterns for hybrid cloud storage
- Understood Object Lifecycle Management for automated data tiering and cost optimization

**Disaster Recovery & Backup:**
- Grasped disaster recovery fundamentals: RTO (Recovery Time Objective) and RPO (Recovery Point Objective)
- Learned AWS Backup service for centralized backup management across services
- Understood Backup & Restore strategies for business continuity

**Development Skills:**
- Enhanced Python programming through practical exercises
- Successfully created S3 buckets, configured static websites, and tested lifecycle policies
- Improved understanding of data structures and error handling

**Project Planning:**
- Finalized comprehensive infrastructure architecture diagram
- Restructured code skeleton with proper directory structure
- Standardized technology stack for team collaboration

**DevSecOps Insights:**
- Attended "Reinventing DevSecOps with AWS Generative AI" webinar (October 16, 2025)
- Learned DevSecOps integration: Security in SDLC using Jenkins (CI/CD), SonarQube (SAST), OWASP ZAP (DAST), Terraform (IaC)
- Explored Amazon Q Developer: AI assistant for code generation, testing, vulnerability scanning, and AWS optimization

**Workshop Progress - Database & Storage:**
- Deployed RDS PostgreSQL Multi-AZ instance in private DB subnets for high availability
- Configured ElastiCache Redis cluster for session management and application caching
- Set up S3 buckets for static website assets, user uploads, and document storage
- Configured S3 lifecycle policies for cost optimization (transitioning to Glacier)
- Established database connection strings and caching endpoints for application integration
- Implemented database backup strategies using AWS Backup service

**Key Takeaways:**
- S3 is the foundation for object storage in AWS - understanding storage classes is crucial for cost optimization
- Lifecycle policies automate data management and reduce storage costs significantly
- AWS Backup provides unified backup management across multiple AWS services
- DevSecOps integrates security throughout the development lifecycle, not as an afterthought
- RDS Multi-AZ provides automatic failover for database high availability
- ElastiCache significantly improves application performance through in-memory caching
- Private subnets for databases provide additional security layer
