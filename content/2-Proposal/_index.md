---
title: "Proposal"
date: 
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# IELTS Self-Learning Web System
## An Intelligent Platform for Independent IELTS Preparation

### 1. Executive Summary
The IELTS Self-Learning Web System is a comprehensive online platform designed to support students in their independent IELTS preparation journey. The platform provides an all-in-one solution featuring user management, educational blogs, interactive study rooms, practice tests, and AI-powered flashcard systems. Leveraging modern web technologies and AI integration, the system offers personalized learning experiences, real-time collaboration features, and automated assessment tools to help students achieve their IELTS goals efficiently.

### 2. Problem Statement
### What's the Problem?
Many IELTS learners face challenges in finding affordable, comprehensive, and interactive self-study platforms. Traditional learning methods lack real-time collaboration, personalized feedback, and integrated practice tools. Students often struggle with:
- Limited access to quality practice materials and mock tests
- Lack of immediate feedback on Speaking and Writing tasks
- Difficulty finding study partners and maintaining study discipline
- Fragmented resources across multiple platforms
- High costs of traditional IELTS preparation courses

### The Solution
The IELTS Self-Learning Web System provides a unified platform with five core features:

1. **User Management System**: Multi-tier membership (Guest, Member, Premium, Admin) with social authentication (Google, Facebook), personal profiles, and messaging capabilities.

2. **Educational Blog Platform**: Community-driven content with CRUD operations, genre/tag categorization, advanced filtering, commenting, reporting, and favoriting features.

3. **Interactive Study Rooms**: Virtual study spaces with scheduling, voice/video calls, Pomodoro timers, background music, dictionary integration, and translation support for enhanced learning.

4. **IELTS Practice Tests**: Comprehensive mock tests for Reading and Listening with automatic vocabulary extraction into flashcards, plus AI-powered assessment for Speaking and Writing tasks, and dictation exercises.

5. **Quizlet Flashcard System**: Intelligent flashcard creation with multiple study modes, automatic vocabulary extraction from texts, AI-generated quizzes from uploaded materials, and sharing capabilities.

### Benefits and Return on Investment
- **For Students**: Cost-effective alternative to expensive courses, personalized learning paths, 24/7 access to study materials, AI-powered feedback, and collaborative learning environment.
- **Educational Value**: Develops self-discipline, provides comprehensive IELTS preparation, enables peer learning, and offers trackable progress.
- **Technical Benefits**: Scalable architecture, modern tech stack, AI integration for automated assessment, and potential for future feature expansion.
- **Market Potential**: Growing demand for online IELTS preparation, subscription-based revenue model (Guest → Member → Premium), and potential for partnerships with educational institutions.

### 3. Solution Architecture
The platform employs a modern full-stack web architecture designed for scalability, real-time collaboration, and AI integration. The system consists of five major modules working together to provide a comprehensive IELTS learning experience. The infrastructure uses an **active-passive Multi-AZ** deployment on AWS ECS for high availability, where AZ-1 handles all active traffic and AZ-2 serves as a standby for automatic failover.

**System Architecture Overview:**
![System Architecture](/images/2-Proposal/AWS-Bandup-Architecture.png)

#### 3.1. Architecture Overview
The IELTS Self-Learning Web System is built on AWS cloud infrastructure with a multi-layered architecture that ensures high availability, scalability, and security. The system follows a microservices-inspired approach with a monolithic backend for core services and serverless architecture for AI-powered features.

**Key Architecture Principles:**
- **High Availability**: Active-passive Multi-AZ deployment with automatic failover
- **Scalability**: Horizontal scaling with ECS Auto Scaling and serverless Lambda functions
- **Security**: Multi-layer security with WAF, VPC isolation, and encryption at rest and in transit
- **Performance**: CDN distribution, caching layers, and optimized database queries
- **Cost Efficiency**: Serverless AI services, active-passive standby resources, and pay-per-use pricing

#### 3.2. Network Architecture
The network infrastructure is built within a **Virtual Private Cloud (VPC)** spanning two Availability Zones (AZ-1 and AZ-2) for redundancy and high availability.

**VPC Structure:**
- **Public Subnets (AZ-1 & AZ-2)**: 
  - Application Load Balancer (ALB) endpoints
  - NAT Gateways for outbound internet access
  - Bastion hosts for secure access (optional)
- **Private Subnets (AZ-1 & AZ-2)**:
  - ECS Fargate tasks (Next.js frontend and Spring Boot backend)
  - Amazon RDS PostgreSQL instances
  - Amazon ElastiCache Redis clusters
  - Internal service communication

**Network Components:**
- **Route 53**: DNS management and domain routing
- **AWS Certificate Manager (ACM)**: SSL/TLS certificates for HTTPS
- **AWS WAF**: Web Application Firewall protecting against common web exploits
- **Internet Gateway**: Public internet access for public subnets
- **NAT Gateways**: Outbound internet access for private subnets
- **Security Groups**: Stateful firewall rules controlling traffic between components
- **Network ACLs**: Additional layer of subnet-level security

**Traffic Flow:**
1. User requests → Route 53 → CloudFront CDN (for static assets)
2. API requests → Route 53 → AWS WAF → Application Load Balancer
3. ALB routes to ECS tasks in private subnets (AZ-1 active, AZ-2 standby)
4. ECS tasks communicate with RDS, ElastiCache, and S3 via private subnets
5. AI service requests → API Gateway → SQS → Lambda functions

#### 3.3. Application Architecture
The application layer consists of frontend and backend services deployed on **Amazon ECS (Fargate)** with an active-passive Multi-AZ configuration.

**Frontend Layer (Next.js):**
- **Deployment**: Containerized Next.js application on ECS Fargate
- **Location**: Private subnets in AZ-1 (active) and AZ-2 (standby)
- **Features**:
  - Server-side rendering (SSR) for SEO optimization
  - Static asset delivery via CloudFront CDN
  - Real-time WebRTC for study room video/voice calls
  - Socket.io client for real-time messaging
  - Responsive design for mobile and desktop

**Backend Layer (Spring Boot):**
- **Deployment**: Monolithic Spring Boot REST API on ECS Fargate
- **Location**: Private subnets in AZ-1 (active) and AZ-2 (standby)
- **Components**:
  - RESTful API endpoints for all modules
  - Spring Security for authentication and authorization
  - Spring WebSocket for real-time features
  - JWT token management
  - OAuth 2.0 integration (Google, Facebook)
  - File upload handling for S3 integration

