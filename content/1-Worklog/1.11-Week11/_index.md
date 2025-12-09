---
title: "Worklog Week 11"
date: 2024-11-18
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives: 

* **Participate in AWS Cloud Mastery Series #2** to continue resolving specialized technical issues.
* **Refactor and standardize the Frontend structure** for improved stability and maintainability.
* **Implement a Multi-Stack architecture** to optimize deployment speed and Serverless project management.
* **Integrate basic CRUD functionalities with AI Image Processing** (using Rekognition) into the website.
* **Completely resolve deployment errors** (especially CORS issues) to stabilize the system.
* **Workshop: AI Service Architecture, Security & IAM, Monitoring** - Complete serverless AI pipeline setup.

---

### Tasks to be Deployed This Week:

| Day | Task | Start Date | Completion Date | Resources |
| :--- | :--- | :--- | :--- | :--- |
| Sun | - **Participate in AWS Cloud Mastery Series #2 (Nov 17th):** Continue receiving guidance and addressing deeper technical questions about authorization errors and the AI workflow. | 17/11/2024 | 17/11/2024 | Mentor, AWS Cloud Mastery Series |
| Mon | - **Frontend Structure Unification and Refactor:** Hold team meeting to standardize the Frontend code structure for maintainability. <br> - **Research Multi-Stack Solution:** Begin analyzing how to split the `template.yaml` file into smaller Stacks (Multi-Stack) to optimize the `sam deploy` process. | 18/11/2024 | 18/11/2024 | Serverless Architecture Docs |
| Tue | - **Implement Multi-Stack Architecture:** Start splitting and configuring separate Stacks (e.g., API Backend Stack, Frontend Hosting Stack). <br> - **Proceed with AI Image Processing Integration:** Combine basic CRUD functions with image processing logic (e.g., calling Rekognition API/S3 trigger) in preparation for the Update function. <br> - **Workshop Activity:** Set up API Gateway REST endpoints and SQS queues for asynchronous AI processing. | 19/11/2024 | 19/11/2024 | Backend Codebase, AWS Rekognition, [Workshop 5.7](5-Workshop/5.7-AI-Service/) |
| Wed | - **Error Encountered after AI Integration:** The system faced errors after combining AI functionality, necessitating a full Stack deletion and redeployment. <br> - **Leader Develops Backup Stack:** The team leader created a separate, optimized Multi-Stack as a contingency and reference for future optimal deployments. | 20/11/2024 | 20/11/2024 | Leader's Backup Stack |
| Thu | - **Persistent CORS Error:** After redeploying, the CORS issue re-emerged. <br> - **In-depth CORS Debugging:** Spent time thoroughly analyzing the root cause and permanently fixing the CORS error, ensuring correct header configuration on both API Gateway and Lambda. <br> - **Workshop Activity:** Configure IAM roles and policies for Lambda functions, set up Secrets Manager for API keys, and implement WAF rules. | 21/11/2024 | 21/11/2024 | API Gateway/Lambda Configuration, [Workshop 5.9](5-Workshop/5.9-Security-IAM/) |
| Fri | - **Team Meeting and Project Stabilization:** Held a team meeting to review the new Frontend structure, stabilize the main project Stack, and synchronize the fixes for CORS and basic template errors. <br> - **Optimization for Maintenance:** Finalized the solution to use a separate stack (developed by the leader) for flexibility and easier optimization in future development. | 22/11/2024 | 22/11/2024 | New Structure Report |

---

### Week 11 Achievements: 

* **Participated in AWS Cloud Mastery Series #2**, gaining deeper knowledge of Serverless, Rekognition, and solutions for authorization errors.
* **Successfully refactored the Frontend** and standardized the overall project structure, improving maintainability.
* **Implemented the Multi-Stack architecture** (or at least established a reliable solution/backup stack), which speeds up deployment and simplifies resource management.
* **Completely resolved the persistent CORS error** after identifying the root cause, ensuring stable communication between Frontend and Backend.
* **Acquired knowledge on fixing basic Template errors** and gained a clearer understanding of AWS SAM deployment issues.
* **Developed a separate stack for backup/optimization**, enhancing project flexibility and safety during future major updates.
* The project has moved into the AI functionality testing phase, and although errors were encountered, a clear path for troubleshooting has been established.

**Workshop Progress - AI Services, Security & Monitoring:**
- Configured API Gateway REST API with three endpoints: `/writing/evaluate`, `/speaking/evaluate`, `/flashcard/generate`
- Set up SQS queues for asynchronous message processing (writing-queue, speaking-queue, flashcard-queue)
- Deployed Lambda functions: `writing_evaluator`, `speaking_evaluator`, `rag_flashcard` with proper IAM roles
- Configured DynamoDB tables: `bandup-evaluations` and `bandup-flashcard-sets` for storing AI results
- Integrated Amazon Bedrock (Titan Embeddings V2) and Google Gemini API for AI processing
- Set up AWS Secrets Manager for secure API key storage
- Configured IAM roles with least-privilege permissions for Lambda functions
- Implemented AWS WAF rules for application-level protection
- Set up CloudWatch Logs and Alarms for monitoring Lambda execution and errors
- Configured CloudWatch Insights for log analysis and troubleshooting