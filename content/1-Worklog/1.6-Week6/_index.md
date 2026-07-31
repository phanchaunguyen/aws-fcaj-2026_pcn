---
title: "Week 6 Worklog"
date: 2026-07-13
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 

[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Week 6 Objectives:

*   Master the concepts of Serverless Architecture and event-driven computing.
*   Analyze the Cold Start problem in AWS Lambda and compare execution environments (Java vs. Python).
*   Understand Event-Driven Architecture (EDA) for decoupling systems using Amazon SQS and Amazon SNS.
*   Explore Container Orchestration services: Amazon ECS and Amazon EKS.
*   Determine the best practices for running the Spring Boot framework in an AWS environment.

---

### Serverless Foundation & AWS Lambda

Serverless architecture shifts the operational responsibilities of provisioning and managing servers to AWS, allowing focus entirely on application code.

**1. AWS Lambda & Background Tasks**
*   AWS Lambda executes code in response to triggers (e.g., HTTP requests via API Gateway, file uploads to S3, or schedule-based rules).
*   It is highly effective for asynchronous background tasks, scaling automatically to handle thousands of concurrent executions.

**2. The Cold Start Challenge: Java vs. Python**
*   A "Cold Start" occurs when Lambda provisions a new execution environment to handle an incoming request, adding latency.
*   **Python:** Being a lightweight interpreted language, Python functions start up very quickly, minimizing cold start latency.
*   **Java:** Java applications suffer from significantly longer cold starts due to the heavy initialization of the Java Virtual Machine (JVM) and framework loading times.
*   **Mitigation Strategies:** Utilizing **AWS Lambda SnapStart** (which caches a snapshot of the initialized memory and JVM state) or configuring **Provisioned Concurrency** keeps instances "warm" and ready to respond instantly.


![EC2 Instance types comparison](/images/1-Worklog/1.6-Week6/2.png)

![EC2 Instance types comparison](/images/1-Worklog/1.6-Week6/3.png)

---

### Event-Driven Architecture (EDA)

Monolithic architectures often suffer from tight coupling, where the failure of one component brings down the entire system. Event-Driven Architecture solves this by decoupling services.

**1. Amazon SNS (Simple Notification Service)**
*   A managed Pub/Sub (Publish/Subscribe) messaging service.
*   A single published message can be instantly "fanned out" to multiple subscribing services (e.g., triggering multiple Lambda functions or sending email alerts simultaneously).

**2. Amazon SQS (Simple Queue Service)**
*   A managed message queuing service used to decouple producers and consumers.
*   Messages are held securely in a queue until the consuming service is ready to process them. This prevents backend systems from being overwhelmed during unexpected traffic spikes (buffering).
*   Combining SNS and SQS is a standard cloud pattern: SNS fans out the message, and SQS queues it for reliable, asynchronous processing.


---

### Container Orchestration: ECS and EKS

While Lambda is perfect for short-lived tasks, continuously running applications (like robust backend APIs) are often better suited for containers.

**1. Amazon ECS (Elastic Container Service)**
*   An AWS-native, highly scalable container orchestration service.
*   It is deeply integrated with the AWS ecosystem (ALB, IAM, CloudWatch) and provides a simpler learning curve for deploying Docker containers.
*   When paired with **AWS Fargate**, it operates as a serverless container engine, eliminating the need to manage underlying EC2 infrastructure.

**2. Amazon EKS (Elastic Kubernetes Service)**
*   A managed Kubernetes service for running open-source Kubernetes workloads.
*   It provides immense flexibility and avoids cloud vendor lock-in, making it the industry standard for complex, enterprise-grade microservices architectures.

![EC2 Instance types comparison](/images/1-Worklog/1.6-Week6/5.png)

![EC2 Instance types comparison](/images/1-Worklog/1.6-Week6/4.png)

![EC2 Instance types comparison](/images/1-Worklog/1.6-Week6/6.png)

---

### Reasearch on running Spring Boot on AWS

Spring Boot is a powerful framework, but deploying it efficiently on AWS requires choosing the right compute model based on the application's needs.

*   **Amazon EC2 / Elastic Beanstalk:** The traditional approach. The Spring Boot `.jar` file is deployed onto provisioned virtual machines. It is easy to understand but requires managing scaling and server updates.
*   **Amazon ECS with Fargate:** The highly recommended modern approach. The Spring Boot application is packaged into a Docker container. ECS manages the deployment, and Fargate handles the compute resources automatically, providing excellent horizontal scaling.
*   **AWS Lambda (Serverless Spring):** Running a full Spring Boot application on Lambda traditionally faced severe cold start issues. However, using **Spring Cloud Function** combined with **GraalVM Native Images** compiles the application into a lightweight, standalone executable, achieving lightning-fast startup times suitable for serverless environments.

> *[NOTE FOR IMAGE INSERTION: Insert an architecture diagram showing a Spring Boot Docker container deployed on Amazon ECS (Fargate) behind an Application Load Balancer.]*



### Team Meeting — 07/19/2026

**Attendees:** Hieu, Thanh, Nguyen, Danh, Hung
**Absent:** None

**Presentations**

- **Hieu** demoed the backend scaffold: repo structure, 7-table schema migrations applied to local Postgres, and the deployment guide walkthrough (two-repo CI/CD, two-account strategy).
- **Nguyen** proposed 6 additional endpoints extending the unified API design (working doc `ADJ_APIs.md`): **Admin Operations** (court approval queue, approve/reject, user role management), **Manager Analytics** (revenue), and **User Profile** (view/update — deferred as low priority).

**Review summary of Nguyen's API additions**

1. **The admin approval endpoints fill a real gap**: §6.5 introduced the `PENDING` → `ACTIVE`/`REJECTED` court lifecycle but defined no admin API to drive it. `GET /admin/courts` (queue) + a review endpoint complete the flow, with an SNS notification to the court manager on decision.
2. **The revenue endpoint backs the manager dashboard** promised in §6.5. Agreed definition: aggregate from **`payments` with `status = 'SUCCESS'`** (refunds fall out automatically) joined to the caller's courts — not from booking totals — and scoped by `courts.owner_id` per the IDOR rule, with optional `group_by=day|court` for charts.
3. **Role changes must go through Cognito first**: `users.role` is only a cache — the endpoint must call Cognito `AdminAddUserToGroup`/`AdminRemoveUserFromGroup`, then update the DB row, or JWT claims and DB drift apart.
4. **Naming standardization**: `GET/PUT /users/me` (matching the existing `/bookings/me` pattern) instead of verb-style `/users/update-profile`.
5. **Schema addition needed**: `courts.rejection_reason` (nullable) so a rejected court's reason survives beyond the notification.

**Decisions & workload distribution**

All 6 endpoints adopted with the adjustments above (integrated into the Proposal as [§6.6](/2-proposal/2.1-architecture/)). Hieu prioritizes the CI/CD setup, so feature implementation is distributed by domain ownership:

| # | Action | Owner | Notes |
| - | ------ | ----- | ----- |
| 1 | **CI/CD setup**: `ci.yml` in both repos → branch protection → environments; then the walking-skeleton deploy to the dev account (guide Parts 0–1) | Hieu | Priority — unblocks the PR quality gate for everyone |
| 2 | `courts.rejection_reason` model + migration (first task — follow the Alembic guide), then the **Admin Operations router** (queue, review + SNS) and the **Cognito-first role endpoint** | Nguyen | His §6.6 proposal; Cognito fits his auth domain. `ADJ_APIs.md` superseded by §6.6 |
| 3 | **Booking routers** on the scaffolded schema; then the **revenue endpoint** (payments-based `SUM` per the §6.6 definition) | Thanh | Revenue is a read-only aggregate — natural follow-on once booking queries are familiar |
| 4 | FE–BE **connectivity proof** (health check through CORS); build the **admin review screen** and **revenue chart** against mocks; regenerate types when the contract updates (§4.3 flow) | Danh & Hung | Mock-first means no waiting on backend endpoints |
| 5 | User-profile endpoints deferred (low priority, revisit after core features) | — | |
| 6 | Cognito admin permissions (`AdminAddUserToGroup` etc.) noted for the EC2 instance role at deploy phase | Hieu | New IAM surface — tracked in the CI/CD guide hand-off checklist |

---