**Load Balancing & High Availability:**
- **Application Load Balancer (ALB)**:
  - Routes traffic to healthy ECS tasks in AZ-1 (active)
  - Health checks every 30 seconds
  - Automatic failover to AZ-2 if AZ-1 becomes unavailable
  - SSL/TLS termination with ACM certificates
  - Sticky sessions for WebSocket connections

**Container Orchestration:**
- **Amazon ECS (Fargate)**:
  - No server management required
  - Auto Scaling based on CPU/memory utilization
  - Task definitions for Next.js and Spring Boot containers
  - Service discovery and load balancing integration
  - Container images stored in Amazon ECR

#### 3.4. Data Architecture
The data layer uses a combination of relational and NoSQL databases, along with caching for optimal performance.

**Primary Database:**
- **Amazon RDS PostgreSQL (Multi-AZ)**:
  - **Primary Instance**: db.t3.medium in AZ-1 (active)
  - **Standby Instance**: db.t3.medium in AZ-2 (passive, synchronous replication)
  - **Data**: Users, blogs, study sessions, practice tests, flashcards metadata
  - **Backup**: Automated daily backups with 7-day retention
  - **High Availability**: Automatic failover to standby in < 60 seconds
  - **Encryption**: At rest (AES-256) and in transit (SSL/TLS)

**Caching Layer:**
- **Amazon ElastiCache (Redis)**:
  - Session management and user authentication tokens
  - Frequently accessed data caching (blog posts, user profiles)
  - Real-time leaderboards and statistics
  - Rate limiting and API throttling
  - Cache invalidation strategies for data consistency

**Object Storage:**
- **Amazon S3**:
  - User-uploaded files (images, documents, audio recordings)
  - Static assets (before CloudFront distribution)
  - Backup storage for RDS snapshots
  - Lifecycle policies for cost optimization (move to Glacier after 90 days)
  - Versioning enabled for critical files

**NoSQL Database (AI Services):**
- **Amazon DynamoDB**:
  - AI assessment results (Writing and Speaking scores)
  - Generated flashcards from RAG pipeline
  - User progress tracking for AI features
  - On-demand scaling for variable workloads
  - Point-in-time recovery enabled

**Vector Store (RAG):**
- **Amazon OpenSearch Service** (or **Amazon Bedrock Knowledge Base**):
  - Document embeddings generated by Amazon Titan V2
  - Semantic search for relevant document chunks
  - Vector similarity search for flashcard generation
  - Index management and query optimization

#### 3.5. Security Architecture
Multi-layer security ensures data protection and system integrity at every level.

**Network Security:**
- **AWS WAF**: Protection against SQL injection, XSS, DDoS attacks
- **Security Groups**: Restrictive firewall rules (least privilege principle)
- **Network ACLs**: Subnet-level traffic filtering
- **VPC Isolation**: Private subnets with no direct internet access
- **VPN/Bastion Hosts**: Secure administrative access (optional)

**Data Security:**
- **Encryption at Rest**:
  - RDS: AES-256 encryption
  - S3: Server-side encryption (SSE-S3)
  - DynamoDB: Encryption at rest enabled
  - ElastiCache: Encryption in transit and at rest
- **Encryption in Transit**:
  - HTTPS/TLS 1.2+ for all external communications
  - SSL/TLS for database connections
  - ACM certificates for all domains

**Identity & Access Management:**
- **AWS IAM**: Role-based access control for AWS services
  - ECS tasks use IAM roles (no hardcoded credentials)
  - Lambda functions have minimal required permissions
  - S3 bucket policies for access control
- **Spring Security**: Application-level authentication
  - JWT tokens for stateless authentication
  - OAuth 2.0 for social login (Google, Facebook)
  - Role-based access control (Guest, Member, Premium, Admin)
- **AWS Secrets Manager**: Secure storage of API keys and credentials
  - Database passwords
  - Third-party API keys (Gemini, dictionary APIs)
  - OAuth client secrets

**Application Security:**
- **Input Validation**: All user inputs validated and sanitized
- **SQL Injection Prevention**: Parameterized queries with Spring Data JPA
- **XSS Protection**: Content Security Policy (CSP) headers
- **CSRF Protection**: Spring Security CSRF tokens
- **Rate Limiting**: API throttling with Redis
- **Audit Logging**: CloudWatch Logs for security events

#### 3.6. CI/CD Pipeline
Automated deployment pipeline ensures consistent and reliable releases.

**Source Control:**
- **Git Repository**: Code versioning and collaboration
- **Branch Strategy**: Main branch for production, feature branches for development

**CI/CD Components:**
- **GitLab Webhook** (or **GitHub Actions**): Triggers pipeline on code push
- **AWS CodePipeline**: Orchestrates the deployment workflow
- **AWS CodeBuild**: Builds and tests application code
  - Frontend: `npm install && npm run build`
  - Backend: `mvn clean package`
  - Docker image creation and tagging
- **Amazon ECR**: Container registry for Docker images
  - Stores Next.js and Spring Boot container images
  - Image versioning and lifecycle policies

**Deployment Flow:**
1. Developer pushes code to repository
2. Webhook triggers CodePipeline
3. CodeBuild compiles and tests code
4. CodeBuild creates Docker images and pushes to ECR
5. ECS service updates with new task definitions
6. Rolling deployment: New tasks start, health checks pass, old tasks terminate
7. CloudWatch monitors deployment success/failure

**Infrastructure as Code:**
- **AWS CloudFormation** (or **Terraform**): Infrastructure provisioning
  - VPC, subnets, security groups
  - ECS clusters, services, task definitions
  - RDS instances, ElastiCache clusters
  - Lambda functions, API Gateway, SQS queues
  - IAM roles and policies

#### 3.7. Monitoring & Observability
Comprehensive monitoring ensures system health and performance.

**Application Monitoring:**
- **Amazon CloudWatch**:
  - **Metrics**: CPU, memory, network utilization for ECS tasks
  - **Logs**: Application logs from Next.js and Spring Boot
  - **Alarms**: Automated alerts for errors, high latency, resource exhaustion
  - **Dashboards**: Custom dashboards for key metrics
