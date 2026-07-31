---
title: "Week 3 Worklog"
date: 2026-06-22
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 

[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)


### Week 3 Objectives:
*   Deep dive into AWS Compute Services.
*   Understand and compare different EC2 instance types and pricing models (On-Demand, Spot, Reserved, Savings Plans).
*   Compare self-managed EC2 with containerized deployments (Docker with ECS/EKS).
*   Master the concepts of High Availability, Auto Healing, and Auto Scaling Groups (ASG).
*   Explore deployment strategies (Blue/Green, Rolling Update) to achieve zero-downtime during backend updates.

---

### Understanding AWS Compute Services

This week focused heavily on computing architectures in AWS, transitioning from traditional vertical scaling to highly resilient horizontal scaling models.

**1. The Evolution of Compute: EC2 vs. Serverless vs. Containers**
*   **Amazon EC2 (Elastic Compute Cloud):** Provides secure, resizable compute capacity. It allows the provisioning of virtual servers where full control over the OS and dependencies is maintained. However, it requires active management (patching, scaling).
*   **Container Management (ECS/EKS):** Instead of manually deploying Spring Boot or Python applications directly onto an EC2 instance, applications are packaged into Docker containers. 
    *   **Amazon ECS (Elastic Container Service)** and **EKS (Elastic Kubernetes Service)** orchestrate these containers.
    *   When combined with **AWS Fargate** (a serverless container engine), the need to provision or manage underlying EC2 instances is entirely eliminated. This significantly reduces operational overhead compared to self-managed EC2 environments.
*   **Serverless (AWS Lambda):** The ultimate abstraction of compute. Code is executed strictly in response to events (e.g., an API call or an S3 upload) without any concept of underlying infrastructure. It scales automatically from zero to thousands of concurrent executions.

**2. Choosing the Right EC2 Instance Type**
AWS offers specific instance families optimized for different workloads:
*   **General Purpose (e.g., T, M families):** Balanced CPU, memory, and networking for typical backend APIs.
*   **Compute Optimized (C family):** High-performance processors for batch processing or media transcoding.
*   **Memory Optimized (R, X families):** Ideal for in-memory databases (like Redis) or real-time big data analytics.
*   **Storage Optimized (I, D families):** High sequential read/write access to large datasets on local storage.

> *[NOTE FOR IMAGE INSERTION: Insert an infographic or comparison table showing the different EC2 instance families (General Purpose, Compute, Memory, Storage) and their typical use cases.]*

---

### EC2 Pricing Models: Optimizing Cloud Costs

Understanding how to pay for compute resources is as critical as understanding the technical specifications. AWS provides several purchasing options:

*   **On-Demand:** Pay-by-the-hour/second with no upfront commitment. Best for unpredictable or short-term workloads. It is the most expensive, yet most flexible option.
*   **Reserved Instances (RI):** A commitment of 1 to 3 years to a specific instance type in exchange for a significant discount (up to 72%). Best for steady, known workloads.
*   **Savings Plans:** The modern alternative to RIs. It offers similar discounts for a 1 or 3-year commitment but provides flexibility. A *Compute Savings Plan* applies discounts across EC2, Fargate, and Lambda, regardless of instance family or region.
*   **Spot Instances:** Bidding on unused AWS capacity. It offers the highest discount (up to 90% off On-Demand) but comes with a catch: AWS can terminate the instance with a 2-minute warning. 
    *   *Best practice:* Only use Spot instances for stateless, fault-tolerant, or batch-processing workloads that can handle interruptions.
*   **EC2 Fleet:** A capability that allows configuring an Auto Scaling Group to provision a mix of On-Demand and Spot instances across different instance types. This ensures the application remains highly available while significantly reducing compute costs.

> *[NOTE FOR IMAGE INSERTION: Insert a side-by-side comparison chart illustrating the differences between On-Demand, Spot, Reserved Instances, and Savings Plans, highlighting the flexibility vs. cost trade-offs.]*

---

### High Availability, Auto Healing, and Auto Scaling

In cloud computing, a single instance failure should never result in application downtime.

