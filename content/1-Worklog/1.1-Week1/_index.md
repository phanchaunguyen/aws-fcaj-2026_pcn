---
title: "Week 1 Worklog"
date: 2026-06-08
weight: 1
chapter: false
pre: " <b> 1.1. </b> "
---

### Week 1 Objectives:

*   Connect and get acquainted with members of First Cloud AI Journey.
*   Attend the team kick-off meeting and align on the hybrid AWS architecture proposal.
*   Read an overview of AWS services referenced in the project architecture diagram.
*   Create an AWS account.

---

### Getting to Know the General Knowledge About AWS Cloud Services

The first week was dedicated to studying the overarching ecosystem of Amazon Web Services (AWS).

![AWS Introduction by official aws utube account](/images/1-Worklog/1.1-Week1/2.png)
AWS Introduction by official aws utube account


Below is a detailed breakdown of the core service categories explored:

**1. Compute Services**
*   **Amazon EC2 (Elastic Compute Cloud):** The fundamental building block for provisioning virtual servers (instances) in the cloud.
*   **Managed Services:** Services like **Elastic Beanstalk** (a PaaS for quick deployments) and **Amazon Lightsail** (ideal for simple web applications) abstract away underlying infrastructure management.
*   **Serverless Architecture:** **AWS Lambda** allows code execution in response to events without provisioning servers. This model charges only for exact compute time consumed (measured in milliseconds).
*   **Containers:** **ECS** and **EKS** (for Kubernetes) manage containerized applications, often combined with **AWS Fargate** as a serverless compute engine to eliminate EC2 instance management.

**2. Storage Services**
*   **Amazon S3 (Simple Storage Service):** A highly scalable, cost-effective object storage service used for static assets (images, HTML, CSS, videos).
*   **EBS (Elastic Block Store):** Provides high-performance block storage attached directly to a compute instance.
*   **EFS (Elastic File System):** Utilized for shared file storage across multiple instances simultaneously.

**3. Database Services**
*   **Relational Databases:** **Amazon RDS** automates administrative tasks for SQL databases. **Amazon Aurora** is a proprietary, highly performant relational database featuring a serverless variant that scales down to zero.
*   **NoSQL Databases:** **DynamoDB** is a fully managed key-value store offering single-digit millisecond latency. **DocumentDB** serves as a MongoDB-compatible document database.
*   **Caching:** **ElastiCache** and **MemoryDB** reduce database load by keeping frequently accessed data in memory.

**4. Networking & Content Delivery**
*   **Route 53:** Handles DNS management and traffic routing.
*   **CloudFront:** Acts as a Content Delivery Network (CDN), caching static assets from S3 to edge locations worldwide to minimize latency for global users.

![AWS most important services](/images/1-Worklog/1.1-Week1/1.png)

Here is a youtube video of most important aws services to learn, all in all, help taking a grasp of the scope of the system. 

[I put the link here so you can watch, highly recommend](https://www.youtube.com/watch?v=OGYEXGy8ca4)

---

### Create an AWS Console Account & Management Console Overview


*   The registration process requires linking a credit/debit card for identity verification and fraud prevention. 
*   No charges are incurred as long as usage remains strictly within the Free Tier limits. 
*   The "Free Basic Support" plan was selected to finalize the process.

Upon logging in, the **AWS Management Console** serves as the central control panel. Key operational concepts include:

*   **Regions:** Located in the top-right corner, representing distinct physical data center locations. Choosing the appropriate Region (e.g., `us-east-1` N. Virginia over `us-west-1` N. California) is critical for minimizing latency and optimizing costs.
*   **Resource Provisioning:** Through hands-on mini-labs, the process of launching an EC2 instance was completed (using Amazon Linux OS and configuring a Security Group to open port 8080).
*   **Storage & Database Creation:** An S3 Bucket was created (disabling `Block all public access` and attaching a Bucket Policy for public viewing), alongside provisioning a PostgreSQL database via Amazon RDS.

![Mô tả cho ảnh](/images/1-Worklog/1.1-Week1/3.png)

[Here is the link](https://www.youtube.com/watch?v=gUaNRNjLkcM)

Though a good introduction to the console, it is very outdated, i recommend anyone who reading this to search for a newer videos as AWS is infamous for changing the console now and then.

---

### Explore AWS Budget and Do the Sidequests for Extra $100 Credits

Utilizing cloud services requires strict financial vigilance. The Pay-As-You-Go model can quickly accumulate charges if mismanaged. 

*   Immediately after account creation, the **Billing and Cost Management** dashboard was reviewed.
*   **AWS Budgets** and **Billing Alarms** were configured via CloudWatch. Setting a budget threshold (e.g., $1) guarantees an immediate email alert if monthly usage inadvertently exceeds the Free Tier boundaries. 
*   Various AWS sidequests and programs (such as AWS Educate) were researched to secure AWS Credits (worth $100). These credits provide a financial safety net for experimenting with services outside the Free Tier scope.



---

### Basic Concepts of AWS IAM

 **AWS IAM (Identity and Access Management)** acts as the gatekeeper, centrally managing identities and access permissions.

Key core concepts studied:

*   **Resources:** Entities created within AWS (e.g., EC2 instances, S3 buckets, Lambda functions).
*   **Actions:** Specific operations performed on a Resource (e.g., `s3:CreateBucket`, `lambda:InvokeFunction`).
*   **Policies:** JSON documents defining permissions through an `Effect` (Allow or Deny), an `Action`, and a `Resource`. 


![Mô tả cho ảnh](/images/1-Worklog/1.1-Week1/4.png)

---

### Tasks to be Carried Out This Week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Attend Kick-off & Setup Account | 2026-06-08 | 2026-06-08 | Kick-off notes |
| 2-3 | Learn AWS Fundamentals | 2026-06-09 | 2026-06-10 | AWS YouTube videos |
| 4 | IAM Deep Dive & Best Practices | 2026-06-11 | 2026-06-11 | IAM Core Concepts |
| 5 | Setup Budgets & Billing Alarms | 2026-06-12 | 2026-06-12 | AWS Console |

### Week 1 Achievements:

*   Successfully created and secured an AWS Free Tier account.
*   Gained a comprehensive understanding of the vast AWS service landscape (Compute, Storage, Database, Networking).
*   Implemented IAM best practices by setting up standard IAM Users/Roles and securing the Root account.
*   Effectively configured AWS Budgets to prevent unexpected costs.

---

### Glossary

| Abbreviation | Meaning |
| --- | --- |
| AI | Artificial Intelligence |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| BE | Backend — the server-side part of the application |
| DB | Database |
| EC2 | Amazon Elastic Compute Cloud — virtual servers on AWS |
| ELB | Elastic Load Balancer — distributes incoming traffic across instances |
| FE | Frontend — the client-side part of the application |
| IAM | AWS Identity and Access Management — users, roles, and permissions |
| RDS | Amazon Relational Database Service — managed SQL databases |
| S3 | Amazon Simple Storage Service — object storage |
| SNS | Amazon Simple Notification Service — pub/sub messaging and notifications |
| UI | User Interface |
| UX | User Experience |