- **AWS X-Ray** (optional): Distributed tracing for request flow analysis

**Infrastructure Monitoring:**
- **CloudWatch Metrics**:
  - RDS: CPU, memory, connection count, read/write latency
  - ElastiCache: CPU, memory, cache hit ratio
  - ALB: Request count, response time, error rates
  - Lambda: Invocations, duration, errors, throttles
  - SQS: Queue depth, message age, DLQ size
  - API Gateway: Request count, latency, 4xx/5xx errors

**Logging:**
- **CloudWatch Logs**:
  - Application logs (structured JSON format)
  - Access logs from ALB
  - API Gateway execution logs
  - Lambda function logs
  - Log retention: 30 days (configurable)

**Alerting:**
- **CloudWatch Alarms**:
  - High error rate (> 5% 5xx errors)
  - High latency (> 2 seconds p95)
  - Low availability (< 99% uptime)
  - Resource exhaustion (CPU > 80%, Memory > 85%)
  - Database connection pool exhaustion
  - SQS queue depth exceeding threshold
- **SNS Notifications**: Email/SMS alerts for critical alarms

**Performance Optimization:**
- **CloudFront CDN**: Caching static assets at edge locations
- **ElastiCache**: Reducing database load with intelligent caching
- **Database Query Optimization**: Indexed queries, connection pooling
- **Auto Scaling**: ECS tasks scale based on CPU/memory metrics

### Technology Stack
**Frontend:**
- **Next.js 14+**: Modern React framework for responsive web application
- **TypeScript**: Type-safe development
- **TailwindCSS**: Utility-first styling
- **WebRTC**: Real-time video/voice communication
- **Socket.io Client**: Real-time messaging and collaboration

**Backend:**
- **Spring Boot 3.x**: Monolithic RESTful API architecture
- **Java 17+**: Backend programming language
- **Spring Security**: Authentication and authorization
- **Spring WebSocket**: Real-time communication
- **JWT**: Secure token-based authentication
- **OAuth 2.0**: Social login integration (Google, Facebook)

**Database:**
- **PostgreSQL 14+**: Primary relational database for all data (users, tests, blogs, flashcards, study sessions)
- **Amazon ElastiCache (Redis)**: Caching and session management

**Cloud Infrastructure (AWS):**
- **Amazon ECS (Elastic Container Service)**: Container orchestration with active-passive Multi-AZ deployment
- **Application Load Balancer**: Routes traffic to active AZ with automatic failover
- **Amazon RDS for PostgreSQL**: Multi-AZ deployment (active primary in AZ-1, passive standby in AZ-2)
- **Amazon S3**: Media and file storage
- **Amazon CloudFront**: CDN for static assets
- **Amazon CloudWatch**: Monitoring and logging
- **Amazon API Gateway**: RESTful API endpoint for AI service requests
- **Amazon SQS**: Message queue for asynchronous AI processing with Dead Letter Queue (DLQ)
- **AWS Lambda**: Serverless functions for AI-powered assessments and flashcard generation
- **Amazon DynamoDB**: NoSQL database for storing AI assessment results and generated flashcards
- **Amazon Bedrock**: AI model service for embeddings (Titan V2) and content generation
- **Amazon OpenSearch Service** (optional): Vector store for RAG-based document embeddings and semantic search

**Third-party Services:**
- **Google Gemini Flash API (Free Tier)**: AI-powered Speaking/Writing assessment and smart query generation for RAG
- **Amazon Bedrock (GPT-OSS)**: Alternative AI model for assessments and content generation
- **Amazon Titan V2 Embeddings**: Vector embeddings for document chunks in RAG-based flashcard generation
- **Free Dictionary APIs**: Word definitions and examples
- **Open-source translation libraries**: Context-aware translation (alternative to paid APIs)

#### 3.9. Component Design
The system consists of five major functional modules, each integrated with the core architecture described above. Each module leverages the shared infrastructure (ECS, RDS, ElastiCache, S3) while maintaining clear separation of concerns.

**1. User Management Module:**
- Multi-tier authentication system (Guest, Member, Premium, Admin)
- Social OAuth integration (Google, Facebook)
- User profile management with learning statistics
- Real-time messaging system
- Password recovery and email verification

**2. Blog Platform Module:**
- CRUD operations for blog posts
- Genre and tag management system
- Advanced search and filtering (by tags, genres, date, popularity)
- Comment system with nested replies
- Content reporting and moderation
- Favorite/bookmark functionality
- SEO-optimized content delivery

**3. Study Room Module:**
- Virtual study room creation and management
- Study session scheduling with calendar integration
- WebRTC-based voice and video calls
- Pomodoro timer with customizable intervals
- Background music library with focus playlists
- Integrated dictionary with free dictionary APIs
- Translation support using open-source libraries
- Screen sharing for collaborative study
- Real-time participant management

**4. IELTS Practice Test Module:**
- Test bank management (Reading, Listening, Speaking, Writing) - stored in PostgreSQL
- Timed test simulations with auto-submission
- Automatic vocabulary extraction from Reading passages to flashcards
- **AI-powered Speaking assessment** (see Section 3.8, Lambda Function 2):
  - Audio recordings uploaded to S3
  - Processed asynchronously via API Gateway → SQS → Lambda Function 2
  - Uses Amazon Transcribe for speech-to-text
  - Evaluates pronunciation, fluency, coherence, lexical resource
  - Results stored in DynamoDB and displayed to user
- **AI-powered Writing assessment** (see Section 3.8, Lambda Function 1):
  - Writing samples submitted via API Gateway → SQS → Lambda Function 1
  - Evaluates grammar, vocabulary, task achievement, coherence
  - Results stored in DynamoDB with detailed feedback
- Dictation exercises for Listening practice
- Detailed score reports and analytics (aggregated from DynamoDB)
- Progress tracking across test types (stored in PostgreSQL, cached in Redis)

**5. Quizlet Flashcard Module:**
- CRUD operations for flashcard sets (stored in PostgreSQL)
- Multiple study modes (flashcards, learn, test, match, write)
- Spaced repetition algorithm (SRS) for optimized learning
- Automatic vocabulary extraction from text passages
- **AI-generated flashcards** from uploaded documents using RAG (see Section 3.8)
  - Documents uploaded to S3
  - Processed asynchronously via API Gateway → SQS → Lambda Function 3
  - Generated flashcards stored in DynamoDB and linked to user's sets