*   **Horizontal Scaling vs. Vertical Scaling:** 
    *   *Vertical scaling* involves adding more CPU/RAM to a single instance. It has a hard limit, becomes disproportionately expensive, and represents a single point of failure.
    *   *Horizontal scaling* involves adding more instances (cloning the application) and distributing traffic via an Elastic Load Balancer (ELB). This ensures **High Availability** and fault tolerance.
*   **Auto Scaling Groups (ASG):** The mechanism that handles horizontal scaling automatically.
    *   *Scale Out:* Adds instances when metrics (like CPU utilization > 70%) breach a threshold.
    *   *Scale In:* Removes instances during low traffic to save costs.
*   **Auto Healing:** ASG continuously monitors instance health checks (often coupled with ELB health checks). If an instance crashes or becomes unhealthy, the ASG automatically terminates the faulty instance and spins up a healthy replacement without human intervention.


### Homework: Create a ASG Group



---

### Zero-Downtime Deployment Strategies

When deploying a new version of a backend application (e.g., updating a Spring Boot API from v1 to v2), downtime must be avoided. Two primary strategies are utilized:

**1. Rolling Updates**
*   The ASG incrementally replaces old instances with new ones.
*   For example, if 4 instances are running, it updates 1 instance at a time. The ELB stops sending traffic to the instance being updated. Once the new instance passes health checks, the ELB routes traffic to it, and the process repeats for the next instance.
*   *Advantage:* Cost-effective (does not require duplicating infrastructure).
*   *Disadvantage:* Deployment takes longer, and rolling back in case of failure is slow.

**2. Blue/Green Deployments**
*   Two identical environments are maintained: "Blue" (the current live version) and "Green" (the new version).
*   The new version is deployed entirely to the Green environment. Once it is fully tested and healthy, traffic is instantly switched at the Load Balancer/DNS level from Blue to Green.
*   *Advantage:* Instant rollback capability (just switch traffic back to Blue) and true zero-downtime.
*   *Disadvantage:* Temporarily doubles the infrastructure cost during the deployment window.

> *[NOTE FOR IMAGE INSERTION: Insert a diagram comparing Rolling Update (instance-by-instance replacement) with Blue/Green Deployment (traffic shifting between two distinct environments).]*

---

### Tasks to be Carried Out This Week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1 | Study EC2 Instance Types & Pricing | 2026-06-22 | 2026-06-22 | AWS EC2 Documentation |
| 2 | Compare EC2 vs. Docker (ECS/EKS) | 2026-06-23 | 2026-06-23 | Container Orchestration Videos |
| 3 | Implement High Availability & ELB | 2026-06-24 | 2026-06-24 | AWS Load Balancing Guide |
| 4 | Configure Auto Scaling Groups | 2026-06-25 | 2026-06-25 | ASG Best Practices |
| 5 | Research Deployment Strategies | 2026-06-26 | 2026-06-26 | Blue/Green vs Rolling Update Docs |

### Week 3 Achievements:

*   Mastered the evaluation of EC2 instance types and identified the most cost-effective pricing models (Spot vs. Savings Plans) for backend workloads.
*   Understood the operational advantages of using Docker containers orchestrated by ECS/Fargate compared to managing raw EC2 instances.
*   Designed a highly available architecture utilizing Load Balancers and Auto Scaling Groups across multiple Availability Zones to ensure Auto Healing.
*   Gained theoretical proficiency in executing zero-downtime deployments using Blue/Green and Rolling Update strategies.

---

### Team Meeting — 06/27/2026

**Attendees:** Hieu, Thanh, Nguyen, Hung
**Absent:** Danh

**Presentations**

- **Hieu** demonstrated the end-to-end payment logic and walked the team through the proposed database design for the application.
- **Danh** (submitted asynchronously prior to the meeting) shared an initial UI draft for review.

**Task Distribution**

