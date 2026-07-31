---
title: "Week 2 Worklog"
date: 2026-06-15
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

### Week 2 Objectives:

*   Deep dive into AWS Identity and Access Management (IAM) and cross-account access configurations.
*   Understand the fundamental components of AWS Networking.
*   Explore Virtual Private Cloud (VPC) architecture.
*   Learn how to configure IP addressing, routing, public/private subnets, and internet access.


### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 


[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)

![AWS roadmap](/images/1-Worklog/1.2-Week2/1.png)

You can make one of your own to track your progress..

---

### AWS Networking & VPC

A Virtual Private Cloud (VPC) acts as a logically isolated virtual network deployed within a specific AWS account. It functions similarly to a traditional on-premises data center but leverages the highly scalable infrastructure of the cloud.

**1. VPC and Regions**
*   A VPC is bound to a single AWS Region but spans multiple Availability Zones (AZs) within that region. 
*   This multi-AZ design is essential for ensuring high availability and fault tolerance for applications.

**2. CIDR Blocks and IP Addressing**
*   When a VPC is created, a range of IPv4 addresses must be allocated using a **CIDR block** (Classless Inter-Domain Routing), such as `10.0.0.0/16`.
*   This notation dictates the total number of available IP addresses for resources within that network. 
*   Careful planning is required to prevent overlapping CIDR blocks, especially when connecting multiple VPCs later via VPC Peering or Transit Gateways.


---

### Structuring the Network: Subnets

Subnets are smaller subdivisions of the VPC's IP address range. Unlike the VPC itself, a subnet is strictly tied to a single Availability Zone. Subnets are used to group resources based on security and routing requirements.

*   **Public Subnets:** Designed for internet-facing resources (e.g., web servers, application load balancers). Resources deployed here are assigned public IP addresses and have a direct route to the internet.
*   **Private Subnets:** Used for hosting backend systems and databases (e.g., Amazon RDS instances, internal application servers). These subnets have no direct route to the internet, providing an essential layer of security against unauthorized external access.

![Subnets and VPC example](/images/1-Worklog/1.2-Week2/2.png)

---

### Gateways and Traffic Routing

For resources to communicate with the outside world or with each other, specific gateways and routing configurations must be established.

*   **Internet Gateway (IGW):** A horizontally scaled, redundant component attached to the VPC. It enables bi-directional communication between resources in public subnets and the public internet.
*   **NAT Gateway (Network Address Translation):** Placed inside a public subnet, a NAT Gateway allows instances in a private subnet to initiate outbound traffic to the internet (e.g., for software updates or API calls) while blocking inbound traffic from the internet.
*   **Route Tables:** These act as the "virtual routers" of the VPC. A route table contains a set of rules (routes) determining where network traffic from subnets or gateways is directed. 
    *   A public route table includes a route pointing `0.0.0.0/0` (all internet traffic) to the Internet Gateway.
    *   A private route table directs outbound internet traffic (`0.0.0.0/0`) to the NAT Gateway instead.

### Practice setting up VPC, Subnet, Route tables, and IGW

![Mô tả cho ảnh](/images/1-Worklog/1.2-Week2/4.png)
---


### Network Security: NACLs vs. Security Groups

AWS provides two primary layers of firewall protection to secure the VPC infrastructure. Understanding the difference between them is crucial for network security.

*   **Security Groups (SGs):** 
    *   Operate at the instance level (attached directly to the Elastic Network Interface).
    *   Act as **stateful** firewalls. This means if an inbound request is allowed, the return traffic is automatically permitted, regardless of outbound rules.
    *   Support only "Allow" rules. All other traffic is implicitly denied.
*   **Network Access Control Lists (NACLs):** 
    *   Operate at the subnet level.
    *   Act as **stateless** firewalls. Both inbound and outbound traffic must be explicitly allowed.
    *   Support both "Allow" and "Explicit Deny" rules.
    *   Rules are processed in numerical order; the evaluation stops as soon as a matching rule is found.

Due to the scope of the internship program, i decided not to propose NACLs and Security Groups to the project as it inflates the project alot.