- Collaborative flashcard sets with sharing
- Study statistics and mastery tracking (cached in Redis)
- Import/export functionality

#### 3.8. AI Service Architecture (Serverless)
The AI service is implemented as a fully serverless architecture using AWS services to handle AI-powered assessments and content generation. This architecture follows an asynchronous processing pattern for scalability, cost-efficiency, and reliability.

**Architecture Components:**

**1. API Gateway (Entry Point)**
- **Purpose**: RESTful API endpoint for AI service requests
- **Configuration**:
  - REST API with custom domain (optional)
  - API keys for rate limiting and access control
  - CORS configuration for frontend integration
  - Request/response transformation
  - Integration with SQS for asynchronous processing
- **Endpoints**:
  - `POST /ai/writing-assessment` - Submit writing samples
  - `POST /ai/speaking-assessment` - Submit audio recordings
  - `POST /ai/generate-flashcards` - Upload documents for flashcard generation
- **Security**: IAM authentication or API keys

**2. Amazon SQS (Message Queue)**
- **Purpose**: Decouples API Gateway from Lambda functions for asynchronous processing
- **Configuration**:
  - Standard queue for high throughput
  - Message retention: 14 days
  - Visibility timeout: 5 minutes (adjustable per function)
  - Dead Letter Queue (DLQ) for failed messages after 3 retries
- **Message Format**: JSON payloads containing:
  - User ID and request metadata
  - File references (S3 URIs for documents/audio)
  - Processing parameters
- **Benefits**:
  - Handles traffic spikes without overwhelming Lambda
  - Automatic retry mechanism for transient failures
  - Cost-effective message queuing

**3. AWS Lambda Functions (Processing Layer)**
Three specialized Lambda functions process different types of AI tasks, each optimized for its specific workload:

**Lambda Function 1: Writing Assessment**
- **Trigger**: SQS queue messages for writing assessments
- **Runtime**: Python 3.11 or Node.js 18.x
- **Memory**: 512 MB - 1 GB (configurable)
- **Timeout**: 5 minutes
- **Processing Flow**:
  1. Receives writing sample from SQS message
  2. Retrieves document from S3 if needed
  3. Calls **Amazon Bedrock (GPT-OSS)** or **Google Gemini Flash API** with prompt:
     - Task description and IELTS band descriptors
     - Writing sample text
     - Evaluation criteria (grammar, vocabulary, task achievement, coherence)
  4. Parses AI response for structured assessment:
     - Overall band score (0-9)
     - Detailed scores per criterion
     - Feedback comments and suggestions
  5. Stores results in **DynamoDB** with:
     - User ID, timestamp, writing sample reference
     - Assessment scores and feedback
     - Processing metadata
  6. Sends notification (optional) via SNS or updates user via WebSocket

**Lambda Function 2: Speaking Assessment**
- **Trigger**: SQS queue messages for speaking assessments
- **Runtime**: Python 3.11 or Node.js 18.x
- **Memory**: 1 GB - 2 GB (for audio processing)
- **Timeout**: 15 minutes (for longer audio files)
- **Processing Flow**:
  1. Receives audio file reference from SQS message
  2. Retrieves audio file from S3
  3. **Audio Transcription** (if needed):
     - Uses **Amazon Transcribe** for speech-to-text conversion
     - Supports multiple languages and accents
     - Generates transcript with timestamps
  4. Calls **Amazon Bedrock (GPT-OSS)** or **Google Gemini Flash API** with:
     - Transcript text
     - Audio metadata (duration, sample rate)
     - IELTS speaking band descriptors
     - Evaluation criteria (pronunciation, fluency, coherence, lexical resource)
  5. Parses AI response for structured assessment:
     - Overall band score (0-9)
     - Detailed scores per criterion
     - Pronunciation analysis
     - Fluency and coherence feedback
  6. Stores results in **DynamoDB** with:
     - User ID, timestamp, audio file reference
     - Transcript and assessment scores
     - Detailed feedback
  7. Sends notification or updates user status

**Lambda Function 3: Flashcard Generation (RAG-based)**
- **Trigger**: SQS queue messages for document uploads
- **Runtime**: Python 3.11 (for ML/AI libraries)
- **Memory**: 2 GB - 3 GB (for document processing)
- **Timeout**: 15 minutes (for large documents)
- **Processing Flow**:

  **Step 1: Document Processing & Embedding**
  - Receives document file reference from SQS
  - Retrieves document from S3 (PDF, DOCX, TXT formats)
  - **Document Chunking**:
    - Splits document into semantic chunks (500-1000 tokens each)
    - Preserves context and structure
    - Handles tables, lists, and formatted content
  - **Vector Embedding Generation**:
    - Uses **Amazon Titan V2 Embeddings** model via Bedrock
    - Generates 1024-dimensional vectors for each chunk
    - Maintains chunk metadata (position, section, page number)
  - **Vector Storage**:
    - Stores embeddings in **Amazon OpenSearch Service** (vector index)
    - Alternative: **Amazon Bedrock Knowledge Base** (managed service)
    - Index structure: Document ID, chunk ID, embedding vector, metadata

  **Step 2: Smart Query Generation**
  - Uses **Google Gemini Flash API** to analyze document content
  - **Query Generation Process**:
    - Analyzes document structure and content
    - Identifies key concepts, important facts, learning points
    - Generates 10-20 intelligent, context-aware queries
    - Queries designed to extract:
      - Definitions and explanations
      - Important dates, facts, and figures
      - Concepts and relationships
      - Examples and use cases
  - **Query Optimization**:
    - Ensures queries are specific and answerable
    - Covers different difficulty levels
    - Balances factual and conceptual questions

  **Step 3: RAG-based Flashcard Generation**
  - **Retrieval Phase**:
    - For each generated query, performs semantic search in vector store
    - Retrieves top 3-5 most relevant document chunks
    - Uses cosine similarity for vector matching
  - **Augmentation Phase**:
    - Combines query with retrieved chunks
    - Adds context and metadata
    - Formats prompt for flashcard generation
  - **Generation Phase**:
    - Sends query-context pairs to **Google Gemini Flash** or **Amazon Bedrock**
    - Prompt includes:
      - Query/question to answer
      - Relevant document chunks
      - Flashcard format requirements (question-answer pairs)
      - Educational guidelines
    - Model generates flashcards that are:
      - Relevant to document content
      - Contextually accurate
      - Educationally valuable
      - Properly formatted (question-answer pairs)
      - Appropriate difficulty level
  - **Post-processing**:
    - Validates flashcard quality
    - Removes duplicates
    - Formats for storage
  - **Storage**:
    - Stores generated flashcards in **DynamoDB**
    - Associates with user's flashcard sets
    - Links to original document
    - Stores metadata (generation date, source chunks)