| Track                         | Owner       | Deliverable                                                                                                  |
| ----------------------------- | ----------- | ------------------------------------------------------------------------------------------------------------ |
| Frontend — UI Design          | Danh & Hung | Unify and improve the UI design based on Danh's initial draft                                                |
| Frontend — Tech Stack         | Danh & Hung | Propose tech stack for frontend implementation                                                               |
| Backend — Tech Stack          | All BE      | Propose tech stack for backend implementation                                                                |
| Backend — Architecture        | Hieu        | Integrate Amazon Amplify for frontend hosting into the architecture; redraw the updated architecture diagram |
| Backend — Booking Management  | Thanh       | API documentation (endpoint name, input, output, use case) and DB design (tables, columns, data types)       |
| Backend — User Authentication | Nguyen      | API documentation (endpoint name, input, output, use case) and DB design (tables, columns, data types)       |

---

### Hieu's Deliverable — Payment Feature

#### API Documentation

<!--
| # | Endpoint | Method | Input | Output | Use Case |
|---|----------|--------|-------|--------|----------|
| 1 | `/api/v1/payments` | `POST` | `booking_id` (UUID), `amount` (decimal), `payment_method` (string) | `payment_id` (UUID), `checkout_url` (string), `status: "PENDING"` | Player initiates payment after confirming a court booking. Creates a PENDING payment record in RDS and returns a checkout URL to redirect the player. |
| 2 | `/api/v1/payments/webhook` | `POST` | `payment_id` (UUID), `transaction_id` (string), `status` (string), `gateway_data` (object) | `{ success: boolean }` | Payment gateway notifies the system of the transaction result. This is the entry point of the serverless path: API Gateway receives the callback → invokes Lambda to update payment status in RDS → triggers SNS notification to the player. |
| 3 | `/api/v1/payments/{payment_id}` | `GET` | `payment_id` (path param), Bearer token (header) | `payment_id`, `booking_id`, `amount`, `currency`, `status`, `payment_method`, `transaction_id`, `created_at`, `updated_at` | Player or admin checks the status of a specific payment. |
| 4 | `/api/v1/payments` | `GET` | Bearer token (header), `page` (int), `limit` (int) | `{ data: [...payments], total, page, limit }` | Player views their full payment history with pagination. |
-->

<table style="width:100%; table-layout:fixed; word-break:break-word;">
  <colgroup>
    <col style="width:3%">
    <col style="width:15%">
    <col style="width:5%">
    <col style="width:20%">
    <col style="width:15%">
    <col style="width:42%">
  </colgroup>
  <thead>
    <tr>
      <th>#</th>
      <th>Endpoint</th>
      <th>Method</th>
      <th>Input</th>
      <th>Output</th>
      <th>Use Case</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td><code>/api/v1/payments</code></td>
      <td><code>POST</code></td>
      <td><code>booking_id</code> (UUID), <code>amount</code> (decimal), <code>payment_method</code> (string)</td>
      <td><code>payment_id</code> (UUID), <code>checkout_url</code> (string), <code>status: "PENDING"</code></td>
      <td>Player initiates payment after confirming a court booking. Creates a PENDING payment record in RDS and returns a checkout URL to redirect the player.</td>
    </tr>
    <tr>
      <td>2</td>
      <td><code>/api/v1/payments/webhook</code></td>
      <td><code>POST</code></td>
      <td><code>payment_id</code> (UUID), <code>transaction_id</code> (string), <code>status</code> (string), <code>gateway_data</code> (object)</td>
      <td><code>{ success: boolean }</code></td>
      <td>Payment gateway notifies the system of the transaction result. This is the entry point of the serverless path: API Gateway receives the callback → invokes Lambda to update payment status in RDS → triggers SNS notification to the player.</td>
    </tr>
    <tr>
      <td>3</td>
      <td><code>/api/v1/payments/{payment_id}</code></td>
      <td><code>GET</code></td>
      <td><code>payment_id</code> (path param), Bearer token (header)</td>
      <td><code>payment_id</code>, <code>booking_id</code>, <code>amount</code>, <code>currency</code>, <code>status</code>, <code>payment_method</code>, <code>transaction_id</code>, <code>created_at</code>, <code>updated_at</code></td>
      <td>Player or admin checks the status of a specific payment.</td>
    </tr>
    <tr>
      <td>4</td>
      <td><code>/api/v1/payments</code></td>
      <td><code>GET</code></td>
      <td>Bearer token (header), <code>page</code> (int), <code>limit</code> (int)</td>
      <td><code>{ data: [...payments], total, page, limit }</code></td>
      <td>Player views their full payment history with pagination.</td>
    </tr>
  </tbody>
