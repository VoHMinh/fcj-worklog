---
title: "Worklog Week 10"
date: 2024-11-11
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Week 10 Objectives

- **Stabilize AWS SAM/Serverless deployment environment** and resolve critical issues.
- **Focus on debugging** core problems: CORS configuration, template validation errors, and API response formatting.
- **Integrate Frontend/Backend** to enable end-to-end testing on user interface.
- **Complete** basic **Read** and **Delete** functionalities with proper error handling.
- **Participate in AWS Cloud Mastery Series event** to receive expert guidance and address project challenges.
- **Workshop: Load Balancer Configuration** - Set up Application Load Balancer for traffic distribution.

---

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Mon | - **Debug CORS:** Analyze CORS configuration in **API Gateway** (CORS headers, preflight OPTIONS requests) and Lambda response headers to allow Frontend access. <br> - **Fix template validation errors:** Review and optimize `template.yaml` file to prevent deployment loop errors and resource dependency issues during `sam deploy`. | 11/11/2024 | 11/11/2024 | API Gateway/CORS Documentation |
| Tue | - Strengthen **Read** function (Retrieving flashcard sets): Ensure data is queried from DynamoDB correctly and returned in proper JSON format for Frontend consumption. <br> - Implement error handling for missing records and invalid queries. <br> - Add logging for debugging purposes. | 12/11/2024 | 12/11/2024 | DynamoDB Query Documentation |
| Wed | - **Frontend Integration:** Begin combining Frontend codebase with project and test deployed API endpoints. <br> - **Successfully display** flashcard sets list on user interface. <br> - Test API connectivity and data rendering in React/Vue components. <br> - **Workshop Activity:** Create Application Load Balancer (ALB) in public subnets and configure target groups for ECS services. | 13/11/2024 | 13/11/2024 | Frontend Framework Documentation, [Workshop 5.5](5-Workshop/5.5-Load-Balancer/) |
| Thu | - Deploy and test **Delete** function (Removing flashcard sets). <br> - **Encountered Error:** Identified authorization issue with **Cognito User Sub ID** when executing Delete function - Lambda unable to extract/process Sub ID from Cognito token correctly. <br> - Begin troubleshooting authentication flow. | 14/11/2024 | 14/11/2024 | AWS Cognito Documentation |
| Fri | - **Participation in AWS Cloud Mastery Series:** <br>&emsp; + Received expert guidance and clarified questions regarding Serverless architecture, Lambda best practices, and authentication patterns. <br> - **Analyze Update/Delete errors:** Apply mentor guidance to resolve authorization issues and Cognito token parsing problems. <br> - Document solutions for future reference. | 15/11/2024 | 15/11/2024 | Mentor, AWS Cloud Mastery Series |

---

### Week 10 Achievements

- **Successfully fixed** CORS error and stabilized SAM deployment process (mitigated template validation errors).
- **Participated in AWS Cloud Mastery Series event** and gathered essential information to solve major project blockers.
- **Completed Frontend and Backend integration**, achieving first functional user interface for end-to-end testing.
- **Successfully deployed Read** (Retrieving flashcard sets) and **Delete** (Removing flashcard sets) functionalities, operational on web interface.
- **Identified and gained direction** to solve critical bottlenecks:
  - Authorization error: Lambda fails to retrieve/incorrectly process **Cognito Sub ID** from JWT token, affecting privileged operations
  - Update function dependency: Requires proper authentication flow and token validation
- The project has transitioned to basic user testing phase with working CRUD operations.
- Established debugging workflow and error handling patterns for team.

**Workshop Progress - Load Balancer Configuration:**
- Created Application Load Balancer (ALB) in public subnets across two AZs
- Configured target groups for Frontend (port 3000) and Backend (port 8080) ECS services
- Set up health checks for automatic unhealthy target removal
- Configured SSL/TLS termination using ACM certificates
- Integrated ALB with Route 53 for DNS routing
- Implemented listener rules for path-based routing (Frontend: `/`, Backend: `/api/*`)
- Configured security groups to allow ALB traffic to ECS tasks

**Key Takeaways:**
- CORS requires proper configuration in both API Gateway and Lambda response headers
- Cognito JWT tokens must be properly decoded to extract user identity (Sub ID)
- Frontend-Backend integration requires careful attention to API contracts and data formats
- Error handling and logging are essential for debugging production issues
- AWS Cloud Mastery Series provides valuable real-world insights from experienced practitioners
- ALB provides intelligent traffic distribution and automatic failover
- Health checks ensure only healthy targets receive traffic
- SSL/TLS termination at ALB reduces computational load on backend services