**4. Amazon DynamoDB (Data Storage)**
- **Purpose**: Stores AI assessment results and generated flashcards
- **Table Design**:
  - **WritingAssessments Table**:
    - Partition Key: `userId` (String)
    - Sort Key: `timestamp` (Number)
    - Attributes: writingSample, scores, feedback, metadata
  - **SpeakingAssessments Table**:
    - Partition Key: `userId` (String)
    - Sort Key: `timestamp` (Number)
    - Attributes: audioFile, transcript, scores, feedback, metadata
  - **GeneratedFlashcards Table**:
    - Partition Key: `userId` (String)
    - Sort Key: `flashcardId` (String)
    - Attributes: question, answer, sourceDocument, sourceChunks, metadata
- **Features**:
  - On-demand scaling for variable workloads
  - Point-in-time recovery enabled
  - Encryption at rest
  - TTL for automatic cleanup of old data

**5. Amazon Bedrock (AI Model Service)**
- **Models Used**:
  - **Amazon Titan V2 Embeddings**: Vector embeddings for document chunks
  - **GPT-OSS (Open Source)**: Alternative model for assessments and generation
- **Integration**: Direct API calls from Lambda functions
- **Cost**: Pay-per-use pricing model

**6. Amazon OpenSearch Service (Vector Store)**
- **Purpose**: Semantic search and retrieval for RAG pipeline
- **Configuration**:
  - Vector index for document embeddings
  - KNN (k-nearest neighbors) search capability
  - Index management and optimization
- **Alternative**: Amazon Bedrock Knowledge Base (fully managed)

**7. Amazon CloudWatch (Monitoring)**
- **Lambda Metrics**:
  - Invocations, duration, errors, throttles
  - Memory utilization, concurrent executions
- **SQS Metrics**:
  - Queue depth, message age
  - DLQ message count
- **API Gateway Metrics**:
  - Request count, latency, 4xx/5xx errors
- **Alarms**: Automated alerts for:
  - High error rates
  - Queue depth exceeding threshold
  - Lambda timeouts
  - API Gateway throttling

**Data Flow Summary:**
1. User submits request → **API Gateway** (validates and queues)
2. Request queued in **SQS** (decoupled processing)
3. **Lambda function** triggered by SQS message
4. Lambda processes request using **AI services** (Bedrock/Gemini)
5. Results stored in **DynamoDB**
6. User notified or results retrieved via API
7. **CloudWatch** monitors entire flow

**Benefits of Serverless Architecture:**
- **Scalability**: Auto-scales with request volume
- **Cost Efficiency**: Pay only for actual usage
- **Reliability**: Built-in retry mechanisms and DLQ
- **Maintenance**: No server management required
- **Performance**: Low latency with optimized cold starts

### 4. Technical Implementation
**Implementation Phases**

**Phase 1: Planning and Design (Weeks 1-2)**
- Requirements gathering and user story definition
- System architecture design and AWS infrastructure planning
- Database schema design for PostgreSQL
- UI/UX wireframes and mockups
- API endpoint design for Spring Boot
- Third-party service evaluation and integration planning
- Multi-AZ architecture setup on AWS ECS

**Phase 2: Core Development (Weeks 3-6)**
- Spring Boot application setup with monolithic architecture
- User authentication and authorization with Spring Security
- PostgreSQL database setup on Amazon RDS (Multi-AZ: active-passive)
- JWT and OAuth 2.0 integration (Google, Facebook)
- Next.js frontend setup with TypeScript
- Frontend component library creation
- Basic CRUD operations for all modules
- AWS ECS cluster configuration with active-passive failover

**Phase 3: Feature Development (Weeks 7-10)**
- Blog platform with advanced filtering and search
- Study room creation with WebRTC integration
- Practice test module with automatic grading logic
- Flashcard system with spaced repetition algorithm
- Real-time messaging with Spring WebSocket
- Dictionary and Google Translate API integration
- Amazon S3 integration for file uploads
- ElastiCache Redis for session management and caching

**Phase 4: AI Integration & Deploying (Weeks 11-12)**
- **AI Service Infrastructure Setup:**
  - Amazon API Gateway configuration for AI endpoints
  - Amazon SQS queue setup with Dead Letter Queue (DLQ)
  - AWS Lambda function development (3 functions)
  - Amazon DynamoDB table design for AI results storage
- **Lambda Function 1: Writing Assessment**
  - Integration with Google Gemini Flash API or Amazon Bedrock
  - Writing evaluation logic (grammar, vocabulary, task achievement, coherence)
  - Result storage in DynamoDB
- **Lambda Function 2: Speaking Assessment**
  - Audio transcription integration
  - Integration with Google Gemini Flash API or Amazon Bedrock
  - Speaking evaluation logic (pronunciation, fluency, coherence, lexical resource)
  - Result storage in DynamoDB
- **Lambda Function 3: RAG-based Flashcard Generation**
  - Document chunking algorithm implementation
  - Amazon Titan V2 Embeddings integration for vector generation
  - Vector store setup (Amazon OpenSearch Service or Bedrock Knowledge Base)
  - Google Gemini integration for smart query generation
  - RAG pipeline: Query generation → Document retrieval → Flashcard generation
  - Flashcard storage in DynamoDB
- Automatic vocabulary extraction algorithms
- Comprehensive testing (unit, integration, end-to-end)
- Performance optimization and load testing
- Security audit and bug fixes
- Production deployment to AWS ECS with Multi-AZ
- Monitoring setup with CloudWatch for Lambda, SQS, and API Gateway

**Technical Requirements**

**Development Environment:**
- Java 17+ for Spring Boot backend
- Node.js 18+ for Next.js frontend
- PostgreSQL 14+ for database
- Docker for local containerization
- Git for version control
- Maven for Java dependency management