</table>

#### Database Design

**Table: `payments`**

| Column             | Data Type        | Constraints                     | Description                                                |
| ------------------ | ---------------- | ------------------------------- | ---------------------------------------------------------- |
| `id`               | `UUID`           | `PRIMARY KEY`                   | Unique payment identifier                                  |
| `booking_id`       | `UUID`           | `NOT NULL`, `FK → bookings(id)` | The booking this payment is for                            |
| `user_id`          | `UUID`           | `NOT NULL`, `FK → users(id)`    | Player who initiated the payment                           |
| `amount`           | `DECIMAL(12, 2)` | `NOT NULL`                      | Payment amount                                             |
| `currency`         | `VARCHAR(3)`     | `NOT NULL`, `DEFAULT 'VND'`     | Currency code                                              |
| `status`           | `VARCHAR(20)`    | `NOT NULL`, `DEFAULT 'PENDING'` | `PENDING` / `SUCCESS` / `FAILED` / `REFUNDED`              |
| `payment_method`   | `VARCHAR(50)`    | `NOT NULL`                      | `CREDIT_CARD` / `BANK_TRANSFER` / `E_WALLET`               |
| `transaction_id`   | `VARCHAR(255)`   | `UNIQUE`                        | Transaction ID returned by the payment gateway             |
| `gateway_response` | `JSONB`          |                                 | Raw response payload from the payment gateway for auditing |
| `created_at`       | `TIMESTAMP`      | `NOT NULL`, `DEFAULT NOW()`     | Payment initiation time                                    |
| `updated_at`       | `TIMESTAMP`      | `NOT NULL`, `DEFAULT NOW()`     | Time of last status update                                 |

**Notes:**

- `transaction_id` is nullable until the gateway responds (stays `NULL` while status is `PENDING`).
- `gateway_response` stores the full raw payload from the gateway — useful for dispute resolution and debugging without needing to re-query the gateway.
- The `status` column is updated by the confirmation Lambda (webhook path), not by the EC2 monolith, keeping the two paths clearly separated.

---

### Full Database Design

#### Tables

**`users`**

| Column          | Data Type      | Constraints        | Description                                                   |
| --------------- | -------------- | ------------------ | ------------------------------------------------------------- |
| `id`            | `UUID`         | `PRIMARY KEY`      | Unique user identifier                                        |
| `email`         | `VARCHAR(255)` | `UNIQUE, NOT NULL` | Login email                                                   |
| `password_hash` | `VARCHAR(255)` | `NOT NULL`         | Bcrypt-hashed password                                        |
| `full_name`     | `VARCHAR(255)` | `NOT NULL`         | Display name                                                  |
| `phone`         | `VARCHAR(20)`  |                    | Contact number                                                |
| `cognito_sub`   | `VARCHAR(255)` | `UNIQUE`           | Cognito User Pool subject ID — links JWT to local user record |
| `role`          | `VARCHAR(20)`  | `DEFAULT 'player'` | `player` / `admin` / `court_owner`                            |
| `avatar_url`    | `VARCHAR(500)` |                    | S3 URL of profile photo                                       |
| `created_at`    | `TIMESTAMP`    | `DEFAULT NOW()`    | Account creation time                                         |
| `updated_at`    | `TIMESTAMP`    | `DEFAULT NOW()`    | Last profile update                                           |

**`courts`**

