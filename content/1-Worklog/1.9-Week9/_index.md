---
title: "Worklog Week 9"
date: 2024-11-04
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives

- Complete transition to **AWS SAM (Serverless Application Model)** development framework.
- **Refactor** and re-implement CRUD functionalities following SAM architecture patterns.
- Resolve environment-related issues to achieve **successful deployment** status on AWS.
- Integrate **Docker** for standardized build environment and dependency management.
- **Workshop: ECS & Container Setup** - Deploy containerized Frontend and Backend services.

---

### Tasks Completed This Week

| Day | Task | Start Date | Completion Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Monday | - **In-depth research on AWS SAM:** Understand `template.yaml` structure, SAM CLI commands, and how Serverless resources (Lambda, API Gateway) operate within SAM model. <br> - Plan detailed migration strategy: Convert existing Lambda functions to SAM-compatible structure. <br> - Study SAM local testing capabilities (`sam local invoke`, `sam local start-api`). | 04/11/2024 | 04/11/2024 | AWS SAM Documentation, AWS Study Group |
| Tuesday | - **Source Code Refactoring:** Rewrite CRUD functionalities (Create/Read operations) using SAM patterns (Lambda handlers and API Gateway events). <br> - **Docker Integration:** Install and configure Docker to ensure consistent Python runtime environment for `sam build` process. <br> - Create Dockerfile for Lambda layer dependencies. <br> - **Workshop Activity:** Create ECR repositories for Frontend (Next.js) and Backend (Spring Boot) container images. | 05/11/2024 | 06/11/2024 | Docker Documentation, SAM CLI, [Workshop 5.4](5-Workshop/5.4-ECS-Setup/) |
| Wednesday | - **Local Debugging and Testing:** Execute `sam local invoke` to test individual Lambda functions. <br> - **Encountered critical issues** in Local environment: Dependency conflicts, Python version mismatches, DynamoDB local connection problems. <br> - Attempt to resolve local testing barriers through configuration adjustments. | 06/11/2024 | 07/11/2024 | SAM CLI Error Reports, Stack Overflow |
| Thursday | - **Strategic Decision:** Backend Team decided to adopt **deploy-then-test** strategy on actual AWS environment to overcome local debugging limitations, accepting calculated risk. <br> - Focus on fixing configuration errors in `template.yaml` (resource definitions, IAM permissions, environment variables). <br> - Validate SAM template syntax and resource dependencies. | 07/11/2024 | 08/11/2024 | CloudFormation Template Validator |
| Friday | - **Successful Deployment:** Executed `sam deploy --guided` and successfully deployed project to AWS environment. <br> - **Basic Verification:** Tested created API endpoints using Postman/curl, confirming CRUD functionality is operational. <br> - Document deployment process and configuration for team reference. <br> - **Workshop Activity:** Build and push Docker images to ECR, create ECS Task Definitions and deploy ECS Services with Fargate. | 08/11/2024 | 08/11/2024 | AWS CloudFormation Deployment Logs, [Workshop 5.4](5-Workshop/5.4-ECS-Setup/) |

---

### Week 9 Achievements

- **Completed technology transition** to **AWS SAM** development model for entire project.
- **Successfully refactored** CRUD functionalities into SAM Serverless structure with proper handler organization.
- Resolved environment issues by using **Docker** to ensure `sam build` process uses correct Python version and dependencies.
- **Achieved critical milestone:** Successfully deployed project to AWS environment, overcoming local debugging hurdles.
- The **Bandup IELTS project** now has working API version on real Cloud environment (though deeper testing still required).
- Established deployment workflow and best practices for team collaboration.
- Created comprehensive `template.yaml` with proper resource definitions and IAM permissions.

**Workshop Progress - ECS & Container Setup:**
- Created ECR repositories for Frontend (Next.js) and Backend (Spring Boot) container images
- Built and pushed Docker images to ECR with proper tagging strategy
- Created ECS Task Definitions with CPU/memory specifications (Frontend: 512 CPU/1024MB, Backend: 1024 CPU/2048MB)
- Set up ECS Cluster with Fargate capacity providers
- Deployed ECS Services in active-passive Multi-AZ pattern (2 replicas active, 1 standby)
- Configured Service Connect for internal service discovery between Frontend and Backend
- Implemented health checks for automatic task recovery

**Key Takeaways:**
- SAM simplifies serverless application development with infrastructure as code
- Docker ensures consistent build environments across different development machines
- Deploy-then-test strategy can be viable when local testing is problematic
- SAM templates provide single source of truth for serverless infrastructure
- Proper IAM permissions in SAM templates are critical for Lambda function execution
- ECS Fargate eliminates server management overhead for containerized applications
- Multi-AZ deployment ensures high availability for container services
- Service Connect simplifies internal service communication without load balancers