**Frontend Requirements:**
- Next.js 14+ with App Router and TypeScript
- WebRTC for real-time video/voice communication
- Socket.io client for real-time features
- Form validation (React Hook Form + Zod)

**Backend Requirements:**
- Spring Boot 3.x with Java 17+
- Spring Data JPA for database operations
- Spring Security for authentication/authorization
- Spring WebSocket for real-time features
- Spring Web for RESTful APIs
- JWT for token-based authentication
- OAuth 2.0 for social login
- Multipart file upload handling

**AWS Infrastructure:**
- **Amazon ECS**: Fargate launch type for containerized applications
- **Application Load Balancer**: Routes traffic to active AZ with health checks
- **Amazon RDS PostgreSQL**: Multi-AZ active-passive deployment for high availability
- **Amazon ElastiCache (Redis)**: Session and cache management
- **Amazon S3**: Media file storage
- **Amazon CloudFront**: CDN for static assets
- **Amazon CloudWatch**: Logging and monitoring
- **Amazon VPC**: Network isolation with public/private subnets across 2 AZs
- **AWS Certificate Manager**: SSL/TLS certificates
- **Amazon API Gateway**: RESTful API for AI service endpoints
- **Amazon SQS**: Message queue for asynchronous AI processing with DLQ
- **AWS Lambda**: Serverless compute for AI functions (3 functions: Writing, Speaking, Flashcard Generation)
- **Amazon DynamoDB**: NoSQL database for AI assessment results and flashcards
- **Amazon Bedrock**: AI model service (Titan V2 Embeddings, GPT-OSS)
- **Amazon OpenSearch Service** (optional): Vector store for RAG document embeddings

**AI/ML Integration:**
- **Google Gemini Flash API (Free Tier)**: 
  - Writing assessment (grammar, vocabulary, task achievement)
  - Speaking assessment (pronunciation, fluency, coherence)
  - Smart query generation for RAG-based flashcard creation
- **Amazon Bedrock (GPT-OSS)**: 
  - Alternative AI model for assessments
  - Flashcard generation from query-context pairs
- **Amazon Titan V2 Embeddings**: 
  - Vector embeddings for document chunks
  - Semantic search and retrieval for RAG
- **RAG (Retrieval-Augmented Generation) Architecture**:
  - Document chunking and embedding generation
  - Vector store for semantic search (OpenSearch Service or Bedrock Knowledge Base)
  - Context-aware flashcard generation using smart queries + document chunks

### 5. Timeline & Milestones

**Project Timeline: 3 Months (12 Weeks)**

**Weeks 1-2: Planning & Design**
- ✓ Requirements analysis and documentation
- ✓ AWS Multi-AZ architecture design
- ✓ PostgreSQL database schema design
- ✓ UI/UX mockups completion
- ✓ Spring Boot project structure setup
- Deliverable: Complete technical specification document and AWS infrastructure plan

**Weeks 3-6: Core Development**
- ✓ Spring Boot monolithic backend setup
- ✓ Spring Security implementation (JWT, OAuth 2.0)
- ✓ User management and role-based access control
- ✓ Amazon RDS PostgreSQL Multi-AZ deployment
- ✓ Next.js frontend framework and component library
- ✓ AWS ECS cluster setup with Application Load Balancer
- ✓ ElastiCache Redis configuration
- Deliverable: Working authentication system and AWS infrastructure

**Weeks 7-10: Feature Development**
- ✓ Blog platform with full CRUD functionality
- ✓ Study room creation and management
- ✓ WebRTC integration for video/voice calls
- Practice test module (Reading & Listening)
- Flashcard system with study modes
- Amazon S3 integration for media storage
- Free dictionary API and open-source translation integration
- Spring WebSocket for real-time features
- Deliverable: All five core features operational (without AI)

**Weeks 11-12: AI Integration & Deployment**
- ✓ Amazon API Gateway and SQS setup for AI service architecture
- ✓ AWS Lambda Function 1: Writing Assessment with Gemini/Bedrock integration
- ✓ AWS Lambda Function 2: Speaking Assessment with Gemini/Bedrock integration
- ✓ AWS Lambda Function 3: RAG-based Flashcard Generation
  - Document chunking and Amazon Titan V2 Embeddings integration
  - Google Gemini smart query generation
  - RAG pipeline with vector retrieval and flashcard generation
- ✓ Amazon DynamoDB setup for AI results storage
- ✓ Amazon OpenSearch Service or Bedrock Knowledge Base for vector store
- ✓ Vocabulary extraction algorithms
- ✓ Comprehensive testing (unit, integration, E2E)
- ✓ Performance optimization and security audit
- ✓ Production deployment to AWS ECS Multi-AZ
- ✓ CloudWatch monitoring and alerting setup for Lambda, SQS, API Gateway
- ✓ Final bug fixes and documentation
- Deliverable: Production-ready application deployed on AWS with complete AI service architecture

**Key Milestones:**
1. Week 2: Technical specification and AWS architecture approved
2. Week 6: Core backend and infrastructure deployed
3. Week 10: All features complete (beta version)
4. Week 12: Production launch with AI integration

**Weekly Sprint Goals:**
- Sprint 1-2: Architecture and setup
- Sprint 3-4: Authentication and database
- Sprint 5-6: Core API and AWS deployment
- Sprint 7-8: Blog and study room features
- Sprint 9-10: Practice tests and flashcards
- Sprint 11: AI integration and testing
- Sprint 12: Production deployment and launch

### 6. Budget Estimation

**Development Costs (One-time)**

**Software & Tools:**
- Development tools and licenses: $0 (using free/open-source tools)
- Design tools (Figma Free): $0
- Project management tools: $0 (using free tier)
- **Total Software: $0**

**Initial Setup:**
- Domain registration: $1/year
- SSL certificate: $0 (AWS Certificate Manager - Free)
- Development servers: $0 (local development)
- **Total Initial Setup: $1**

**Operating Costs (Monthly)**

**Infrastructure & Hosting (AWS):**
- **Amazon ECS (Fargate)**:
  - 2 tasks (Next.js + Spring Boot) in active AZ-1: $30/month
  - 2 standby tasks in passive AZ-2 (minimal usage): $10/month
  - Application Load Balancer: $25/month
- **Amazon RDS PostgreSQL (Multi-AZ active-passive)**:
  - db.t3.medium instance (primary + standby): $85/month
  - Storage (100 GB): $12/month