| Column           | Data Type       | Constraints        | Description                                        |
| ---------------- | --------------- | ------------------ | -------------------------------------------------- |
| `id`             | `UUID`          | `PRIMARY KEY`      | Unique court identifier                            |
| `owner_id`       | `UUID`          | `FK → users(id)`   | Court owner (user with role `court_owner`)         |
| `name`           | `VARCHAR(255)`  | `NOT NULL`         | Court display name                                 |
| `description`    | `TEXT`          |                    | Court details                                      |
| `address`        | `VARCHAR(500)`  | `NOT NULL`         | Physical location                                  |
| `sport_type`     | `VARCHAR(50)`   | `NOT NULL`         | `badminton` / `tennis` / `football` / `basketball` |
| `price_per_hour` | `DECIMAL(12,2)` | `NOT NULL`         | Hourly rate in VND                                 |
| `status`         | `VARCHAR(20)`   | `DEFAULT 'ACTIVE'` | `ACTIVE` / `INACTIVE` / `MAINTENANCE`              |
| `created_at`     | `TIMESTAMP`     | `DEFAULT NOW()`    |                                                    |
| `updated_at`     | `TIMESTAMP`     | `DEFAULT NOW()`    |                                                    |

**`court_images`**

| Column       | Data Type      | Constraints                 | Description                     |
| ------------ | -------------- | --------------------------- | ------------------------------- |
| `id`         | `UUID`         | `PRIMARY KEY`               |                                 |
| `court_id`   | `UUID`         | `NOT NULL, FK → courts(id)` | Parent court                    |
| `image_url`  | `VARCHAR(500)` | `NOT NULL`                  | S3 URL of the photo             |
| `is_primary` | `BOOLEAN`      | `DEFAULT false`             | Whether this is the cover photo |
| `created_at` | `TIMESTAMP`    | `DEFAULT NOW()`             |                                 |

**`bookings`**

| Column         | Data Type       | Constraints                 | Description                                         |
| -------------- | --------------- | --------------------------- | --------------------------------------------------- |
| `id`           | `UUID`          | `PRIMARY KEY`               | Unique booking identifier                           |
| `user_id`      | `UUID`          | `NOT NULL, FK → users(id)`  | Player who made the booking                         |
| `court_id`     | `UUID`          | `NOT NULL, FK → courts(id)` | Court being reserved                                |
| `start_time`   | `TIMESTAMP`     | `NOT NULL`                  | Slot start                                          |
| `end_time`     | `TIMESTAMP`     | `NOT NULL`                  | Slot end                                            |
| `total_amount` | `DECIMAL(12,2)` | `NOT NULL`                  | Price at time of booking                            |
| `status`       | `VARCHAR(20)`   | `DEFAULT 'PENDING'`         | `PENDING` / `CONFIRMED` / `CANCELLED` / `COMPLETED` |
| `note`         | `TEXT`          |                             | Optional player note                                |
| `created_at`   | `TIMESTAMP`     | `DEFAULT NOW()`             |                                                     |
| `updated_at`   | `TIMESTAMP`     | `DEFAULT NOW()`             |                                                     |

> Double-booking prevention: add a PostgreSQL exclusion constraint using `btree_gist` on `(court_id, tsrange(start_time, end_time))` to reject overlapping time ranges at the DB level.

**`payments`** — see Hieu's Deliverable above.

#### ERD

