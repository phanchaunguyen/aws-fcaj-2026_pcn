---
title: "Week 6 Worklog"
date: 2026-07-13
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

### Tasks to be carried out this week:

| Day | Task | Start Date | Completion Date | Reference Material |
| --- | ---- | ---------- | --------------- | ------------------ |

### Week 6 Achievements:



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