- **Amazon ElastiCache (Redis)**:
  - cache.t3.micro: $15/month
- **Amazon S3**:
  - Storage (50 GB): $1.15/month
  - Data transfer: $5/month
- **Amazon CloudFront (CDN)**: $10/month
- **Amazon CloudWatch**: $10/month
- **Amazon API Gateway**: $3.50/month (1M requests)
- **Amazon SQS**: $0.40/month (1M requests)
- **AWS Lambda**: $5/month (3 functions, 1M requests, 512MB memory)
- **Amazon DynamoDB**: $5/month (25 GB storage, 25 RCU/WCU)
- **Amazon Bedrock (Titan V2 Embeddings)**: $10/month (estimated usage)
- **Amazon OpenSearch Service** (optional, for vector store): $15/month (t3.small.search)
- **Data Transfer (outbound)**: $15/month
- **VPC & Networking**: $5/month
- **Subtotal AWS Infrastructure: $290.90/month**

**Third-party Services:**
- **Google Gemini Flash API**: Free tier
- **Subtotal Services: $0/month**

**Other Operating Costs:**
- Monitoring & analytics: $10/month (integrated with CloudWatch)
- Backup storage (RDS automated backups): $5/month
- Domain & SSL renewal: $0.08/month (amortized from $1/year, SSL via AWS Certificate Manager - Free)
- **Subtotal Other: $15.08/month**

**Total Monthly Operating Costs: $305.98/month**

**Annual Budget Summary**

**Development Phase (3 Months):**
- Development costs: $1 (one-time, domain only)
- Operating costs (3 months): $917.94 ($305.98 × 3 months)
- **Total Development Phase: $918.94**

**Year 1 (After Launch):**
- Development costs: $1 (one-time, domain only)
- Operating costs: $3,671.76 ($305.98 × 12 months)
- **Total Year 1: $3,672.76**

**Year 2+ (Annual Recurring):**
- Operating costs: $3,671.76/year
- Domain renewal: $1/year
- **Total Annual: $3,672.76**

**Revenue Projections (Subscription Model)**

**Membership Tiers:**
- Guest: Free (limited features)
- Member: $5/month (basic features)
- Premium: $15/month (all features + AI assessments)

**Conservative Revenue Estimate (Year 1):**
- Month 6-12: Average 100 Premium + 200 Members
- Revenue: (100 × $15 + 200 × $5) × 7 months = $17,500
- Operating costs (Year 1): $3,671.76
- **Net Profit Year 1: $13,828.24**
- Break-even: Month 1 after launch

**Optimistic Revenue Estimate (Year 2):**
- 500 Premium + 1,000 Members
- Monthly revenue: $12,500
- Annual revenue: $150,000
- Operating costs: $3,671.76
- **Net Profit Year 2: $146,328.24**
- Profit margin: ~97.6% after operating costs

**Cost Optimization Strategies:**
- Zero personnel costs (self-developed project)
- Active-passive Multi-AZ deployment reduces costs (standby resources only used during failover)
- Use AWS ECS Fargate Spot for development environments (70% cost savings)
- Implement caching with ElastiCache to reduce database queries
- Use CloudFront CDN to minimize data transfer costs
- Leverage free tier of Google Gemini Flash API for AI features
- Use AWS Lambda for cost-effective serverless AI processing (pay per request)
- Amazon SQS provides reliable message queuing with minimal cost
- Amazon Bedrock Titan V2 Embeddings offers competitive pricing for vector generation
- DynamoDB on-demand pricing scales with usage
- Utilize free dictionary APIs and open-source translation libraries
- Free development tools and design software (VS Code, Figma Free, etc.)
- Leverage AWS Free Tier during initial development
- RDS automated backups included (7-day retention)
- Use AWS Certificate Manager for free SSL certificates
- Implement S3 lifecycle policies to move old data to cheaper storage tiers
- No email service costs by deferring email features to later phase
- Standby ECS tasks in AZ-2 kept minimal until failover needed

### 7. Risk Assessment

#### Risk Matrix

**High Priority Risks:**

1. **AI API Cost Overruns**
   - Impact: Low | Probability: Low
   - Description: Google Gemini Flash API free tier has usage limits that may be exceeded
   - Mitigation: Implement usage quotas and rate limiting; monitor API usage through dashboard
   - Contingency: Upgrade to paid tier if free limits are consistently exceeded, or implement queuing system

2. **Data Privacy & Security Breaches**
   - Impact: Critical | Probability: Low
   - Description: User data exposure or unauthorized access
   - Mitigation: Implement encryption, regular security audits, GDPR compliance
   - Contingency: Incident response plan, insurance, legal consultation

3. **Scalability Issues**
   - Impact: High | Probability: Medium
   - Description: System performance degradation with user growth
   - Mitigation: AWS ECS Auto Scaling in active AZ, RDS Multi-AZ active-passive for high availability, ElastiCache for performance
   - Contingency: Scale ECS tasks in active AZ, activate additional tasks in passive AZ if needed, upgrade RDS instance class

**Medium Priority Risks:**

4. **Third-party Service Downtime**
   - Impact: Medium | Probability: Low
   - Description: Dependency on external APIs (Gemini Flash free tier, free dictionary APIs)
   - Mitigation: Graceful degradation, inform users when AI features are unavailable
   - Contingency: Cached responses for dictionary lookups, manual grading option for tests during outages

5. **User Acquisition Challenges**
   - Impact: High | Probability: Medium
   - Description: Difficulty attracting and retaining users
   - Mitigation: Marketing strategy, SEO optimization, referral programs
   - Contingency: Pivot features based on feedback, partnerships with schools

6. **Content Moderation Issues**
   - Impact: Medium | Probability: High
   - Description: Inappropriate content in blogs, comments, or study rooms
   - Mitigation: Automated content filtering, reporting system, moderator team
   - Contingency: Community guidelines, user bans, legal disclaimer

**Low Priority Risks:**

7. **Technology Stack Obsolescence**
   - Impact: Low | Probability: Medium
   - Description: Chosen technologies become outdated
   - Mitigation: Regular dependency updates, modular architecture
   - Contingency: Gradual migration plan, refactoring budget