---

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |
| 1-2 | Deep Dive into IAM Concepts | 2026-06-15 | 2026-06-16 | AWS IAM Documentation |
| 3 | Learn VPC & Subnet Architecture | 2026-06-17 | 2026-06-17 | AWS Networking Tutorials |
| 4 | Configure IGW, NAT, and Route Tables | 2026-06-18 | 2026-06-18 | AWS VPC Masterclass |
| 5 | Implement SGs and NACLs | 2026-06-19 | 2026-06-19 | AWS Security Guidelines |

### Week 2 Achievements:

*   Grasped the core concepts of IP addressing and CIDR block calculations.
*   Successfully mapped out a custom VPC architecture spanning multiple Availability Zones.
*   Differentiated the roles of Public and Private subnets for securing multi-tier applications.
*   Understood how to route traffic securely using Internet Gateways, NAT Gateways, and Route Tables.
*   Mastered the differences between stateful Security Groups and stateless Network ACLs.
### Team Meeting — 06/20/2026

**Attendees:** Hieu, Danh, Thanh (Absent: Nguyen, Hung)

**Architecture Decision**

Hieu presented the hybrid AWS architecture proposal for the court booking application. The team aligned on the following breakdown of the three core features:

| Feature                                                     | Implementation                    |
| ----------------------------------------------------------- | --------------------------------- |
| User Authentication (Sign Up / Sign In)                     | Monolith (EC2)                    |
| Booking Management (Create / Edit / Cancel / View / Search) | Monolith (EC2)                    |
| Payment Processing                                          | Serverless (Lambda + API Gateway) |

**Task Distribution**

| Track                         | Owner     | Deliverable                                                                                                                                 |
| ----------------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| All members                   | Everyone  | Review and provide feedback on the architecture proposal                                                                                    |
| Frontend                      | FE Team   | Market research on similar apps; AI-assisted UX/UI design → design system (color palette, typography, icon set) and screen list per feature |
| Backend — Booking Management  | Thanh     | API documentation (endpoint name, input, output, use case) and DB design (tables, columns, data types)                                      |
| Backend — User Authentication | Nguyen    | API documentation and DB design for the authentication feature                                                                              |
| Backend — Payment             | Hieu      | API documentation and DB design for the payment feature                                                                                     |
| AWS Administration            | AWS Admin | Set up AWS Organization, accounts, IAM users, roles, and policies                                                                           |

**Next Steps**

- FE team to complete market research and produce an initial design system by end of Week 2
- BE members to complete API docs and DB schema drafts by end of Week 2
- AWS Admin to have the base account structure ready before infrastructure work begins in Week 3

---

### Glossary

| Abbreviation | Meaning |
| --- | --- |
| AI | Artificial Intelligence |
| API | Application Programming Interface — the contract through which software components communicate |
| AWS | Amazon Web Services — Amazon's cloud computing platform |
| BE | Backend — the server-side part of the application |
| CLF-C02 | Exam code of the AWS Certified Cloud Practitioner certification |
| DB | Database |
| DVA-C02 | Exam code of the AWS Certified Developer – Associate certification |
| EC2 | Amazon Elastic Compute Cloud — virtual servers on AWS |
| ERD | Entity-Relationship Diagram — visual model of database tables and their relationships |
| FK | Foreign Key — a column referencing another table's primary key |
| IAM | AWS Identity and Access Management — users, roles, and permissions |
| JSONB | JSON Binary — PostgreSQL's binary JSON column type |
| JWT | JSON Web Token — signed token carrying identity claims |
| PK | Primary Key — the column uniquely identifying each row |
| RDS | Amazon Relational Database Service — managed SQL databases |
| S3 | Amazon Simple Storage Service — object storage |
| SNS | Amazon Simple Notification Service — pub/sub messaging and notifications |
| SQS | Amazon Simple Queue Service — managed message queues |
| UI | User Interface |
| UK | Unique Key — column(s) whose values must be unique |
| URL | Uniform Resource Locator — web address |
| UUID | Universally Unique Identifier |
| VND | Vietnamese Dong (ISO currency code) |
| VPC | Amazon Virtual Private Cloud — an isolated virtual network on AWS |
