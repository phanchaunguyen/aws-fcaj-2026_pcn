---
title: "Week 4 Worklog"
date: 2026-06-29
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 

[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)




### Week 4 Objectives:

*   Understand the differences between AWS storage families: Block, Object, and File storage.
*   Deep dive into Amazon S3, exploring storage classes, lifecycle policies, and secure uploads via Presigned URLs.
*   Compare Amazon EBS (Elastic Block Store) and Amazon EFS (Elastic File System).
*   Analyze Cloud Economics by optimizing frontend delivery using S3 and CloudFront.
*   Master AWS Amplify Gen 2 for rapid full-stack application prototyping in future projects.

---

### AWS Storage Ecosystem: Object, Block, and File

AWS categorizes storage into three main families to address different performance, persistence, and architectural needs.

**1. Object Storage: Amazon S3**
*   **Concept:** S3 (Simple Storage Service) is an object storage service designed to store and protect any amount of data, from media files to massive data lakes.
*   **Security & Presigned URLs:** By default, S3 buckets block all public access. Secure uploads and downloads can be managed using **Presigned URLs** generated via the AWS SDK. This mechanism allows client applications (like a web browser) to upload files directly to S3 within a limited timeframe. It prevents exposing AWS credentials and stops heavy file payloads from bottlenecking the backend servers.
*   **Storage Classes:** S3 offers various tiers to optimize costs based on access frequency:
    *   *S3 Standard:* High availability and low latency for frequently accessed data (hot data).
    *   *S3 Standard-Infrequent Access (IA):* Lower storage cost for older data that still requires rapid access when needed.
    *   *S3 Glacier / Deep Archive:* Lowest-cost storage designed for long-term archiving and compliance.
*   **Lifecycle Rules:** Cost optimization is fully automated by configuring lifecycle rules. These rules transition objects between storage classes automatically (e.g., moving data from Standard to IA after 30 days, and to Glacier after 90 days).
*   **Event-Driven Architecture:** S3 can emit events upon object creation or deletion. These events can seamlessly trigger AWS Lambda functions to perform tasks like image resizing or metadata extraction.

**2. Block Storage vs. File Storage**
*   **Amazon EBS (Elastic Block Store):** A block storage service designed to be attached to a single EC2 instance at a time. It acts like a physical hard drive (SSD/HDD), highly performant for transactional workloads and relational databases. Data on EBS persists independently of the instance's life.
*   **Amazon EFS (Elastic File System):** A serverless, fully elastic file storage service utilizing the NFS protocol (ideal for Linux environments). Unlike EBS, EFS can be mounted concurrently across hundreds of EC2 instances, making it perfect for shared data workloads and distributed applications.

> *[NOTE FOR IMAGE INSERTION: Insert a comparison table or diagram illustrating the architectural differences between S3, EBS, and EFS, highlighting concurrent connections and typical use cases.]*

---

### Cloud Economics & Performance Optimization

Optimizing cloud costs requires architectural separation and smart content delivery strategies.

*   **Decoupling Frontend and Backend:** Hosting static assets (like a compiled React or Vue.js build) on EC2 instances incurs unnecessary and expensive compute costs. Instead, these static assets are uploaded to an S3 bucket configured specifically for static website hosting.
*   **Integrating Amazon CloudFront:** The S3 bucket is combined with CloudFront, a global Content Delivery Network (CDN). CloudFront caches the React application at edge locations worldwide.
*   **Cost & Performance Benefits:** 
    *   This architecture completely offloads static traffic from the backend servers.
    *   Compute instances (EC2/ECS) only process dynamic API requests. This allows the backend infrastructure to be scaled down, significantly reducing monthly EC2 costs.
    *   End-users experience near-instant load times regardless of their geographic location.

> *[NOTE FOR IMAGE INSERTION: Insert an architecture diagram showing users accessing a React app via CloudFront/S3, while dynamic API requests are routed to an Application Load Balancer and the Backend Compute layer.]*

---

### AWS Amplify Gen 2: Accelerating Full-Stack Development

AWS Amplify simplifies full-stack development by provisioning backend services automatically. Gen 2 introduces a code-first approach, establishing it as a critical tool for future project scaffolding.

**1. Core Features of Amplify Gen 2**
*   **TypeScript-Based Infrastructure as Code (IaC):** Unlike Gen 1's CLI/Studio approach, Gen 2 defines all backend resources (Authentication, Data, Storage) using TypeScript (e.g., in a `resource.ts` file). Developers can use familiar programming languages rather than learning complex CloudFormation or Terraform syntaxes.
*   **Automated Authentication:** Integrating Amazon Cognito is drastically simplified. A few lines of code generate a fully functional login/signup UI component (the `Authenticator` wrapper in React) alongside secure backend identity pools.
*   **Managed Data Models:** Defining a data model automatically provisions an Amazon DynamoDB table and an AWS AppSync GraphQL API. The frontend immediately gains out-of-the-box CRUD (Create, Read, Update, Delete) operations with full typing intelligence (IntelliSense).