8. **Competition from Established Platforms**
   - Impact: Medium | Probability: High
   - Description: Competing with Duolingo, IELTS.org, etc.
   - Mitigation: Unique features (study rooms, AI assessment), niche targeting
   - Contingency: Differentiation strategy, feature innovation

#### Mitigation Strategies

**Technical Mitigations:**
- Implement comprehensive error handling and logging with CloudWatch
- Set up CloudWatch alarms for resource utilization and errors
- Regular automated backups with RDS Multi-AZ and point-in-time recovery
- Use CloudFront CDN for static assets to reduce ECS load
- Implement API rate limiting to prevent abuse
- Code reviews and automated testing in CI/CD pipeline (AWS CodePipeline/GitHub Actions)
- AWS WAF for application security and DDoS protection

**Business Mitigations:**
- Start with freemium model to build user base
- Beta testing phase to identify critical issues
- Gradual feature rollout to manage costs
- Build community through social media and content marketing
- Establish partnerships with IELTS teachers and institutions

**Legal & Compliance Mitigations:**
- Terms of Service and Privacy Policy
- GDPR and data protection compliance
- Content licensing agreements
- User consent for data processing
- Regular compliance audits

#### Contingency Plans

**Technical Failures:**
- Database failure: Automatic failover to RDS Multi-AZ standby instance in AZ-2 (passive)
- ECS task failure in AZ-1: Auto Scaling replaces unhealthy tasks; critical failure triggers AZ-2 activation
- API downtime: Serve cached content from ElastiCache and queue requests
- Security breach: AWS Security Hub immediate alerts, lockdown, and investigation
- Active-passive Multi-AZ ensures high availability with automatic failover to passive AZ-2

**Business Failures:**
- Low user adoption: Pivot to B2B model (schools, tutors)
- High churn rate: User interviews, feature improvements
- Revenue shortfall: Cost optimization, seek investment

**Legal Issues:**
- Copyright claims: Content takedown procedure
- Privacy complaints: Data deletion and compliance review
- Terms violations: User suspension and investigation

### 8. Expected Outcomes

#### Technical Improvements
**Platform Capabilities:**
- Fully functional web application with 5 integrated modules
- Real-time collaboration features (video/voice calls, messaging)
- Serverless AI service architecture with asynchronous processing
- AI-powered Writing assessment via Lambda Function 1 (grammar, vocabulary, task achievement, coherence)
- AI-powered Speaking assessment via Lambda Function 2 (pronunciation, fluency, coherence, lexical resource)
- RAG-based flashcard generation via Lambda Function 3 with smart query generation and document understanding
- Scalable Multi-AZ architecture on AWS ECS supporting 10,000+ concurrent users
- Mobile-responsive design for learning on-the-go
- Robust RESTful API built with Spring Boot monolith
- High availability with 99.9% uptime through active-passive Multi-AZ deployment
- Vector embeddings and semantic search for intelligent content retrieval

**Technical Achievements:**
- Modern full-stack web development with Next.js and Spring Boot
- Real-time communication implementation (WebRTC, Spring WebSocket)
- Serverless AI service architecture with API Gateway, SQS, and Lambda
- Three specialized Lambda functions for Writing/Speaking assessment and RAG-based flashcard generation
- RAG (Retrieval-Augmented Generation) implementation with Amazon Titan V2 Embeddings
- Vector store integration for semantic search and document retrieval
- Google Gemini Flash AI/ML integration for assessments and smart query generation
- Amazon Bedrock integration for alternative AI models and embeddings
- AWS cloud infrastructure and active-passive Multi-AZ architecture implementation
- PostgreSQL database design and optimization
- DynamoDB for AI results and flashcard storage
- Container orchestration with Amazon ECS Fargate
- Security best practices with Spring Security and AWS services
- DevOps practices with CloudWatch monitoring and automated deployments

#### Educational Impact
**For Students:**
- Accessible, affordable IELTS preparation platform
- Personalized learning paths and progress tracking
- Immediate feedback on practice tests
- Community-driven learning environment
- 24/7 access to study materials and practice tests
- Estimated 30-40% cost reduction compared to traditional courses

**Learning Outcomes:**
- Improved IELTS scores through consistent practice
- Better time management with Pomodoro integration
- Enhanced vocabulary through flashcard system
- Intelligent flashcard generation from documents using RAG technology
- Speaking confidence through AI feedback with detailed pronunciation and fluency analysis
- Writing skills improvement with detailed grammar, vocabulary, and task achievement analysis
- Context-aware learning through RAG-based flashcard generation that understands document content

#### Business Value
**Market Position:**
- Competitive alternative to expensive IELTS prep courses
- Unique combination of features (study rooms + AI + community)
- Scalable SaaS business model
- Potential for B2C and B2B markets (schools, tutoring centers)

**User Growth Targets:**
- Week 12 (Launch): 100 registered users (beta testers)
- Month 6: 500 registered users
- Month 12: 2,000 registered users
- Year 2: 10,000 registered users
- Premium conversion rate: 10-15%

**Revenue Potential:**
- Year 1: $17,500 (after launch in Month 6)
- Year 2: $150,000+ (with 500 premium, 1,000 regular members)
- Year 3: $500,000+ (with market expansion and partnerships)

#### Long-term Value
**Platform Evolution:**
- Foundation for other language learning modules (TOEFL, SAT, etc.)
- Data collection for improved AI models
- Community-generated content library
- Potential for gamification and achievement systems
- Mobile app development based on web platform success

**Social Impact:**
- Democratizing IELTS preparation for students worldwide
- Reducing language learning barriers
- Building a supportive learning community
- Enabling peer-to-peer knowledge sharing
- Creating opportunities for educational content creators

**Portfolio & Career Benefits:**
- Comprehensive full-stack project with Spring Boot and Next.js for developer portfolios
- Real-world experience with AWS cloud services (ECS, RDS, S3, CloudFront, etc.)
- Active-passive Multi-AZ architecture and high-availability system design experience
- AI integration experience with Google Gemini Flash
- Understanding of educational technology (EdTech)
- Container orchestration and failover strategies
- Potential startup opportunity or acquisition target

**Success Metrics:**
- User engagement: Average 3+ sessions per week
- Test completion rate: 70%+ of started tests
- User retention: 60%+ monthly active users
- NPS Score: 50+ (indicating strong user satisfaction)
- Average IELTS score improvement: 0.5-1.0 band increase