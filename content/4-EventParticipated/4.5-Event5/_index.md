---
title: "Event 5"
date: 2025-11-29
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---


# Event Report: AWS Cloud Mastery Series #3 — AWS Well-Architected Security Pillar

## Event Purpose

- Introduce the role of the Security Pillar within the AWS Well-Architected Framework.
- Present the five core pillars of cloud security: Identity & Access Management, Detection, Infrastructure Protection, Data Protection, and Incident Response.
- Provide best practices and practical playbooks to protect cloud applications.

## Highlights

### Pillar 1 — Identity & Access Management (08:50 – 09:30)

- Principles: Least Privilege, Zero Trust, Defense in Depth.
- Modern IAM: avoid long-term credentials; prefer Roles and Policies.
- IAM Identity Center: SSO and management of Permission Sets.
- Multi-account security: SCPs (Service Control Policies) and Permission Boundaries.
- Mini demo: validate IAM policies and simulate access.

### Pillar 2 — Detection (09:30 – 09:55)

- Continuous monitoring: CloudTrail (organization-level), GuardDuty, Security Hub.
- Logging at all layers: VPC Flow Logs, ALB/S3 logs.
- Automated alerting: using EventBridge.

### Pillar 3 — Infrastructure Protection (10:10 – 10:40)

- Network security: VPC segmentation (private vs. public).
- Defenses: Security Groups vs. NACLs; using WAF, Shield, Network Firewall.
- Workload security: securing EC2, basics for ECS/EKS.

### Pillar 4 — Data Protection (10:40 – 11:10)

- Encryption: encryption at rest & in transit (S3, EBS, RDS, DynamoDB).
- Key and secret management: KMS, Secrets Manager, Parameter Store.
- Data classification and access guardrails.

### Pillar 5 — Incident Response (11:10 – 11:40)

- IR lifecycle: AWS-recommended incident response processes.
- IR playbook & automation.
- Sample scenarios: compromised IAM key, public S3 exposure, EC2 malware detection.
- Automated response using Lambda / Step Functions.

## What I Learned

- Understand the five Security Pillars and the Shared Responsibility Model.
- Advanced IAM: using IAM Identity Center, SCPs, and avoiding long-term credentials.
- Data security: the importance of KMS and managing secrets.
- Incident Response: building playbooks and automating responses with serverless.

## Event Experience

- The workshop served as the final summary session in the series, providing essential security knowledge before project completion.
- The IAM Identity Center and Secrets Manager presentations helped address Sub ID authentication issues and API key management for the team.
- IR scenarios (e.g., S3 public exposure) were valuable for reinforcing project security policies.
- The final Q&A helped outline the next learning path (Security Specialty).