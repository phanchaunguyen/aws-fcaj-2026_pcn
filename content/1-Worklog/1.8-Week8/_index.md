---
title: "Week 8 Worklog"
date: 2026-07-27
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### roadmap.sh recommended AWS roadmap
As the AWS eco-system is too vast to cover all, i decided to followed this roadmap from roadmap.sh as my study path. From now on, i will update my progress here. 

[Follow my road map journey here](https://roadmap.sh/u/6a50761e8b578e964b053e38?roadmapId=aws)

### Week 8 Objectives:
*   Collaborating with the team to rush the progress of the FCAJ project
*   Propose a stronger architecture diagram, configurating region, vpc, subnets, IGW, etc ...
*   Polishing the FCAJ report website, add video, pictures and diagrams.

### 1. Team Synchronization & Architecture Consensus

This week marks a major transition as we consolidate our individual AWS deployment experiences into the execution of the group project: the **Court Booking System**. 

On **July 28th**, the team held a critical synchronization meeting to finalize the system architecture and address early integration bugs. Key outcomes from the meeting included:

*   **Architecture Alignment:** We agreed on a strict separation of concerns. The Frontend (SPA) will not interact directly with AWS managed services. Instead, the Backend will act as a centralized API Gateway, wrapping all AWS service calls (including Auth and Database operations).
*   **Bug Fixing & Environment Parity:** We resolved several bugs related to local development environments not matching the cloud infrastructure. We established a strict `APP_ENV` variable convention (`local` vs. `dev`) to ensure seamless transitions between mocked local services and real AWS resources.

---

### 2. Authentication (Cognito) Setup

Amazon Cognito is the project's **auth authority**: it stores users, verifies passwords, and issues JWTs. A key design decision agreed upon during our July 28th meeting shapes everything here:

{{% notice note %}}
**The frontend never talks to Cognito.** There is no Amplify Auth SDK in the SPA. The backend *wraps* Cognito (`app/cognito.py`): the FE calls `POST /api/v1/auth/login` on our API; the BE calls Cognito, returns the tokens; the FE just stores them and sends `Authorization: Bearer <token>` on every request. Result: zero Cognito configuration in the frontend, and the auth flow can be swapped or mocked server-side.
{{% /notice %}}

#### 2.1. User Pool, App Client, and Groups

Three resources were created via the AWS CLI to establish the authentication foundation:

```bash
# 1. The pool — email is the username, self-verified
POOL_ID=$(aws cognito-idp create-user-pool --pool-name court-booking   --username-attributes email --auto-verified-attributes email   --query 'UserPool.Id' --output text --profile thanh)

# 2. A PUBLIC app client for the SPA — no secret (a browser cannot keep one)
CLIENT_ID=$(aws cognito-idp create-user-pool-client --user-pool-id $POOL_ID   --client-name court-booking-spa --no-generate-secret   --explicit-auth-flows ALLOW_USER_PASSWORD_AUTH ALLOW_REFRESH_TOKEN_AUTH ALLOW_USER_SRP_AUTH   --query 'UserPoolClient.ClientId' --output text --profile thanh)

# 3. Groups = application roles
for g in player court_manager admin; do
  aws cognito-idp create-group --user-pool-id $POOL_ID --group-name "$g" --profile thanh
done
```

The three groups mirror the role model exactly: a user's group appears in their JWT as the `cognito:groups` claim; the backend caches it into `users.role`, which `require_role("admin")` / `require_manager` dependencies check on every protected endpoint. Changing a user's role in production means changing their Cognito group first, then the DB cache — Cognito stays the source of truth.

---

### 3. Wiring Cognito to the Backend

To ensure smooth deployments and avoid hardcoding sensitive IDs, the pool and client IDs go into AWS Systems Manager (SSM) Parameter Store and reach the application via the environment bridge:

```bash
aws ssm put-parameter --name /court-booking/dev/COGNITO_USER_POOL_ID --type String --value "$POOL_ID"   --overwrite --profile thanh
aws ssm put-parameter --name /court-booking/dev/COGNITO_CLIENT_ID    --type String --value "$CLIENT_ID" --overwrite --profile thanh
```

**Environment-Based Execution:**
The switch is automatic. While `APP_ENV=local`, the `auth.py` script uses a local-mode fallback (a Bearer token is matched directly against seeded `cognito_sub` values — this is what lets the walking-skeleton deploy work before Cognito exists). Once `APP_ENV=dev` and the two IDs are present in SSM, `cognito.py` calls the real pool. This uses the exact same endpoints and frontend code, but issues real JWTs.

{{% notice tip %}}
**Future Scope:** Google OAuth (`POST /auth/oauth`) additionally requires a Cognito hosted-UI domain + identity provider. Email/password auth needs none of that, so social login is treated as a later, independent step deferred for upcoming sprints.
{{% /notice %}}


### Team Meeting — 07/28/2026

**Attendees:** Hieu, Thanh, Nguyen, Danh, Hung
**Absent:** None

**Presentations & Discussions**

- **Hieu & Thanh** presented the finalized **Architecture Diagram**, ensuring team consensus on the network flow from the end-user through the global CDN down to the private database subnets.
- **Danh** led a session on **VPC & CIDR Block Re-partitioning** to avoid IP exhaustion and route conflicts, establishing a strict boundary between public and private resources.
- **Team** conducted a comprehensive audit of **Security Group (SG)** configurations to guarantee secure inter-service communication and fix early integration timeouts.

**Review summary of Architecture & Network adjustments**

1. **Architecture Consensus**: Reaffirmed the strict separation of concerns. The Frontend (SPA) will not interact directly with internal AWS managed services. The Backend (ECS Fargate) acts as the centralized API Gateway, wrapped behind an Application Load Balancer (ALB).
2. **VPC CIDR Strategy**: Agreed on a base VPC CIDR of `10.0.0.0/16` to ensure ample IP availability.
   - **Public Subnets** (`10.0.1.0/24`, `10.0.2.0/24`) are strictly reserved for NAT Gateways and the internet-facing ALB.
   - **Private Subnets** (`10.0.10.0/24`, `10.0.11.0/24`) are dedicated to the Backend ECS tasks and the RDS instance to ensure absolute isolation from direct internet access.
3. **Security Group (SG) Chaining**: Resolved inter-app communication blocks by enforcing strict SG referencing rather than hardcoding IP ranges:
   - **ALB-SG**: Allows Inbound HTTP/HTTPS (Port `80`/`443`) from `0.0.0.0/0`.
   - **ECS-Backend-SG**: Allows Inbound TCP Port `8080` *exclusively* from `ALB-SG`.
   - **RDS-DB-SG**: Allows Inbound PostgreSQL Port `5432` *exclusively* from `ECS-Backend-SG`.
4. **Environment Parity**: Addressed bugs where local setups failed to mirror cloud networking. We established a strict `APP_ENV` variable convention (`local` vs. `dev`) so the backend can seamlessly switch connection strings between local mocked services and real AWS VPC endpoints.