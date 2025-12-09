---
title: "Week 3 Worklog"
date: 2025-09-21
weight: 3
chapter: false
pre: "<b>1.3. </b>"
---

### Week 3 Objectives

- Resolve AWS account issues and create a new account if necessary.
- Master Hybrid DNS configuration with Route 53 Resolver.
- Implement and understand VPC Peering for inter-VPC communication.
- Discuss project plans and finalize programming language with the team.

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Reference Material |
|-----|------|------------|-----------------|---------------------|
| 2 | - Access Management with AWS Identity and Access Management (IAM). | 21/09/2025 | 23/09/2025 | [AWS Identity and Access Management (IAM) Access Control](https://000002.awsstudygroup.com/) |
| 3 | - Complete Lab 10: Route 53 and Hybrid DNS configuration.<br>- Launch virtual servers to implement and test DNS setup.<br>- Complete: **Hybrid DNS Management with Amazon Route 53**. | 24/09/2025 | 25/09/2025 | [FCJ Playlist](https://youtube.com/playlist?list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i) |
| 4 | - Implement VPC Peering for private communication between VPCs.<br>- Create necessary resources for VPC Peering configuration.<br>- Clean up resources after completion.<br>- Complete: **Network Integration with VPC Peering**. | 25/09/2025 | 26/09/2025 | [AWS VPC Peering](https://docs.aws.amazon.com/vpc/latest/peering/) |
| 5 | - Attend team meeting to discuss project plans and programming language selection.<br>- Set deadlines for team members to study chosen technology stack. | 28/09/2025 | 28/09/2025 | Team Meeting |

### AWS Skill Builder Courses Completed

| Course | Category | Status |
|--------|----------|--------|
| Hybrid DNS Management with Amazon Route 53 | Networking | ✅ |
| Network Integration with VPC Peering | Networking | ✅ |
| Networking on AWS Workshop | Networking | ✅ |
| Infrastructure as Code with AWS CloudFormation | DevOps | ✅ |
| Cloud Development with AWS Cloud9 | Development | ✅ |
| Static Website Hosting with Amazon S3 | Storage | ✅ |

### Week 3 Achievements

**Technical Skills Acquired:**

*Route 53 and Hybrid DNS:*
- Successfully configured Hybrid DNS infrastructure with Route 53 Resolver
- Created and configured **Outbound Endpoints** for DNS query forwarding
- Set up **Route 53 Resolver** rules for conditional DNS resolution
- Implemented **Inbound Endpoints** for on-premises to AWS DNS queries
- Successfully connected to RD Gateway Server during practical exercises

*VPC Peering:*
- Mastered VPC Peering concepts for private inter-VPC communication without traversing public internet
- Enabled **Cross-Zone and Cross-Region DNS Resolution** in VPC Peering:
  - EC2 instances can now resolve DNS of instances in peered VPCs to private IP addresses
  - Understood that without this feature, DNS queries return public IPs, routing traffic through internet
- Learned resource cleanup procedures to avoid unnecessary costs

*Infrastructure as Code:*
- Learned to provision AWS resources using CloudFormation templates
- Understood declarative infrastructure management principles
- Explored AWS Cloud9 as a cloud-based development environment

**Team Collaboration:**
- Participated in team meeting to finalize project direction
- Selected programming language for the project
- Established deadlines for team members to study the chosen technology stack
- Continued learning journey with FCJ team support

**Key Takeaways:**
- Hybrid DNS enables seamless DNS resolution between on-premises and AWS environments
- VPC Peering is cost-effective for connecting VPCs but has limitations (no transitive peering)
- CloudFormation templates ensure consistent, repeatable infrastructure deployments
- AWS Cloud9 eliminates local development environment setup complexity