{{<mermaid>}}
erDiagram
users {
UUID id PK
VARCHAR email UK
VARCHAR password_hash
VARCHAR full_name
VARCHAR phone
VARCHAR cognito_sub UK
VARCHAR role
VARCHAR avatar_url
TIMESTAMP created_at
TIMESTAMP updated_at
}
courts {
UUID id PK
UUID owner_id FK
VARCHAR name
TEXT description
VARCHAR address
VARCHAR sport_type
DECIMAL price_per_hour
VARCHAR status
TIMESTAMP created_at
TIMESTAMP updated_at
}
court_images {
UUID id PK
UUID court_id FK
VARCHAR image_url
BOOLEAN is_primary
TIMESTAMP created_at
}
bookings {
UUID id PK
UUID user_id FK
UUID court_id FK
TIMESTAMP start_time
TIMESTAMP end_time
DECIMAL total_amount
VARCHAR status
TEXT note
TIMESTAMP created_at
TIMESTAMP updated_at
}
payments {
UUID id PK
UUID booking_id FK
UUID user_id FK
DECIMAL amount
VARCHAR currency
VARCHAR status
VARCHAR payment_method
VARCHAR transaction_id UK
JSONB gateway_response
TIMESTAMP created_at
TIMESTAMP updated_at
}

    users ||--o{ courts : "owns"
    users ||--o{ bookings : "makes"
    users ||--o{ payments : "pays"
    courts ||--o{ court_images : "has"
    courts ||--o{ bookings : "reserved via"
    bookings ||--o| payments : "paid by"

{{</mermaid>}}

---

### Payment Flow

The payment feature spans both deployment paths. The table below maps each step to the actor, DB operation, and AWS service involved.

| Step | Actor                    | Action                                                  | DB Operation                                                                           | AWS Service        |
| ---- | ------------------------ | ------------------------------------------------------- | -------------------------------------------------------------------------------------- | ------------------ |
| 1    | Player                   | Searches and selects a court + time slot                | `SELECT` from `courts`, check no overlap in `bookings`                                 | EC2 (FastAPI)      |
| 2    | Player                   | Confirms booking                                        | `INSERT` into `bookings` with `status = 'PENDING'`                                     | EC2 (FastAPI)      |
| 3    | Player                   | Initiates payment                                       | `INSERT` into `payments` with `status = 'PENDING'`, returns `checkout_url`             | EC2 (FastAPI)      |
| 4    | Player                   | Redirected to payment gateway and completes transaction | —                                                                                      | External gateway   |
| 5    | Gateway                  | Sends webhook callback                                  | —                                                                                      | Amazon API Gateway |
| 6    | Lambda (Process Payment) | Validates webhook, updates payment record               | `UPDATE payments SET status = 'SUCCESS', transaction_id = ..., gateway_response = ...` | AWS Lambda         |
| 7    | Lambda (Confirm Booking) | Confirms the booking                                    | `UPDATE bookings SET status = 'CONFIRMED'`                                             | AWS Lambda         |
| 8    | SNS                      | Notifies player                                         | —                                                                                      | Amazon SNS         |
| 9    | Player                   | Views confirmation                                      | `SELECT` from `bookings` JOIN `payments`                                               | EC2 (FastAPI)      |

**On payment failure (Step 6 returns `FAILED`):**

- `payments.status` → `FAILED`
- `bookings.status` → `CANCELLED`
- SNS notifies the player of the failure

**State lifecycle:**

```
bookings.status:   PENDING ──► CONFIRMED ──► COMPLETED
                       └──────► CANCELLED

payments.status:   PENDING ──► SUCCESS
                       └──────► FAILED ──► (retry creates new payment record)
```

**Key design rule:** Steps 1–3 are handled by the EC2 monolith (writes to `bookings` and `payments`). Steps 5–8 are handled entirely by the serverless path (Lambda reads and writes the same RDS tables). The two paths never write to the same record at the same time — the monolith creates the records, Lambda updates them — so there is no write conflict.


### Glossary

| Abbreviation | Meaning |
| --- | --- |
| AI | Artificial Intelligence |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| BaaS | Backend as a Service |
| BE | Backend — the server-side part of the application |
| CaaS | Containers as a Service |
| DB | Database |
| EC2 | Amazon Elastic Compute Cloud — virtual servers on AWS |
| EFS | Amazon Elastic File System — shared, elastic file storage |
| ELB | Elastic Load Balancer — distributes incoming traffic across instances |
| FaaS | Function as a Service |
| IaaS | Infrastructure as a Service |
| IAM | AWS Identity and Access Management — users, roles, and permissions |
| PaaS | Platform as a Service |
| PR | Pull Request — a proposed code change reviewed before merging |
| RDS | Amazon Relational Database Service — managed SQL databases |
| REST | Representational State Transfer — architectural style for HTTP APIs |
| S3 | Amazon Simple Storage Service — object storage |
| SaaS | Software as a Service |
| UI | User Interface |
| VPC | Amazon Virtual Private Cloud — an isolated virtual network on AWS |