**2. CI/CD and Sandboxed Environments**
*   **Per-Developer Sandboxes:** Amplify Gen 2 introduces cloud sandboxes (`npx amx sandbox`). Every developer gets an isolated cloud backend during local development. This totally prevents database collisions and code overwrites among team members.
*   **Continuous Deployment:** Amplify connects directly to GitHub repositories. Pushing code to a specific branch automatically triggers a pipeline that builds the React frontend and updates the corresponding cloud backend infrastructure simultaneously.

**3. Application in Future Projects**
*   Amplify will be heavily utilized to rapidly prototype web applications.
*   It significantly reduces the boilerplate setup time for configuring authentication, APIs, and database provisioning.
*   The isolated sandbox environments will ensure safe, conflict-free, and collaborative development within team projects.

> *[NOTE FOR IMAGE INSERTION: Insert a screenshot of the AWS Amplify Console showing a successful CI/CD deployment pipeline, or a snippet of the TypeScript data model definition (`resource.ts`).]*

---

### Tasks Completed This Week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Study Storage Services (S3, EBS, EFS) | 2026-06-29 | 2026-06-29 | AWS Storage Comparison |
| 2 | Configure Presigned URLs via SDK | 2026-06-30 | 2026-06-30 | S3 Security Guidelines |
| 3 | Optimize Costs with S3 & CloudFront | 2026-07-01 | 2026-07-01 | Cloud Economics Docs |
| 4 | Explore AWS Amplify Gen 2 Features | 2026-07-02 | 2026-07-02 | Amplify React Tutorial |
| 5 | Build a Full-Stack Prototype App | 2026-07-03 | 2026-07-03 | Local Sandbox Testing |

### Week 4 Achievements:

*   Distinguished the precise use cases for Object, Block, and File storage on AWS.
*   Conceptualized a secure file upload architecture utilizing S3 Presigned URLs to bypass backend network bottlenecks.
*   Designed a cost-optimized architecture by migrating static frontend assets to a Serverless CloudFront + S3 model.
*   Mastered the TypeScript-first approach in AWS Amplify Gen 2 for rapidly provisioning Cognito authentication and DynamoDB databases.
*   Validated the utility of Amplify Sandboxes for isolated, conflict-free team development workflows.


---

### Team Meeting — 07/05/2026

**Attendees:** Hieu, Nguyen, Danh, Hung
**Absent:** Thanh

**Presentations**

- **Thanh** (shared asynchronously) presented the API design for the **Booking** feature: <https://d2kk6nff0gmlot.cloudfront.net/1-worklog/1.3-week3/>
- **Nguyen** shared the API design for the **User Authentication** feature: [GitHub PR #1](https://github.com/minyryo/aws-fcaj-2026/pull/1/changes/b495a65faa1e3bc638aac259dc7008c1d0fccf5a)
- **Hieu** showed the new version of the architecture diagram, implementing **AWS Amplify** to handle the UI.

**Tech Stack Vote**

All members voted for the main tech stack:

| Layer    | Choice                        |
| -------- | ----------------------------- |
| Frontend | TypeScript (React)            |
| Backend  | FastAPI (Python) + PostgreSQL |

**Task Distribution**

| Track                          | Owner          | Deliverable                                                            |
| ------------------------------ | -------------- | ---------------------------------------------------------------------- |
| Frontend — UI Design           | Danh & Hung    | Unify & improve the UI design to match the API documentation           |
| Backend — Tech Stack           | All BE         | Propose tech stack for BE implementation                               |
| Backend — API & DB Unification | Hieu           | Unify the APIs & database design                                       |
| Backend — Repo & Deployment    | Hieu           | Init code repo and provide deployment guidance for the team            |
| Backend — API Review & Research | Thanh & Nguyen | Review the existing API doc & research on coding APIs using FastAPI (Python) |


---

### Glossary

| Abbreviation | Meaning |
| --- | --- |
| AI | Artificial Intelligence |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| EC2 | Amazon Elastic Compute Cloud — virtual servers on AWS |
| EFS | Amazon Elastic File System — shared, elastic file storage |
| ELB | Elastic Load Balancer — distributes incoming traffic across instances |
| IAM | AWS Identity and Access Management — users, roles, and permissions |
| IDE | Integrated Development Environment |
| RDS | Amazon Relational Database Service — managed SQL databases |
| REST | Representational State Transfer — architectural style for HTTP APIs |
| S3 | Amazon Simple Storage Service — object storage |
| VPC | Amazon Virtual Private Cloud — an isolated virtual network on AWS |
