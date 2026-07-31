---
title: "Week 5 Worklog"
date: 2026-07-06
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 

[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Week 5 Objectives:

*   Deep dive into Amazon RDS (PostgreSQL) architecture, focusing on High Availability and Connection Pooling.
*   Master NoSQL data modeling using DynamoDB's single-table design.
*   Implement caching mechanisms with Amazon ElastiCache (Redis) to offload database queries.
*   Explore Amazon Cognito comprehensively for user authentication, authorization, and seamless integrations.

---

### Databases in Practice: Relational vs. NoSQL vs. Caching

Selecting the right database and optimizing its architecture is crucial for a scalable backend. This week focused on three distinct data storage and retrieval strategies.

**1. Relational Database: Amazon RDS (PostgreSQL)**
*   **Architecture & Disaster Recovery:** Amazon RDS automates time-consuming administration tasks. To ensure Disaster Recovery (DR) and High Availability (HA), **Multi-AZ deployments** are utilized. This architecture synchronously replicates data to a standby instance in a different Availability Zone. In the event of an infrastructure failure, RDS automatically fails over to the standby instance without manual intervention.
*   **Connection Pooling with RDS Proxy:** Modern applications (especially Serverless architectures) can easily exhaust a relational database's maximum connections. **Amazon RDS Proxy** sits between the application (e.g., EC2, Lambda) and the RDS instance. It establishes and manages the necessary connection pools, allowing applications to scale unpredictably without overwhelming the underlying PostgreSQL database.

> *[NOTE FOR IMAGE INSERTION: Insert an architecture diagram showing an application connecting to RDS Proxy, which then pools connections to a Multi-AZ RDS PostgreSQL cluster.]*

**2. NoSQL Database: Amazon DynamoDB**
*   **Core Concepts:** DynamoDB is a fully managed, serverless NoSQL database designed for applications requiring consistent, single-digit millisecond latency at any scale.
*   **Single-Table Design:** Unlike relational databases where data is normalized across multiple tables, DynamoDB thrives on **Single-Table Design**. All related entities (e.g., Users, Orders, Products) are stored in one single table.
*   **Implementation Strategy:** This requires identifying all application "access patterns" beforehand. Complex queries are achieved by carefully designing Partition Keys (PK) and Sort Keys (SK), and utilizing Global Secondary Indexes (GSIs) to fetch pre-joined data in a single query.

**3. Caching: Amazon ElastiCache (Redis)**
*   **Purpose:** Databases are often the bottleneck in read-heavy applications. **Amazon ElastiCache** provides a fully managed Redis environment to act as an in-memory data store.
*   **Query Offloading:** Complex, frequently requested PostgreSQL queries (e.g., leaderboard generation, product catalogs) are cached in Redis. The backend first checks the cache; if the data exists (Cache Hit), it is returned instantly, bypassing the RDS instance entirely and significantly reducing database load.

> *[NOTE FOR IMAGE INSERTION: Insert a flowchart demonstrating the Cache-Aside pattern: Application -> Checks ElastiCache -> If Miss, queries RDS -> Updates ElastiCache -> Returns Data.]*

---

### Amazon Cognito: Identity and Access Management for Applications

Amazon Cognito provides frictionless and highly secure customer identity and access management (CIAM) for web and mobile applications.

**1. Core Components and Purpose**
*   **User Pools:** Serve as user directories. They handle authentication (verifying *who* the user is) and provide functionalities like sign-up, sign-in, MFA (Multi-Factor Authentication), and account recovery.
*   **Identity Pools:** Handle authorization (determining *what* the user can access). They exchange User Pool tokens (or external tokens) for temporary AWS credentials, allowing users to directly access AWS resources (like S3 or DynamoDB).

**2. Extending with External Identity Providers (IdP)**
*   Cognito supports identity federation. Users can sign in through social identity providers (Google, Facebook, Apple) or enterprise providers via SAML 2.0 and OpenID Connect (OIDC).
*   Cognito acts as the central broker, unifying these various providers and returning a standard set of JSON Web Tokens (JWT) to the application, regardless of the original login method.

**3. Hosted UI vs. Custom UI (Default)**
*   **Hosted UI:** An out-of-the-box, customizable web interface provided by AWS for user sign-up and sign-in. It handles the entire OAuth 2.0 flow automatically, making it the fastest way to implement authentication without writing frontend logic.
*   **Custom UI:** Building the login screens from scratch using AWS SDKs or Amplify. This offers complete design flexibility but requires handling state management, password resets, and MFA flows manually within the frontend code.

**4. Integration Strategies: Amplify vs. EC2**
*   **With AWS Amplify:** Amplify abstracts away the complexity. By using the `Authenticator` component in React, frontend integration is achieved in minutes. Amplify automatically manages token storage, automatic refreshing, and attaching the Authorization header to outgoing API requests.
*   **With EC2 (Backend Verification):** When the frontend sends a request to a backend hosted on EC2 (e.g., a Spring Boot or Python API), the backend must verify the user. 
    *   The frontend passes the Cognito JWT (Access or ID token) in the `Authorization` header.
    *   The EC2 backend uses libraries (like `aws-jwt-verify`) to validate the token's signature against Cognito's public keys (JWKS), verify the expiration time, and extract user claims before processing the business logic.

> *[NOTE FOR IMAGE INSERTION: Insert an authentication flow diagram: User logs in via Cognito Hosted UI -> Receives JWT -> Sends JWT to API Gateway/EC2 -> EC2 validates JWT and returns protected data.]*

---

### Tasks Completed This Week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Configure Multi-AZ RDS & Proxy | 2026-07-06 | 2026-07-06 | AWS RDS Best Practices |
| 2 | Model DynamoDB Single-Table Design | 2026-07-07 | 2026-07-07 | NoSQL Design Patterns |
| 3 | Integrate ElastiCache (Redis) | 2026-07-08 | 2026-07-08 | Caching Strategies |
| 4 | Setup Cognito User & Identity Pools | 2026-07-09 | 2026-07-09 | Cognito Auth Flows |
| 5 | Verify JWT Tokens on Backend (EC2) | 2026-07-10 | 2026-07-10 | JWT Validation Docs |

### Week 5 Achievements:

*   Architected a highly resilient PostgreSQL database utilizing Multi-AZ for failover and RDS Proxy for efficient connection pooling.
*   Transitioned from relational normalization to NoSQL single-table design methodologies for high-performance data retrieval.
*   Successfully implemented a Cache-Aside pattern using Redis to drastically reduce primary database load.
*   Configured Amazon Cognito with Google/Facebook federation using the Hosted UI.
*   Established a secure backend workflow on EC2 to intercept, validate, and process incoming Cognito JSON Web Tokens (JWT).


---

### Glossary

| Abbreviation | Meaning |
| --- | --- |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| BE | Backend — the server-side part of the application |
| CI/CD | Continuous Integration / Continuous Delivery — automated building, testing, and deployment |
| CORS | Cross-Origin Resource Sharing — browser mechanism allowing a page to call APIs on another origin |
| DB | Database |
| ERD | Entity-Relationship Diagram — visual model of database tables and their relationships |
| FE | Frontend — the client-side part of the application |
| FK | Foreign Key — a column referencing another table's primary key |
| IaC | Infrastructure as Code — declaring infrastructure in versioned files |
| IDOR | Insecure Direct Object Reference — accessing another user's resources by manipulating object IDs |
| JWT | JSON Web Token — signed token carrying identity claims |
| SNS | Amazon Simple Notification Service — pub/sub messaging and notifications |
