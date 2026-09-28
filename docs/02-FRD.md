# docs/02-FRD.md

**Document Type:** Functional Requirements Document (FRD)
**Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform
**Version:** 1.0.1
**Status:** DRAFT — PENDING REVIEW
**Date (created):** 2026-09-28
**Date (last updated):** 2026-09-28
**Author:** AI Engineering Agent
**Source Baselines:**
  - Project Definition v0.3.0 (`docs/00-PROJECT-DEFINITION.md`) — BASELINE APPROVED
  - PRD v1.1.0 (`docs/01-PRD.md`) — APPROVED

---

## Change History

| Version | Date | Author | Summary |
|---|---|---|---|
| 1.0.0 | 2026-09-28 | AI Engineering Agent | Initial FRD created from approved PRD v1.1.0 and Project Definition v0.3.0. Resolves PDQ-01 and PDQ-05. |
| 1.0.1 | 2026-09-28 | AI Engineering Agent | Targeted correction pass: clarified blockchain consistency and failure reporting (functional behavior without implying database/blockchain atomic commits); made Server-side AI analysis execution mandatory across all eligible triggers regardless of threshold score; clarified external escalation terminology as off-platform referrals; updated notification generation to Server workflow without atomic guarantees; reinforced explicit labeling of assumptions and deferred decisions; and clarified project completion conditions resolving PDQ-05. |

---

> **Terminology standard (mandatory throughout this document):**
> - The React/Vite web application is always called the **Client**.
> - The Python/FastAPI application is always called the **Server**.
> - Do NOT use "front-end client", "backend server", "frontend server", or "backend" when referring to the Server.
> - Repository directory names `frontend/` and `backend/` are NOT renamed by this convention.

> **Label key used in this document:**
> - **REQUIREMENT** — Explicitly required by approved source documents.
> - **DECISION** — Resolved in source documents; not to be reopened.
> - **ASSUMPTION** — Assumed where source documents do not fully specify behavior; clearly labeled.
> - **DEFERRED** — Belongs to a downstream document (TRD, System Architecture, UI/UX Design, AI/ML Design).

---

## Table of Contents

1. [Purpose and Scope](#1-purpose-and-scope)
2. [Relationship to Project Definition and PRD](#2-relationship-to-project-definition-and-prd)
3. [Functional System Overview](#3-functional-system-overview)
4. [Actors and Permission Model](#4-actors-and-permission-model)
5. [Authentication and Access Control](#5-authentication-and-access-control)
6. [Platform Admin Functional Requirements](#6-platform-admin-functional-requirements)
7. [Government Admin Functional Requirements](#7-government-admin-functional-requirements)
8. [Department Officer / Engineer Functional Requirements](#8-department-officer--engineer-functional-requirements)
9. [Contractor Functional Requirements](#9-contractor-functional-requirements)
10. [Auditor Functional Requirements](#10-auditor-functional-requirements)
11. [Citizen / Public Functional Requirements](#11-citizen--public-functional-requirements)
12. [Project Management](#12-project-management)
13. [Budget and Fund Management](#13-budget-and-fund-management)
14. [Milestone Management](#14-milestone-management)
15. [Fund Release Workflow](#15-fund-release-workflow)
16. [Physical Progress Management](#16-physical-progress-management)
17. [Evidence and Document Management](#17-evidence-and-document-management)
18. [Blockchain Audit Trail Functional Behavior](#18-blockchain-audit-trail-functional-behavior)
19. [AI Risk and Anomaly Detection Functional Behavior](#19-ai-risk-and-anomaly-detection-functional-behavior)
20. [Auditor Investigation Workflow](#20-auditor-investigation-workflow)
21. [Government Admin to Auditor Escalation Workflow](#21-government-admin-to-auditor-escalation-workflow)
22. [Notification Functional Requirements](#22-notification-functional-requirements)
23. [Citizen Transparency Portal](#23-citizen-transparency-portal)
24. [Dashboard and Reporting Functional Requirements](#24-dashboard-and-reporting-functional-requirements)
25. [State Models and State Transitions](#25-state-models-and-state-transitions)
26. [Business Rules and Validation Rules](#26-business-rules-and-validation-rules)
27. [Error and Exception Behavior](#27-error-and-exception-behavior)
28. [Cross-Module Functional Rules](#28-cross-module-functional-rules)
29. [Functional Requirements Traceability](#29-functional-requirements-traceability)
30. [FRD-Level Open Questions and Deferred Decisions](#30-frd-level-open-questions-and-deferred-decisions)

---

## 1. Purpose and Scope

### 1.1 Purpose

This Functional Requirements Document (FRD) translates the approved Product Requirements Document (PRD v1.1.0) into precise functional behavior specifications. Where the PRD defines *what* the product must do, this FRD defines:

- who can perform each action and under what conditions;
- what input data and preconditions are required;
- what validation rules apply;
- what state changes occur in the system;
- what happens when an action fails, is rejected, or encounters an exception;
- what notifications and audit records are produced as results;
- what business rules govern the behavior.

### 1.2 Scope

This FRD covers the functional behavior of all six actor roles across all platform modules as defined in PRD v1.1.0. It resolves the two FRD-level design questions delegated by the PRD (PDQ-01 and PDQ-05).

**This FRD does not specify:**
- Database schemas or SQL definitions (DEFERRED → TRD/System Architecture)
- API endpoint paths, HTTP methods, or request/response schemas (DEFERRED → TRD)
- Solidity function signatures, struct definitions, or gas optimization (DEFERRED → TRD/System Architecture)
- Blockchain signing architecture — which wallet signs which event (DEFERRED → TRD/System Architecture)
- Background task technology or scheduler configuration (DEFERRED → TRD)
- Infrastructure, deployment, or environment configuration (DEFERRED → System Architecture)
- ML model hyperparameters, feature engineering code, or training pipeline (DEFERRED → AI/ML Design)
- CSS implementation, component library configuration, or responsive breakpoints (DEFERRED → UI/UX Design)

### 1.3 Authoritative Sources

| Document | Version | Status |
|---|---|---|
| `docs/00-PROJECT-DEFINITION.md` | 0.3.0 | BASELINE APPROVED |
| `docs/01-PRD.md` | 1.1.0 | APPROVED |

---

## 2. Relationship to Project Definition and PRD

### 2.1 Upstream Traceability

All requirements in this FRD trace to one or more confirmed requirements (CR-01 through CR-29) from the Project Definition v0.3.0 and/or functional requirements (FR-001 through FR-144) from PRD v1.1.0. No requirement in this FRD introduces new product scope.

### 2.2 Resolved PDQ Items

The PRD delegated two questions to the FRD:

| PDQ | Question | Resolution Section |
|---|---|---|
| PDQ-01 | Milestone completion trigger | Section 14, FRD-MILE-004 |
| PDQ-05 | Project completion authorization and conditions | Section 12, FRD-PROJ-004 |

### 2.3 Items Deferred to Downstream Documents

The following PDQ items from PRD v1.1.0 remain outside FRD scope and are recorded in Section 30:

| PDQ | Question | Downstream Document |
|---|---|---|
| PDQ-02 | Server-side missed milestone check frequency | TRD |
| PDQ-03 | AI execution coordination (sync vs async) | TRD/System Architecture |
| PDQ-04 | On-chain event signing model | TRD/System Architecture |

---

## 3. Functional System Overview

### 3.1 Platform Description

The platform is a web-based prototype for tracking public infrastructure project fund lifecycles. It integrates four technical pillars:

| Pillar | Functional Role |
|---|---|
| **Client (React + Vite)** | Role-specific dashboards, forms, views, and public portal. |
| **Server (Python + FastAPI)** | Application API, authentication, authorization, workflow orchestration, deadline detection, AI/ML analysis, IPFS upload, blockchain interaction. |
| **PostgreSQL** | Stores all operational, relational, and queryable application data. |
| **Blockchain (Solidity + Hardhat)** | Tamper-evident anchor of important fund lifecycle events. Does not replace PostgreSQL. |
| **IPFS (Pinata)** | Off-chain storage for evidence documents. CIDs stored in PostgreSQL and anchored on-chain. |
| **AI/ML (hybrid rule-based + Isolation Forest)** | Detects anomalous or high-risk patterns for human auditor investigation. |

### 3.2 Key Functional Constraints

- **DECISION:** The platform tracks fund lifecycle events. It does NOT process or execute real government financial transactions.
- **DECISION:** AI identifies anomalous or high-risk patterns. AI does NOT prove fraud, corruption, or guilt. All final audit decisions are human decisions made by the Auditor.
- **DECISION:** Physical progress and financial utilization are separate, explicitly tracked data streams.
- **DECISION:** Rejected fund release requests are permanently retained and never deleted.
- **DECISION:** Missed milestone detection is a deterministic Server-side function. AI uses the result as an input signal but does not determine the state.
- **DECISION:** All notifications are in-app only. No email or SMS.
- **DECISION:** IPFS documents are unencrypted. This limitation is acknowledged and documented.

---

## 4. Actors and Permission Model

### 4.1 Actor Summary

| Actor ID | Role Name | Authentication | Access Category |
|---|---|---|---|
| ACT-01 | Platform Admin | JWT (email/password) | System administration only |
| ACT-02 | Government Admin | JWT (email/password) | Project and fund management |
| ACT-03 | Department Officer / Engineer | JWT (email/password) | Milestone and progress management |
| ACT-04 | Contractor | JWT (email/password) + Wallet (for on-chain writes) | Fund release and evidence submission |
| ACT-05 | Auditor | JWT (email/password) + Wallet (for on-chain writes) | Anomaly investigation and audit findings |
| ACT-06 | Citizen / Public | None (no authentication) | Public read-only |

### 4.2 Permission Matrix

| Action / Resource | ACT-01 | ACT-02 | ACT-03 | ACT-04 | ACT-05 | ACT-06 |
|---|---|---|---|---|---|---|
| Create/manage user accounts | ✓ | — | — | — | — | — |
| Assign/change user roles | ✓ | — | — | — | — | — |
| View system user list | ✓ | — | — | — | — | — |
| Deactivate user account | ✓ | — | — | — | — | — |
| Create infrastructure project | — | ✓ | — | — | — | — |
| Allocate project budget | — | ✓ | — | — | — | — |
| Assign Department Officer to project | — | ✓ | — | — | — | — |
| View project list | ✓ (read-only, aggregate) | ✓ (all) | ✓ (assigned) | ✓ (assigned) | ✓ (all) | ✓ (public data) |
| Define project milestones | — | — | ✓ (assigned) | — | — | — |
| Record physical progress | — | — | ✓ (assigned) | — | — | — |
| Submit fund release request | — | — | — | ✓ (assigned) | — | — |
| Upload evidence documents | — | — | — | ✓ | — | — |
| Review contractor submission | — | — | ✓ (assigned) | — | — | — |
| Submit verification report | — | — | ✓ (assigned) | — | — | — |
| Recommend approval/rejection | — | — | ✓ (assigned) | — | — | — |
| Approve fund release | — | ✓ | — | — | — | — |
| Reject fund release | — | ✓ | — | — | — | — |
| Resubmit rejected request | — | — | — | ✓ | — | — |
| Mark project complete | — | ✓ | — | — | — | — |
| Mark milestone complete | — | — | ✓ | — | — | — |
| Escalate to Auditor | — | ✓ | — | — | — | — |
| View AI flag queue (summary) | — | ✓ (summary) | — | — | — | — |
| View AI flag queue (full) | — | — | — | — | ✓ | — |
| Record audit finding | — | — | — | — | ✓ | — |
| Mark flag/escalation resolved | — | — | — | — | ✓ | — |
| View blockchain audit trail | — | ✓ | ✓ | ✓ | ✓ | — |
| View public project data | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |

> **REQUIREMENT (CR-07, FR-004):** All permission boundaries are enforced by the Server on every API request. Client-side access guards are for UX only and are not treated as a security boundary.

### 4.3 Project Assignment Scope

- **ACT-03** can only act on projects explicitly assigned to them by ACT-02.
- **ACT-04** can only act on milestones within projects explicitly assigned to them.
- **ACT-05** operates across all projects (platform-wide scope for audit purposes).
- **ACT-02** operates across all projects (platform-wide authority).
- **ACT-01** has no project-level operational visibility beyond system-level aggregate counts. ACT-01 does not view project financial data or internal workflow details.

> **DECISION (PDQ-07 from PRD):** ACT-01 is strictly limited to user management and system configuration. ACT-01 has no access to project financial data or fund release workflows.

---

## 5. Authentication and Access Control

### FRD-AUTH-001 — Login

**Actor:** ACT-01 through ACT-05
**Preconditions:** The actor has an active, non-deactivated account.
**Trigger:** Actor submits email/username and password on the login form.
**Main Flow:**
1. Actor enters email/username and password.
2. Server validates credentials against the hashed password.
3. On success, Server issues a signed JWT encoding the actor's user ID and role.
4. Client stores the JWT for subsequent API requests.
5. Client routes the actor to their role-specific dashboard.
**Result:** Actor is authenticated with access to role-appropriate views.
**Validation Rules:**
- Both fields are required.
- On credential mismatch, return a generic "Invalid credentials" error (do not reveal which field is incorrect).
- JWT expiry is enforced (duration is a TRD decision). Expired tokens are rejected.
**Exception Flow:**
- Deactivated account: Server returns "Account deactivated" error.
- Empty field: form prevents submission.
**Related PRD:** FR-001, FR-002, FR-003

---

### FRD-AUTH-002 — Logout

**Actor:** ACT-01 through ACT-05
**Preconditions:** Actor is logged in.
**Trigger:** Actor selects "Logout."
**Main Flow:**
1. Client discards the stored JWT.
2. Actor is redirected to the login screen.
**Result:** Client-side session is cleared. Actor cannot access protected views without re-authenticating.
**Notes:** Server-side token blacklisting strategy is a TRD decision.
**Related PRD:** FR-005

---

### FRD-AUTH-003 — JWT Validation on Every Request

**Actor:** Server (system behavior)
**Trigger:** Any HTTP request to a protected Server endpoint.
**Main Flow:**
1. Server extracts and validates the JWT (signature + expiry).
2. Server extracts the actor's role.
3. Server checks whether the role is permitted for the requested action.
4. If valid and authorized, the request proceeds.
**Exception Flow:**
- Missing/invalid/expired JWT: Server returns authentication error.
- Valid JWT but insufficient role: Server returns authorization error.
**Related PRD:** FR-003, FR-004

---

### FRD-AUTH-004 — Wallet Connection

**Actor:** ACT-04 (Contractor), ACT-05 (Auditor)
**Preconditions:** Actor is JWT-authenticated. A compatible browser wallet (e.g., MetaMask) is installed.
**Trigger:** Actor initiates an action requiring a blockchain-writing operation.
**Main Flow:**
1. Client prompts wallet connection.
2. Actor approves in the browser wallet.
3. Wallet address is available for blockchain transaction signing.
4. Wallet connection is independent of JWT authentication.
**Notes:** Which specific events require a connected wallet vs. Server-side signer is a TRD/System Architecture decision (PDQ-04).
**Related PRD:** FR-006

---

## 6. Platform Admin Functional Requirements

### FRD-ADMIN-001 — Create User Account

**Actor:** ACT-01
**Preconditions:** ACT-01 is authenticated.
**Trigger:** ACT-01 submits the user creation form.
**Main Flow:**
1. ACT-01 enters: full name, email address/username, initial role (Government Admin / Department Officer/Engineer / Contractor / Auditor), initial password or system-generated credential.
2. Server validates all fields.
3. Server creates the user account with hashed password and assigned role.
**Result:** New user account created; user can immediately log in.
**Validation Rules:**
- Full name: required, non-empty.
- Email/username: required, unique across all accounts. Duplicate rejected.
- Role: required, one of the four permitted values (ASSUMPTION / DEFERRED DECISION: FRD-ASS-06 — In the baseline application UI, Platform Admin can create Government Admin, Department Officer, Contractor, and Auditor accounts; restricting Platform Admin from creating another Platform Admin via standard UI is a baseline assumption deferred to TRD/System Architecture for administrative provisioning design).
- Password: required; exact policy is a TRD decision.
**Exception Flow:**
- Duplicate email: "Email already registered."
- Missing field: field-level validation error.
**Related PRD:** FR-010

---

### FRD-ADMIN-002 — Assign or Update User Role

**Actor:** ACT-01
**Preconditions:** ACT-01 is authenticated. Target account exists and is active.
**Trigger:** ACT-01 submits a role change for an existing user.
**Main Flow:**
1. ACT-01 selects a user and a new role.
2. Server updates the user's role in the database.
3. Role change takes effect at next login (exact JWT invalidation behavior is a TRD decision).
**Validation Rules:**
- New role must be a valid role value.
- ACT-01 cannot modify the only remaining Platform Admin account (ASSUMPTION).
**Related PRD:** FR-011

---

### FRD-ADMIN-003 — View User List

**Actor:** ACT-01
**Preconditions:** ACT-01 is authenticated.
**Trigger:** ACT-01 navigates to user management.
**Main Flow:** Server returns all user accounts. Client displays: name, email, role, status (Active/Deactivated), date created. List is filterable by role.
**Related PRD:** FR-012

---

### FRD-ADMIN-004 — Deactivate User Account

**Actor:** ACT-01
**Preconditions:** ACT-01 is authenticated. Target account exists and is Active.
**Trigger:** ACT-01 selects "Deactivate" on a user account.
**Main Flow:**
1. Server marks the account as Deactivated.
2. Subsequent login attempts by the user are rejected.
**Validation Rules:**
- ACT-01 cannot deactivate their own account.
- ACT-01 cannot deactivate the only remaining active Platform Admin (ASSUMPTION).
**Related PRD:** FR-013

---

### FRD-ADMIN-005 — Platform Admin Dashboard

**Actor:** ACT-01
**Preconditions:** ACT-01 is authenticated.
**Trigger:** ACT-01 logs in or navigates to dashboard.
**Main Flow:** Client displays system-level summary: total user counts by role, total project count, system status indicators. ACT-01 does not view project financial data, fund release details, or AI flag content.
**Related PRD:** FR-014

---

## 7. Government Admin Functional Requirements

### FRD-GOV-001 — View Government Admin Dashboard

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated.
**Trigger:** ACT-02 logs in or navigates to dashboard.
**Main Flow:** Client displays:
- Total active project count.
- Total allocated budget (sum across all projects).
- Total disbursed amount (sum of all approved fund releases).
- Count of pending fund release requests requiring ACT-02 decision.
- Count of active AI flags across all projects.
- Count of milestones marked Missed/Overdue.
- Count of open formal escalations.
- Navigable project list: name, status, budget, disbursed amount, physical progress %, latest AI risk score, overdue milestone count.
**Related PRD:** FR-022, FR-023

---

### FRD-GOV-002 — View Project Detail

**Actor:** ACT-02 (and ACT-03, ACT-04, ACT-05 within their permitted scope)
**Preconditions:** Actor is authenticated with project visibility.
**Trigger:** Actor selects a project.
**Main Flow:** Client displays:
- Project metadata: name, category, region/district, total budget, start/end dates, assigned officer, status.
- Financial utilization %: approved releases ÷ total budget.
- Physical progress %: latest ACT-03 entry.
- Financial utilization % vs. physical progress % chart over time.
- AI risk summary: latest score, flag count, highest-risk flag.
- Milestone list with statuses, due dates, budget portions.
- Fund release request history (all including rejected).
- Blockchain audit trail for the project.
- Escalation actions (ACT-02 only).
**Related PRD:** FR-023

---

### FRD-GOV-003 — Assign Department Officer to Project

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated. Project exists. Target user has role Department Officer/Engineer.
**Trigger:** ACT-02 selects an officer in the assignment control.
**Main Flow:**
1. ACT-02 selects a user with ACT-03 role.
2. Server stores the assignment in the project record.
3. ACT-03 can now view and act on the project.
**Validation Rules:**
- Assigned user must have Department Officer/Engineer role.
- A project has one assigned officer at a time in the baseline (ASSUMPTION / DEFERRED DECISION: FRD-ASS-02 — Single assigned officer per project is a baseline design assumption; multiple officer assignments are deferred to TRD/future releases). Re-assignment replaces the currently assigned officer.
**ASSUMPTION:** Single officer per project for MVP (FRD-ASS-02).
**Related PRD:** FR-021

---

## 8. Department Officer / Engineer Functional Requirements

### FRD-OFF-001 — View Assigned Projects

**Actor:** ACT-03
**Preconditions:** ACT-03 is authenticated. At least one project is assigned.
**Trigger:** ACT-03 logs in or navigates to the project list.
**Main Flow:** Client displays all projects assigned to ACT-03: name, status, budget, physical progress %, financial utilization %, milestone summary, overdue alert count.

---

### FRD-OFF-002 — View Milestone List

**Actor:** ACT-03
**Preconditions:** ACT-03 is authenticated and assigned to the project.
**Trigger:** ACT-03 navigates to the milestone list for a project.
**Main Flow:** Client displays all milestones: name, deliverable, budget portion, due date, status, latest physical progress %, cumulative approved fund releases.

---

### FRD-OFF-003 — Review Contractor Submission

**Actor:** ACT-03
**Preconditions:** A fund release request in Pending status exists for an ACT-03-assigned project.
**Trigger:** ACT-03 navigates to the fund release review view.
**Main Flow:**
1. ACT-03 views all Pending fund release requests for their assigned projects.
2. ACT-03 selects a request to review: requested amount, milestone context, IPFS evidence documents.
3. ACT-03 accesses each document via the in-browser document viewer.
**Related PRD:** FR-073

---

## 9. Contractor Functional Requirements

### FRD-CON-001 — View Assigned Milestones

**Actor:** ACT-04
**Preconditions:** ACT-04 is authenticated and assigned to a project with milestones.
**Trigger:** ACT-04 logs in or navigates to milestones.
**Main Flow:** Client displays all milestones for the assigned project(s): name, deliverable, budget portion, due date, status, ACT-04's own fund release request history per milestone.

---

### FRD-CON-002 — Monitor Fund Release Request Status

**Actor:** ACT-04
**Preconditions:** ACT-04 has submitted at least one fund release request.
**Trigger:** ACT-04 navigates to fund release history.
**Main Flow:** Client displays all fund release requests by ACT-04: status (Pending / Under Review / Approved / Rejected), amount, submission timestamp, rejection reason (if rejected).
**Related PRD:** FR-053

---

## 10. Auditor Functional Requirements

### FRD-AUD-001 — View Investigation Queue

**Actor:** ACT-05
**Preconditions:** ACT-05 is authenticated.
**Trigger:** ACT-05 logs in or navigates to the investigation queue.
**Main Flow:** Client displays two queues:
1. **AI flag queue:** All active AI flags sorted by risk score (highest first). Each shows: project name, risk score (0–100), anomaly type, contributing factor summary, flag timestamp, status.
2. **Escalation queue:** All formal escalations addressed to ACT-05. Each shows: project name, escalation reason summary, escalating admin, timestamp, linked flag (if any), status.
**Related PRD:** FR-097, FR-100

---

## 11. Citizen / Public Functional Requirements

### FRD-CIT-001 — Access Public Portal

**Actor:** ACT-06
**Preconditions:** None. No authentication required.
**Trigger:** ACT-06 visits the public portal URL.
**Main Flow:** Client renders the public portal without requiring login or registration. Portal is clearly separated from authenticated application routing.
**Related PRD:** FR-130

---

(Full public portal specification: Section 23.)

---

## 12. Project Management

### FRD-PROJ-001 — Create Infrastructure Project

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated.
**Trigger:** ACT-02 submits the project creation form.
**Main Flow:**
1. ACT-02 enters: project name, category, region/district, total budget, start date, expected end date.
2. Server validates all fields.
3. Server creates a Project record in PostgreSQL with status **Draft**.
4. Server submits a **ProjectCreated** blockchain event.
5. Server event listener indexes the confirmed on-chain event.
6. Client displays the new project dashboard.
7. ACT-02 proceeds to assign an officer (FRD-GOV-003) and allocate budget (FRD-PROJ-002).
**Result:** Project in PostgreSQL (status Draft). ProjectCreated event anchored on blockchain.
**Validation Rules:**
- Project name: required, non-empty.
- Category: required, from approved list.
- Region/district: required, non-empty.
- Total budget: required, positive number > 0.
- Start date: required, valid date.
- Expected end date: required, valid date, must be after start date.
**Exception Flow:**
- Any required field missing: field-level validation errors; project not created.
- Blockchain event failure: Server logs failure, returns error to Client, and does NOT report the project creation as successfully completed. See Section 27.2.
**Related PRD:** FR-020, Workflow A

---

### FRD-PROJ-002 — Budget Allocation

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated. Project exists (status Draft or Active).
**Trigger:** ACT-02 confirms budget allocation.
**Main Flow:**
1. ACT-02 confirms the budget amount and allocating authority.
2. Server records the budget allocation in PostgreSQL.
3. Server submits a **BudgetAllocated** blockchain event.
4. Project status transitions to **Active** when an officer is also assigned.
**Result:** Budget allocation recorded. BudgetAllocated event anchored on blockchain.
**ASSUMPTION:** Budget allocation and project creation may be a single combined action in the UI or a two-step confirmation. The TRD/UI/UX Design will confirm the exact flow.
**Related PRD:** FR-024, Workflow B

---

### FRD-PROJ-003 — View Project List

**Actor:** ACT-02, ACT-03 (assigned), ACT-04 (assigned), ACT-05 (all), ACT-01 (aggregate counts)
**Preconditions:** Actor is authenticated.
**Trigger:** Actor navigates to the project list.
**Main Flow:** Server returns projects within the actor's permitted scope. Client displays a sortable, filterable table: name, category, region, budget, disbursed amount, physical progress %, status, AI risk score, overdue milestone count.
**Related PRD:** FR-022

---

### FRD-PROJ-004 — Project Completion

**Actor:** ACT-02
**Preconditions:**
- ACT-02 is authenticated.
- Project currently has status **Active**.
- All milestones defined for the project are resolved: no milestone remains in **Active** status (every defined milestone must have reached either **Completed** or **Missed/Overdue** status).
- No fund release requests for the project remain unresolved: no requests in **Pending**, **Under Review**, or **Pending Admin Decision** status (all fund release requests must be in terminal status **Approved** or **Rejected**).
**Trigger:** ACT-02 selects "Mark Project as Completed."
**Main Flow:**
1. Server validates all completion preconditions.
2. Server transitions Project status to **Completed** in PostgreSQL.
3. Server submits a **ProjectCompleted** blockchain event.
4. Project becomes read-only (no new milestones, fund releases, or progress updates).
**Result:** Project status Completed. ProjectCompleted event anchored on blockchain.
**Validation Rules:**
- Project not in Active status: Server rejects with descriptive error.
- Any milestone still in Active status: Server rejects with descriptive error ("All milestones must be Completed or Missed/Overdue before closing the project").
- Any fund release request Pending, Under Review, or Pending Admin Decision: Server rejects with descriptive error ("All fund release requests must be resolved before closing the project").
**Exception Flow:**
- Preconditions not met: Server returns error describing which condition is not satisfied.
- Blockchain event failure: See Section 27.2.

> **DECISION (PDQ-05 resolved):** Only ACT-02 (Government Admin) can mark a project as Completed. Required functional conditions: (1) The project must currently be Active; (2) No milestones may remain in Active status (all milestones must be either Completed or Missed/Overdue; Missed/Overdue milestones do NOT block project completion, as the Government Admin may formally close a project with acknowledged missed/overdue milestones); (3) No fund release requests may remain active or unresolved (no requests in Pending, Under Review, or Pending Admin Decision status). Upon completion, the project status transitions to Completed in PostgreSQL, a ProjectCompleted blockchain event is anchored, and the project becomes read-only.

**Related PRD:** Workflow N, PDQ-05

---

## 13. Budget and Fund Management

### FRD-FUND-001 — Track Financial Utilization

**Actor:** Server (system behavior, displayed to ACT-02, ACT-03, ACT-05)
**Behavior:**
- Financial utilization amount = sum of all approved fund release amounts for the project.
- Financial utilization % = (total approved fund releases ÷ total project budget) × 100.
- Computed from fund release records; updated immediately upon each approval.
**Related PRD:** FR-030, FR-031, FR-032, CR-14

---

### FRD-FUND-002 — Retain All Fund Release History

**Actor:** Server (system enforcement)
**Behavior:**
- All fund release request records (including rejected) are permanently retained.
- No fund release record is ever deleted or overwritten.
- Fund release history view shows all versions: status, timestamp, amount, rejection reason.
**Related PRD:** FR-033, CR-16

---

### FRD-FUND-003 — Partial Fund Release Support

**Actor:** ACT-04 (submission), ACT-02 (approval)
**Behavior:**
- A contractor may request any amount > 0 up to the milestone's remaining unallocated budget.
- Multiple partial releases against a single milestone are permitted until cumulative approved amount reaches the milestone's budget portion.
**Validation Rules:**
- Requested amount must be > 0.
- Requested amount must not exceed the milestone's remaining available budget (milestone budget portion minus cumulative approved releases for that milestone).
**Related PRD:** FR-034, CR-15

---

## 14. Milestone Management

### FRD-MILE-001 — Define Milestone

**Actor:** ACT-03
**Preconditions:** ACT-03 is authenticated and assigned to the project. Project has status **Active**.
**Trigger:** ACT-03 submits the milestone creation form.
**Main Flow:**
1. ACT-03 enters: milestone name, deliverable description, budget portion, due date.
2. Server validates all fields.
3. Server creates a Milestone record in PostgreSQL with status **Active**.
4. Server submits a **MilestoneDefined** blockchain event.
5. Milestone appears in the project's milestone list.
6. Server triggers AI risk analysis for the project (Section 19).
**Result:** Milestone record (status Active) in PostgreSQL. MilestoneDefined event anchored on blockchain. AI risk analysis triggered.
**Validation Rules:**
- Milestone name: required, non-empty.
- Deliverable description: required, non-empty.
- Budget portion: required, > 0. Sum of all milestone budget portions for project must not exceed the project total budget.
- Due date: required, must be within project start/end date range.
**Exception Flow:**
- Budget portion would exceed available project budget: Server rejects with available budget remaining.
**Related PRD:** FR-040, Workflow C

---

### FRD-MILE-002 — View Milestone Status

**Actor:** ACT-03, ACT-02, ACT-04, ACT-05
**Trigger:** Actor navigates to milestone list or project detail view.
**Main Flow:** Server returns milestone data. Client displays for each: name, deliverable, budget portion, due date, status (Active / Completed / Missed/Overdue), latest physical progress %, cumulative approved fund releases, remaining budget portion.
**Related PRD:** FR-045

---

### FRD-MILE-003 — Server-Side Missed Milestone Detection

**Actor:** Server (automated)
**Preconditions:** Project is Active. At least one milestone is Active.
**Trigger:** Server's periodic scheduled check runs (frequency is a TRD decision — FRD-DEF-01/PDQ-02).
**Main Flow:**
1. Server retrieves all Active milestones.
2. For each: evaluate (current date > milestone due date) AND (milestone status ≠ Completed).
3. If both true: Server transitions milestone status to **Missed/Overdue** in PostgreSQL.
4. Server creates in-app notifications for the assigned ACT-03 and project's ACT-02.
5. Missed/Overdue status becomes an input feature for AI on the next analysis trigger.
**Result:** Missed milestones marked in PostgreSQL. Notifications generated.
**Notes:**
- AI does NOT determine missed status; the Server does this deterministically.
- Missed/Overdue does not automatically revert; only explicit completion action (FRD-MILE-004) changes the status.
**Related PRD:** FR-042, FR-043, FR-044, CR-25

---

### FRD-MILE-004 — Milestone Completion

**Actor:** ACT-03
**Preconditions:** ACT-03 is authenticated and assigned to the project. Milestone has status Active or Missed/Overdue. Project has status Active.
**Trigger:** ACT-03 selects "Mark Milestone as Completed."
**Main Flow:**
1. ACT-03 explicitly marks the milestone as Completed via deliberate action.
2. Server validates preconditions.
3. Server transitions milestone status to **Completed** in PostgreSQL.
4. If the milestone was Missed/Overdue, Completed supersedes that status.
**Result:** Milestone status is Completed.

> **DECISION (PDQ-01 resolved):** Milestone completion is an explicit ACT-03 action. It is NOT automatically triggered by fund release approval, financial utilization reaching 100%, or physical progress reaching 100%. Three concepts remain strictly separate: (1) physical progress % (officer-entered), (2) financial utilization % (derived from approved fund releases), (3) milestone completion status (officer-declared explicit action).

**Notes:**
- A milestone can be Completed even if the full milestone budget has not been released (partial release scenario).
- A milestone can be Completed even after being Missed/Overdue.
**Related PRD:** FR-041, PDQ-01

---

## 15. Fund Release Workflow

### FRD-FREL-001 — Contractor Submits Fund Release Request

**Actor:** ACT-04
**Preconditions:**
- ACT-04 is authenticated and assigned to the project/milestone.
- Milestone has status Active or Missed/Overdue.
- Project has status Active.
- No other fund release request for this milestone is currently in Pending, Under Review, or Pending Admin Decision status (ASSUMPTION: FRD-ASS-01).
**Trigger:** ACT-04 submits the fund release request form.
**Main Flow:**
1. ACT-04 specifies the requested amount.
2. ACT-04 uploads supporting evidence documents (invoices, bills, site photographs) — see Section 17.
3. Server validates request amount and evidence.
4. Server creates a FundRelease record in PostgreSQL with status **Pending**.
5. Server submits a **FundReleaseRequested** blockchain event (including IPFS CIDs).
6. Server event listener indexes the confirmed on-chain event.
7. Server creates in-app notification for ACT-03 to review.
**Result:** FundRelease record (Pending) in PostgreSQL. FundReleaseRequested anchored on blockchain. ACT-03 notified.
**Validation Rules:**
- Requested amount: > 0, must not exceed milestone's remaining available budget (BR-03).
- At least one evidence document must be attached.
- Only one active request per milestone at a time (ASSUMPTION: FRD-ASS-01, BR-04).
**Exception Flow:**
- Amount exceeds available budget: descriptive error showing remaining amount.
- IPFS upload failure: Section 27.3.
- Blockchain event failure: Section 27.2.
- Another active request exists: Server rejects with appropriate error.
**Related PRD:** FR-050, FR-051, FR-052, Workflow E

---

### FRD-FREL-002 — Department Officer Reviews Submission

**Actor:** ACT-03
**Preconditions:** ACT-03 is authenticated and assigned to the project. A Pending fund release request exists.
**Trigger:** ACT-03 opens the fund release review view.
**Main Flow:**
1. Fund release status transitions from **Pending** to **Under Review** when ACT-03 begins review.
2. ACT-03 views: requested amount, milestone context, all IPFS evidence documents.
3. ACT-03 prepares a verification/inspection report document.
4. ACT-03 uploads the verification report to IPFS via the Server.
5. ACT-03 selects: **Recommend Approval** or **Recommend Rejection** (rejection requires a reason).
6. ACT-03 submits the verification report and recommendation.
7. Server records the verification report CID and recommendation in PostgreSQL.
8. Server submits an **OfficerVerified** blockchain event (including IPFS CID and recommendation outcome).
9. Fund release status transitions to **Pending Admin Decision**.
10. Server creates in-app notification for ACT-02.
11. Server triggers AI risk analysis for the project (Section 19).
**Result:** OfficerVerified anchored on blockchain. Status is Pending Admin Decision. ACT-02 notified. AI risk analysis triggered.
**Related PRD:** Workflows F, H

---

### FRD-FREL-003 — Government Admin Approves Fund Release

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated. A fund release in **Pending Admin Decision** status exists.
**Trigger:** ACT-02 selects "Approve."
**Main Flow:**
1. ACT-02 confirms approval.
2. Server transitions FundRelease status to **Approved** in PostgreSQL.
3. Server updates project financial utilization.
4. Server submits a **FundReleaseApproved** blockchain event.
5. Server event listener indexes the event.
6. Server creates in-app notification for ACT-04.
7. Server triggers AI risk analysis for the project (Section 19).
**Result:** FundRelease status Approved. Financial utilization updated. FundReleaseApproved anchored. ACT-04 notified. AI triggered.
**Related PRD:** FR-030, FR-032, Workflow G

---

### FRD-FREL-004 — Government Admin Rejects Fund Release

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated. A fund release in **Pending Admin Decision** status exists.
**Trigger:** ACT-02 selects "Reject."
**Main Flow:**
1. ACT-02 enters a rejection reason (mandatory).
2. Server transitions FundRelease status to **Rejected** in PostgreSQL. Rejection reason stored.
3. Server submits a **FundReleaseRejected** blockchain event (with a hash of the rejection reason; full reason in PostgreSQL).
4. Server event listener indexes the event.
5. Server creates in-app notification for ACT-04 with rejection reason.
6. Rejected request remains permanently in fund release history.
**Result:** FundRelease status Rejected. FundReleaseRejected anchored. Rejection reason stored. ACT-04 notified.
**Validation Rules:**
- Rejection reason: required, non-empty.
**Related PRD:** FR-033, Workflow G

---

### FRD-FREL-005 — Contractor Resubmission After Rejection

**Actor:** ACT-04
**Preconditions:** ACT-04 is authenticated. At least one Rejected fund release request exists for the milestone. Milestone still has remaining available budget. Project is Active.
**Trigger:** ACT-04 creates a new fund release request for the same milestone.
**Main Flow:**
1. ACT-04 prepares a new request with possibly revised amount and revised evidence.
2. Server creates a NEW FundRelease record (new record; not an update to the rejected one).
3. All prior rejected requests remain permanently in fund release history.
4. Workflow proceeds from FRD-FREL-001.
**Result:** New FundRelease Pending. Rejected history preserved. All prior blockchain events remain on-chain.
**Related PRD:** FR-054, CR-16, Workflow H

---

## 16. Physical Progress Management

### FRD-PROG-001 — Record Physical Progress Update

**Actor:** ACT-03
**Preconditions:** ACT-03 is authenticated and assigned to project. Milestone has status Active or Missed/Overdue.
**Trigger:** ACT-03 submits a physical progress update.
**Main Flow:**
1. ACT-03 selects a milestone, enters physical progress % (0–100) and optional description.
2. Server validates input.
3. Server creates a ProjectProgress record in PostgreSQL: milestone ID, progress %, description, timestamp, entered by (ACT-03).
4. Progress history is retained; prior records are not overwritten.
5. Physical progress does NOT trigger a blockchain event.
6. Physical progress does NOT directly trigger AI analysis; it is an input feature available on the next analysis trigger.
**Result:** Timestamped progress record stored. Latest physical progress % for the milestone updated.
**Validation Rules:**
- Progress %: required, numeric, 0–100 inclusive.
- Progress % should not be less than the previous entry for the same milestone (ASSUMPTION / DEFERRED DECISION: FRD-ASS-04 — Baseline assumption that physical progress does not regress; whether this is enforced as a hard validation error, a soft warning, or permitted with justification is a UI/UX and TRD design decision).
**Related PRD:** FR-060, CR-14

---

### FRD-PROG-002 — View Physical Progress History

**Actor:** ACT-03, ACT-02, ACT-05
**Trigger:** Actor navigates to progress history for a milestone.
**Main Flow:** Client displays all historical progress records in chronological order: timestamp, progress %, description, entered by.
**Related PRD:** FR-062

---

## 17. Evidence and Document Management

### FRD-DOC-001 — Upload Evidence Document

**Actor:** ACT-04 (contractor evidence), ACT-03 (verification report), ACT-05 (audit report)
**Preconditions:** Actor is authenticated with appropriate role. Upload is part of an in-progress workflow step.
**Trigger:** Actor attaches a document and submits.
**Main Flow:**
1. Client sends file to Server.
2. Server validates file type and size.
3. Server uploads file to IPFS via Pinata API.
4. Pinata returns an IPFS CID.
5. Server stores a Document record in PostgreSQL: CID, document type, uploader ID, timestamp, linked entity ID.
6. CID returned to the calling workflow for blockchain event anchoring.
**Result:** Document on IPFS (pinned). Document record in PostgreSQL. CID available for blockchain anchoring.
**Validation Rules:**
- File must not be empty.
- Supported types: PDF, JPEG, PNG (exact MIME types and size limits are TRD decisions).
- At least one document required for fund release requests (enforced by FRD-FREL-001).
**Exception Flow:**
- IPFS/Pinata failure: Server returns error; workflow step not completed; actor may retry. Section 27.3.
- Invalid file type: Server rejects with descriptive error.
**Related PRD:** FR-070, FR-071, CR-03

---

### FRD-DOC-002 — View / Access Document

**Actor:** ACT-04, ACT-03, ACT-05, ACT-02 (for review)
**Preconditions:** Actor is authenticated with project access. Document CID exists in the Document table.
**Trigger:** Actor selects a document link.
**Main Flow:**
1. Client retrieves the IPFS CID via Server API.
2. Client constructs the IPFS gateway URL (exact gateway configuration is a TRD decision).
3. Document is displayed in the in-browser document viewer (PDF or image viewer).
**Notes:**
- **REQUIREMENT (CR-23):** Documents are unencrypted. A privacy notice is displayed wherever document upload or access occurs.
- ACT-06 (Citizen) does not have access to IPFS document links.
**Related PRD:** FR-072, FR-074

---

### FRD-DOC-003 — Document Viewer in Investigation View

**Actor:** ACT-05
**Preconditions:** ACT-05 is authenticated. An AI flag or escalation is selected for investigation.
**Trigger:** ACT-05 views investigation detail.
**Main Flow:** Investigation detail view directly renders all IPFS evidence documents linked to the flag or escalation (contractor evidence, verification report) without requiring navigation away.
**Related PRD:** FR-073

---

## 18. Blockchain Audit Trail Functional Behavior

### FRD-CHAIN-001 — Ten Confirmed On-Chain Event Types

The Server submits the appropriate event for each confirmed lifecycle action:

| Event | Triggering Action | Key Anchored Data |
|---|---|---|
| ProjectCreated | FRD-PROJ-001 | Project ID, creation timestamp, total budget |
| BudgetAllocated | FRD-PROJ-002 | Project ID, allocation amount, allocating authority |
| MilestoneDefined | FRD-MILE-001 | Project ID, milestone ID, budget portion, due date hash |
| FundReleaseRequested | FRD-FREL-001 | Project ID, milestone ID, requested amount, evidence IPFS CIDs |
| OfficerVerified | FRD-FREL-002 | Project ID, milestone ID, recommendation outcome, verification report IPFS CID |
| FundReleaseApproved | FRD-FREL-003 | Project ID, milestone ID, approved amount |
| FundReleaseRejected | FRD-FREL-004 | Project ID, milestone ID, rejection reason hash |
| AIAnomalyRecorded | FRD-AI-005 | Project ID, flag ID, risk score (0–100), anomaly type |
| AuditFindingRecorded | FRD-AUD-004 | Project ID, flag ID, auditor wallet address, finding outcome, audit report IPFS CID |
| ProjectCompleted | FRD-PROJ-004 | Project ID, completion timestamp, final financial utilization |

**REQUIREMENT (CR-02, FR-080):** All ten event types must be demonstrable on the local Hardhat network.

---

### FRD-CHAIN-002 — Server-Side Event Indexing

**Behavior:**
- The Server runs a blockchain event listener subscribing to on-chain events from the deployed contracts.
- Confirmed events are recorded in the BlockchainEvent table in PostgreSQL: event type, project/entity ID, transaction hash, block number, block timestamp, key data fields.
- Client queries the blockchain audit trail through the Server API (reading from the PostgreSQL index), not by directly calling the blockchain node.
- PostgreSQL index provides query speed; blockchain provides the tamper-evident guarantee.
- Re-indexing or recovery mechanism for listener failures is a TRD decision.
**Related PRD:** FR-081, CR-18

---

### FRD-CHAIN-003 — Blockchain Audit Trail View

**Actor:** ACT-02, ACT-03, ACT-04, ACT-05
**Trigger:** Actor navigates to the blockchain audit trail for a project.
**Main Flow:**
1. Client requests audit trail from Server.
2. Server returns all BlockchainEvent records for the project from the PostgreSQL index, ordered chronologically.
3. Client displays each event: event type (human-readable), key data, transaction hash, block timestamp.
4. Transaction hash enables independent verification on the Hardhat local node or a compatible explorer.
**Related PRD:** FR-082, FR-083

---

### FRD-CHAIN-004 — Blockchain Anchoring and Completion Behavior

**Behavior:**
- A lifecycle action that requires blockchain anchoring must not be reported to the user as successfully completed until the required blockchain event has been confirmed.
- If blockchain anchoring fails (e.g., local Hardhat node unavailable, transaction reverted, gas exhaustion):
  - The Server logs the failure.
  - The system must not present the lifecycle action as fully successful.
  - The Server returns an error to the Client indicating that blockchain anchoring was not confirmed.
  - The actor may retry the action.
- Exact database/blockchain consistency, retry, reconciliation, transaction ordering, and recovery mechanisms are implementation decisions for the TRD/System Architecture. The platform does not assume literal two-phase or atomic commit across PostgreSQL and the blockchain.
**Related PRD:** NFR-19

---

## 19. AI Risk and Anomaly Detection Functional Behavior

### FRD-AI-001 — AI Analysis Trigger Events

**Actor:** Server (automated)
**Behavior:** Every eligible AI trigger event must cause the Server to execute the defined AI/risk analysis for the relevant project.

The eligible triggers are:
1. A fund release request is **approved** by ACT-02 (FRD-FREL-003).
2. An officer verification is **submitted** by ACT-03 (FRD-FREL-002).
3. A new milestone is **defined** by ACT-03 (FRD-MILE-001).

The AI analysis must execute for every eligible trigger event regardless of whether the eventual risk score crosses the configured flagging threshold.

The trigger and execution mechanism (synchronous vs. asynchronous, background task) is a TRD/System Architecture decision (PDQ-03). Functionally: the triggering HTTP response must not be blocked for long durations by AI computation.
**Related PRD:** FR-091, CR-21

---

### FRD-AI-002 — AI Analysis Input Data

**Actor:** Server (AI module within Server process)
**Behavior:** When triggered, the AI module loads from PostgreSQL:
- Latest physical progress % per milestone.
- Cumulative financial utilization % (approved releases ÷ total budget).
- Milestone Missed/Overdue status (Server-determined, not AI-determined).
- Fund release request history: amounts, dates, frequency, contractor.
- Invoice/evidence metadata: amounts, submission dates, contractor.
**Related PRD:** FR-091, FR-092

---

### FRD-AI-003 — AI Detection Capabilities

| Capability ID | Capability | Approach |
|---|---|---|
| AI-CAP-01 | Financial utilization vs. physical progress mismatch | Rule-based: flag when financial utilization % exceeds physical progress % by a configurable threshold margin. |
| AI-CAP-02 | Budget overrun risk | Trajectory rule: project spend rate vs. remaining budget and timeline. |
| AI-CAP-03 | Project delay risk | Date-based rule: uses Server-determined Missed/Overdue milestone count/status as input signal. |
| AI-CAP-04 | Near-duplicate invoice detection | Similarity scoring on invoice metadata (amount, vendor, date, project). |
| AI-CAP-05 | Unusual spending patterns | Isolation Forest: unsupervised anomaly detection on transaction amounts, timing, and contractor patterns. |

**Related PRD:** FR-094, FR-095

---

### FRD-AI-004 — Risk Score and Contributing Factors

**Behavior:**
- AI module produces a **risk score as an integer in the range 0–100** for each analysis run.
- Alongside the score, AI produces **contributing factors**: human-readable explanations identifying which signals drove the score (e.g., "Financial utilization: 82% vs Physical progress: 25% — mismatch threshold exceeded by 35 percentage points").
- Each contributing factor identifies: the capability (AI-CAP-01 through AI-CAP-05), relevant feature values, and a human-readable description of the anomaly.
- SHAP values may optionally be used for Isolation Forest explainability (AI/ML Design decision); rule-based explanations are sufficient for deterministic signals.
**Related PRD:** FR-092, FR-093, CR-13

---

### FRD-AI-005 — AI Flag Creation

**Actor:** Server (automated)
**Preconditions:** AI analysis executed and completed by the Server following an eligible trigger event.
**Behavior:**
The AI analysis produces a project risk score (0–100 integer) and contributing factors:
- **If the risk score meets or exceeds the configured threshold:**
  - Server creates an **AIFlag** record in PostgreSQL (project ID, risk score, anomaly type(s), contributing factors, status **Open**, creation timestamp).
  - Server submits an **AIAnomalyRecorded** blockchain event (referencing flag ID and risk score).
  - Server creates an in-app notification for ACT-05 (Auditor).
  - ACT-02 may view a summary of the flag in the project dashboard.
- **If the risk score is below the configured threshold:**
  - No AI anomaly flag is created.
  - The analysis result may still be internally recorded or logged where appropriate.
  - No blockchain AIAnomalyRecorded event is submitted.
  - No notification is sent to ACT-05.
**Related PRD:** FR-096

---

### FRD-AI-006 — AI Disclaimer (Mandatory)

**REQUIREMENT (CR-04, FR-098):** The following disclaimer must appear on every view presenting AI risk results, risk scores, or flag details:

> *"AI risk flags indicate anomalous patterns and do not establish fraud or corruption."*

Views requiring the disclaimer: AI flag queue, flag detail/investigation view, project dashboard AI risk summary, any other view surfacing AI risk scores or flag information.

AI does NOT determine guilt, wrongdoing, fraud, or corruption. All final audit conclusions are human decisions.
**Related PRD:** FR-098, CR-04

---

### FRD-AI-007 — AI Flag Status Lifecycle

| Status | Meaning |
|---|---|
| Open | Flag created by AI; awaiting Auditor review. |
| Under Review | Auditor has opened the investigation. |
| Reviewed — No Action | Investigation complete; no further action required. |
| Escalated Externally | The platform records that the Auditor has referred/recommended the matter for handling outside this platform. NOTE: This is an internal status record; the platform does NOT integrate with external government authorities, investigation agencies, or external case-management systems. |

(Full state model: Section 25.5)

---

### FRD-AI-008 — AI Analysis Failure

**Behavior:**
- If AI module encounters an error (data loading failure, model inference error): Server logs the failure.
- The triggering lifecycle action (e.g., fund release approval) is NOT rolled back due to AI failure.
- No user-facing error is shown for the AI failure component of the triggering action.
- If no AIFlag is created due to the failure, the Auditor queue is unaffected for that event.
**Related PRD:** Section 27

---

## 20. Auditor Investigation Workflow

### FRD-AUD-001 — View Investigation Queue

(See Section 10, FRD-AUD-001.)

---

### FRD-AUD-002 — Open Investigation Detail

**Actor:** ACT-05
**Preconditions:** ACT-05 is authenticated. An AI flag or escalation with status Open or Under Review exists.
**Trigger:** ACT-05 selects a flag or escalation from the queue.
**Main Flow:**
1. AIFlag or Escalation status transitions to **Under Review** (if it was Open).
2. ACT-05 views:
   - Risk score and contributing factors (for AI flags).
   - Escalation reason and escalating admin (for formal escalations).
   - Project metadata (name, budget, disbursed amount, physical progress %, milestone summary).
   - Fund release request history.
   - All linked IPFS evidence documents in the document viewer.
   - Full blockchain audit trail for the project.
3. ACT-05 reviews all information and forms a conclusion.
**Related PRD:** FR-101, FR-102, Workflow L

---

### FRD-AUD-003 — Access IPFS Evidence from Investigation

**Actor:** ACT-05
**Preconditions:** Investigation detail is open.
**Trigger:** ACT-05 selects an evidence document.
**Main Flow:** Document viewer resolves the IPFS CID and renders the document in-browser. ACT-05 does not need to navigate away.
**Related PRD:** FR-073, FR-101

---

### FRD-AUD-004 — Record Audit Finding

**Actor:** ACT-05
**Preconditions:** ACT-05 is authenticated with a connected wallet. An AI flag or escalation is Under Review.
**Trigger:** ACT-05 submits the audit finding form.
**Main Flow:**
1. ACT-05 writes a detailed audit report narrative.
2. ACT-05 selects a finding outcome: "Reviewed — No Action Required" or "Escalated to External Authorities" (denoting referral outside the platform).
3. ACT-05 submits the form.
4. Server uploads the audit report to IPFS (FRD-DOC-001).
5. Server creates an AuditFinding record in PostgreSQL: project ID, flag/escalation ID, outcome, IPFS CID, auditor user ID, timestamp.
6. ACT-05's connected wallet signs and submits an **AuditFindingRecorded** blockchain event: project ID, flag ID, auditor wallet address, finding outcome, audit report IPFS CID.
7. Server event listener indexes the confirmed on-chain event.
8. AIFlag or Escalation status updated to the determined outcome.
9. Server creates in-app notification for ACT-02 (Government Admin).
**Result:** AuditFinding in PostgreSQL. AuditFindingRecorded anchored on blockchain. ACT-02 notified.
**Validation Rules:**
- Audit report narrative: required, non-empty.
- Finding outcome: required, one of the two permitted values.
- Connected wallet: required.
**Exception Flow:**
- IPFS upload failure: ACT-05 receives error; finding not submitted; may retry.
- Blockchain event failure: ACT-05 receives error indicating the on-chain event could not be confirmed; finding is not reported as successfully recorded; ACT-05 may retry. See Section 27.2.

**Clarification on External Escalation:** Selecting "Escalated to External Authorities" records on-chain and in PostgreSQL that the Auditor has referred or recommended the matter for external handling outside this platform. The platform does NOT have an integration with an external government authority, law-enforcement agency, or external case-management system.
**Related PRD:** FR-103, FR-104, FR-105, Workflow M

---

### FRD-AUD-005 — Mark Flag/Escalation Status

**Actor:** ACT-05
**Preconditions:** ACT-05 has submitted an audit finding (FRD-AUD-004).
**Behavior:** Outcome selection during FRD-AUD-004 determines the final status:
- "Reviewed — No Action Required" → AIFlag/Escalation status: **Reviewed — No Action**.
- "Escalated to External Authorities" → AIFlag/Escalation status: **Escalated Externally**.
Both outcomes are recorded on-chain via AuditFindingRecorded.

**Explicit Clarification:** The status "Escalated Externally" means the platform records that the Auditor has referred or recommended the matter for handling outside this platform. It does NOT mean the platform has an actual integration with an external government authority, investigation agency, or external case-management system.
**Related PRD:** FR-105

---

## 21. Government Admin to Auditor Escalation Workflow

### FRD-ESC-001 — Create Formal Escalation

**Actor:** ACT-02
**Preconditions:** ACT-02 is authenticated. Project exists (status Active). An optional AIFlag may be linked.
**Trigger:** ACT-02 selects "Escalate to Auditor."
**Main Flow:**
1. ACT-02 is shown an escalation form.
2. ACT-02 enters an escalation reason (mandatory).
3. If escalating from an AI flag view, the system auto-links the Escalation to the AIFlag record.
4. Server validates the escalation reason.
5. Server creates an Escalation record in PostgreSQL: project ID, escalating admin ID, reason, linked AIFlag ID (if any), status **Open**, timestamp.
6. Server creates in-app notification for ACT-05.
**Result:** Escalation record in PostgreSQL. ACT-05 notified. Escalation appears in ACT-05's investigation queue.
**Validation Rules:**
- Escalation reason: required, non-empty.
**Notes:** No blockchain event is produced for the escalation itself. The AuditFindingRecorded event is produced later by ACT-05.
**Related PRD:** FR-110, FR-111, FR-112, FR-113, CR-26, Workflow K

---

### FRD-ESC-002 — Escalation Record Permanence

**Behavior:** Escalation records are permanent and never deleted from PostgreSQL. All escalations (resolved or open) are queryable for audit purposes and viewable by ACT-02 and ACT-05.
**Related PRD:** FR-114, CR-26

---

### FRD-ESC-003 — Auditor Investigates Escalation

**Behavior:** An escalation is investigated using the same workflow as an AI flag (FRD-AUD-002 through FRD-AUD-005). Escalations appear in the ACT-05 investigation queue alongside AI flags.
**Related PRD:** FR-100, FR-101

---

## 22. Notification Functional Requirements

### FRD-NOTIF-001 — Notification Delivery Events

All notifications are in-app only. No email or SMS.

**Notification Processing Mechanics:** The relevant lifecycle action generates the appropriate in-app notification as part of the Server-side processing workflow. A failure in notification creation or delivery must not cause an otherwise valid lifecycle action to fail or be considered unsuccessful. Exact database transaction handling, retry behavior, and delivery mechanics are implementation decisions deferred to the TRD/System Architecture.

| Event | Notification Recipient(s) | Triggering FRD |
|---|---|---|
| Fund release request submitted | ACT-03 (assigned officer) | FRD-FREL-001 |
| Officer verification submitted | ACT-02 (Government Admin) | FRD-FREL-002 |
| Fund release request approved | ACT-04 (submitting contractor) | FRD-FREL-003 |
| Fund release request rejected (with reason) | ACT-04 (submitting contractor) | FRD-FREL-004 |
| Milestone marked Missed/Overdue | ACT-03 (assigned officer), ACT-02 (Government Admin) | FRD-MILE-003 |
| New AI flag created | ACT-05 (Auditor) | FRD-AI-005 |
| New formal escalation received | ACT-05 (Auditor) | FRD-ESC-001 |
| Audit finding recorded | ACT-02 (Government Admin) | FRD-AUD-004 |

**Related PRD:** FR-120, PR-01

---

### FRD-NOTIF-002 — Notification Centre

**Actor:** ACT-01 through ACT-05
**Behavior:**
- Each authenticated actor has a Notification Centre in the primary navigation.
- Displays: event type label, brief description, timestamp, link to the relevant project or record.
- Unread notification count is shown as a badge on the Notification Centre icon.
- Notifications are marked as read when viewed.
- Read status is persisted per actor.
**Related PRD:** FR-121, FR-122

---

## 23. Citizen Transparency Portal

### FRD-CIT-001 — Public Portal Access

**Actor:** ACT-06 (no authentication required)
**Behavior:** Public portal is accessible without login or registration. No citizen data is collected. Portal routing is clearly separated from the authenticated application.
**Related PRD:** FR-130

---

### FRD-CIT-002 — Public Project List

**Actor:** ACT-06
**Behavior:**
- Portal displays all projects with status Active or Completed.
- Filterable by: region/district, project category.
- Each entry shows: project name, category, region, total budget, total amount disbursed, physical progress % (latest), current status.
**Related PRD:** FR-131, FR-132

---

### FRD-CIT-003 — Public Project Detail

**Actor:** ACT-06
**Behavior:**
- ACT-06 can select a project to view its public detail page.
- Displays: project name, category, region, total budget, total disbursed amount, physical progress %, milestone names and due dates, current status, major public fund event timeline.
- Major public fund event timeline includes: project creation date, key approval milestones (aggregated, non-sensitive), project completion date.
- If ACT-02 has published an audit summary for the project, it is displayed.
**Related PRD:** FR-132, FR-133, FR-134

---

### FRD-CIT-004 — Sensitive Data Exclusion (Strict)

**REQUIREMENT (FR-135, CR-07):** The following data must never be accessible from the public portal through any API endpoint or Client route:

| Excluded Data |
|---|
| User account data (names, emails, credentials, roles) |
| Contractor identity and contact details |
| Internal fund release request details (amounts, submission documents, rejection reasons) |
| IPFS document links for contractor evidence or officer verification reports |
| AI flag details, risk scores, contributing factors, anomaly type information |
| Auditor investigation details (before formal publication) |
| Internal workflow state (pending approval queues, review status) |

Any ACT-06 access to excluded data constitutes a functional defect.
**Related PRD:** FR-135

---

### FRD-CIT-005 — Published Audit Summary

**Actor:** ACT-02 (publisher), ACT-06 (viewer)
**ASSUMPTION / DEFERRED DECISION (FRD-ASS-05):** ACT-02 explicitly selects a completed AuditFinding record and marks it as "Publish to Public Portal." The published summary shows the finding outcome (not full internal details). The exact publishing mechanism, approval gates, and display criteria are design assumptions deferred to UI/UX Design and TRD.
**Behavior:** When ACT-02 marks a finding as published, it becomes visible on the project's public portal page. Unpublished audit findings are not visible to ACT-06.
**Related PRD:** FR-134

---

## 24. Dashboard and Reporting Functional Requirements

### FRD-DASH-001 — Financial vs Physical Progress Chart

**Actor:** ACT-02, ACT-03, ACT-05
**Behavior:** The project detail view includes a chart showing financial utilization % vs. physical progress % over time, using historical data from approved fund releases and ProjectProgress records. Both series are clearly labeled as distinct metrics.
**Related PRD:** FR-063, FR-140, CR-14

---

### FRD-DASH-002 — AI Risk Score History Chart (P2)

**Actor:** ACT-05, ACT-02
**Priority:** P2
**Behavior:** The project/Auditor view includes a chart showing AI risk score history over time, with data points corresponding to each AI analysis run.
**Related PRD:** FR-141

---

### FRD-DASH-003 — AI Flag Summary Per Project

**Actor:** ACT-02, ACT-05
**Behavior:** Project detail view includes: total AI flag count, highest risk score observed, count of flags by status (Open, Under Review, Resolved).
**Related PRD:** FR-142

---

### FRD-DASH-004 — Audit Finding History

**Actor:** ACT-02, ACT-05
**Behavior:** Project detail or Auditor view includes a list of all AuditFinding records for the project: outcome, IPFS audit report CID (with document link), auditor name, timestamp.
**Related PRD:** FR-143

---

### FRD-DASH-005 — Overdue Milestone Summary

**Actor:** ACT-02 (portfolio level), ACT-03 (assigned projects)
**Behavior:** Dashboards display count of Missed/Overdue milestones. Navigable to a list showing: project name, milestone name, days overdue.
**Related PRD:** FR-022

---

## 25. State Models and State Transitions

### 25.1 Project State Model

| State | Meaning | Allowed Transitions | Actor / Trigger |
|---|---|---|---|
| Draft | Project created; budget and officer assignment pending. | → Active | ACT-02 completes budget allocation and officer assignment |
| Active | Project running; milestones, releases, and progress updates accepted. | → Completed | ACT-02 marks complete (FRD-PROJ-004; project Active, no Active milestones [all Completed or Missed/Overdue], no unresolved fund releases [no Pending, Under Review, or Pending Admin Decision]) |
| Completed | All lifecycle actions complete; project is read-only. | (terminal) | — |

---

### 25.2 Milestone State Model

| State | Meaning | Allowed Transitions | Actor / Trigger |
|---|---|---|---|
| Active | Milestone in progress; deadline check applies. | → Completed | ACT-03 explicitly marks complete (FRD-MILE-004) |
| Active | Milestone in progress; deadline check applies. | → Missed/Overdue | Server periodic check (FRD-MILE-003) |
| Missed/Overdue | Deadline passed without completion; notifications generated. | → Completed | ACT-03 explicitly marks complete (FRD-MILE-004) |
| Completed | Work declared complete by ACT-03. | (terminal) | — |

> **DECISION:** Milestone completion requires explicit ACT-03 action; not automatic from any financial or progress event.

---

### 25.3 Fund Release Request State Model

| State | Meaning | Allowed Transitions | Actor / Trigger |
|---|---|---|---|
| Pending | Submitted by ACT-04; awaiting ACT-03 review. | → Under Review | ACT-03 opens for review |
| Under Review | ACT-03 is actively reviewing. | → Pending Admin Decision | ACT-03 submits verification (FRD-FREL-002) |
| Pending Admin Decision | Officer verification submitted; ACT-02 must decide. | → Approved | ACT-02 approves (FRD-FREL-003) |
| Pending Admin Decision | Officer verification submitted; ACT-02 must decide. | → Rejected | ACT-02 rejects (FRD-FREL-004) |
| Approved | Fund release approved; financial utilization updated; AI triggered. | (terminal) | — |
| Rejected | Fund release rejected; reason stored; ACT-04 may resubmit as new request. | (terminal; record retained) | — |

---

### 25.4 Escalation State Model

| State | Meaning | Allowed Transitions | Actor / Trigger |
|---|---|---|---|
| Open | Submitted by ACT-02; awaiting Auditor. | → Under Review | ACT-05 opens investigation (FRD-AUD-002) |
| Under Review | ACT-05 investigating. | → Reviewed — No Action | ACT-05 records finding (FRD-AUD-004) |
| Under Review | ACT-05 investigating. | → Escalated Externally | ACT-05 records finding (FRD-AUD-004) |
| Reviewed — No Action | Investigation complete; no further action. | (terminal; record retained) | — |
| Escalated Externally | Recorded as referred/recommended for handling outside this platform (no automated external integration exists). | (terminal; record retained) | — |

---

### 25.5 AI Flag State Model

| State | Meaning | Allowed Transitions | Actor / Trigger |
|---|---|---|---|
| Open | AI flag created above threshold; awaiting Auditor. | → Under Review | ACT-05 opens investigation (FRD-AUD-002) |
| Under Review | ACT-05 investigating. | → Reviewed — No Action | ACT-05 records finding (FRD-AUD-004) |
| Under Review | ACT-05 investigating. | → Escalated Externally | ACT-05 records finding (FRD-AUD-004) |
| Reviewed — No Action | Investigation complete. | (terminal; record retained) | — |
| Escalated Externally | Recorded as referred/recommended for handling outside this platform (no automated external integration exists). | (terminal; record retained) | — |

---

## 26. Business Rules and Validation Rules

### Business Rules (BR)

| BR ID | Rule |
|---|---|
| BR-01 | Financial utilization % = (sum of approved fund releases for project ÷ total project budget) × 100. Computed from fund release records; always consistent with underlying data. |
| BR-02 | A milestone's budget portion may not cause the sum of all milestone budget portions for a project to exceed the project's total budget. |
| BR-03 | A fund release request amount must not exceed the milestone's remaining available budget (milestone budget portion minus sum of all approved releases for that milestone). |
| BR-04 | Only one active fund release request per milestone at a time (ASSUMPTION: FRD-ASS-01 — no Pending, Under Review, or Pending Admin Decision request may exist when a new one is submitted; whether concurrent requests are permitted is deferred to TRD/System Architecture). |
| BR-05 | All rejected fund release request records are permanent. Never deleted, hidden, or modified after rejection. |
| BR-06 | Physical progress % and financial utilization % are separate data streams with separate update mechanisms. Must be displayed as distinct values throughout all views. |
| BR-07 | Milestone completion (status = Completed) requires explicit ACT-03 action; not derived automatically from any financial or progress event. |
| BR-08 | Missed/Overdue milestone status is determined by the Server's periodic deadline check, not by AI or manual action. |
| BR-09 | A project may only be marked Completed by ACT-02 when: (a) project is currently Active, (b) no milestones remain in Active status (all milestones are Completed or Missed/Overdue; Missed/Overdue milestones do not block completion), and (c) no fund release requests remain unresolved (no requests in Pending, Under Review, or Pending Admin Decision status). |
| BR-10 | AI risk flags do not determine guilt, fraud, corruption, or wrongdoing. The AI disclaimer must appear on all AI result views. All final audit decisions are made by ACT-05. |
| BR-11 | Escalation requires a non-empty reason from ACT-02. |
| BR-12 | Audit finding requires a non-empty narrative and an explicit outcome from ACT-05. |
| BR-13 | Public Citizen portal must not expose user account data, contractor identity, IPFS evidence links, AI flag details, or internal workflow data. |
| BR-14 | A lifecycle action that requires blockchain anchoring must not be reported to the user as successfully completed until the required blockchain event has been confirmed. If blockchain anchoring fails, the system must not present the lifecycle action as fully successful. Exact database/blockchain consistency, retry, and recovery mechanisms are deferred to the TRD/System Architecture. |
| BR-15 | Documents on IPFS are unencrypted. This limitation is displayed as a notice wherever document upload or access occurs. |

---

### Validation Rules (VR)

| VR ID | Field / Context | Rule |
|---|---|---|
| VR-01 | User email | Required, unique across all user accounts. |
| VR-02 | User role | Required, one of the valid role values. |
| VR-03 | Project name | Required, non-empty string. |
| VR-04 | Project category | Required, from the approved category list. |
| VR-05 | Project region/district | Required, non-empty string. |
| VR-06 | Project total budget | Required, numeric, > 0. |
| VR-07 | Project start date | Required, valid date. |
| VR-08 | Project expected end date | Required, valid date, must be after start date. |
| VR-09 | Milestone name | Required, non-empty string. |
| VR-10 | Milestone deliverable description | Required, non-empty string. |
| VR-11 | Milestone budget portion | Required, numeric, > 0; must not cause total milestone budget to exceed project total budget. |
| VR-12 | Milestone due date | Required, valid date, within project start/end date range. |
| VR-13 | Fund release requested amount | Required, numeric, > 0; must not exceed milestone remaining available budget. |
| VR-14 | Fund release evidence | At least one document required per fund release request. |
| VR-15 | Rejection reason | Required, non-empty (for officer recommendation rejection and Government Admin rejection). |
| VR-16 | Physical progress % | Required, numeric, 0–100 inclusive. |
| VR-17 | Escalation reason | Required, non-empty string. |
| VR-18 | Audit report narrative | Required, non-empty string. |
| VR-19 | Audit finding outcome | Required, one of: "Reviewed — No Action Required", "Escalated to External Authorities" (denoting off-platform referral; no automated external integration exists). |
| VR-20 | Document file type | Must be one of the approved MIME types (exact list is a TRD decision; at minimum: PDF, JPEG, PNG). |

---

## 27. Error and Exception Behavior

### 27.1 Unauthorized Action

**Behavior:** When an actor attempts an action outside their role or project assignment:
- Server returns authorization error.
- Client displays: "You are not authorized to perform this action."
- No state change occurs.
**Related PRD:** AC-20

---

### 27.2 Blockchain Transaction Failure

**Behavior:**
- The Server logs the failure.
- If blockchain anchoring fails, the system must not present the lifecycle action as fully successful.
- The Server returns an error to the Client.
- The Client displays: "Blockchain event could not be recorded. The action was not completed successfully. Please try again."
- The actor may retry the action.
- Exact database/blockchain consistency, transaction ordering, retry, reconciliation, and recovery mechanisms are implementation decisions deferred to the TRD/System Architecture.

---

### 27.3 IPFS Upload Failure

**Behavior:**
- Server returns error to Client.
- Workflow step requiring the document is NOT completed.
- Client displays: "Document upload failed. Please retry or check your connection."
- No partial blockchain event is submitted for the step requiring the CID.
- Actor may retry.

---

### 27.4 Invalid Project or Milestone State

**Behavior:**
- Server validates the state precondition before processing.
- If invalid state: Server returns descriptive error (e.g., "This project has been completed. No new fund release requests are accepted.").
- No state change occurs.

---

### 27.5 Duplicate or Blocked Submission

**Behavior:**
- Active fund release request already exists for the milestone (ASSUMPTION: FRD-ASS-01): "A fund release request is already active for this milestone. Wait for the current request to be resolved before resubmitting."
- Duplicate email on user creation: "This email address is already registered."

---

### 27.6 AI Analysis Failure

See FRD-AI-008. The triggering lifecycle action is not rolled back; AI failure is logged internally and does not produce a visible error to the actor for the lifecycle action itself.

---

### 27.7 Missing Required Fields

**Behavior:**
- Client: form prevents submission; inline field-level error messages displayed.
- Server: validates all required fields independently; returns field-level validation errors if bypassed.
- Submit button is disabled while a request is in-flight.
**Related PRD:** AC-22

---

### 27.8 Not Found / Stale Reference

**Behavior:**
- If a referenced resource does not exist: Server returns a not-found error.
- Client displays an appropriate message and suggests navigation back to the list view.

---

## 28. Cross-Module Functional Rules

### FRD-CROSS-001 — Server-Side RBAC Across All Modules

Every API endpoint, for every module, enforces role-based access control at the Server level. Client navigation guards are UX aids only. This rule applies uniformly across all modules.

---

### FRD-CROSS-002 — Blockchain Event Required for Lifecycle Actions

A lifecycle action that requires blockchain anchoring must not be reported to the user as successfully completed until the required blockchain event has been confirmed. If blockchain anchoring fails, the system must not present the lifecycle action as fully successful. Exact database/blockchain consistency, retry, reconciliation, transaction ordering, and recovery mechanisms are implementation decisions deferred to the TRD/System Architecture. The platform does not imply that PostgreSQL updates and blockchain transactions are literally committed atomically.

---

### FRD-CROSS-003 — Physical Progress and Financial Utilization Are Always Separate

No view, dashboard, chart, or data field may conflate physical progress % with financial utilization %. They must be labeled distinctly and sourced from their respective data streams (ProjectProgress records vs. approved FundRelease records) across all views.

---

### FRD-CROSS-004 — AI Disclaimer on All AI Result Views

The mandatory AI disclaimer must be present on every view displaying AI risk scores, AI flags, or contributing factors. No exceptions.

---

### FRD-CROSS-005 — Document Privacy Notice

Wherever document upload or document access UI is presented: "Documents stored on IPFS are unencrypted. Anyone with the document link can access it." This limitation (CR-23) must be consistently communicated.

---

### FRD-CROSS-006 — Rejection History Retained

All rejected fund release request records are permanent and must appear in the fund release history view. No administrative action may delete or archive a rejected request record.

---

### FRD-CROSS-007 — Notification Generation in Lifecycle Workflows

The relevant lifecycle action generates the appropriate in-app notification as part of the Server-side processing workflow. A notification failure must not cause an otherwise valid lifecycle action to be considered unsuccessful. Exact database transaction handling, retry behavior, and implementation mechanics are deferred to the TRD/System Architecture (no literal transaction atomicity guarantee between lifecycle actions and notifications is implied).

---

## 29. Functional Requirements Traceability

| PRD FR ID | PRD Requirement Summary | FRD Specification | Section |
|---|---|---|---|
| FR-001 | Login interface | FRD-AUTH-001 | §5 |
| FR-002 | JWT issuance | FRD-AUTH-001 | §5 |
| FR-003 | JWT validation on every request | FRD-AUTH-003 | §5 |
| FR-004 | Server-side RBAC enforcement | FRD-AUTH-003, FRD-CROSS-001 | §5, §28 |
| FR-005 | Logout | FRD-AUTH-002 | §5 |
| FR-006 | Wallet connection | FRD-AUTH-004 | §5 |
| FR-010 | Create user account | FRD-ADMIN-001 | §6 |
| FR-011 | Assign/update role | FRD-ADMIN-002 | §6 |
| FR-012 | View user list | FRD-ADMIN-003 | §6 |
| FR-013 | Deactivate user | FRD-ADMIN-004 | §6 |
| FR-014 | Platform Admin dashboard | FRD-ADMIN-005 | §6 |
| FR-020 | Create infrastructure project | FRD-PROJ-001 | §12 |
| FR-021 | Assign officer to project | FRD-GOV-003 | §7 |
| FR-022 | Government Admin dashboard | FRD-GOV-001 | §7 |
| FR-023 | Government Admin project detail | FRD-GOV-002 | §7 |
| FR-024 | Budget allocation + blockchain event | FRD-PROJ-002 | §12 |
| FR-030 | Track financial utilization | FRD-FUND-001 | §13 |
| FR-031 | Financial utilization formula | FRD-FUND-001 | §13 |
| FR-032 | Financial utilization updated on approval | FRD-FREL-003 | §15 |
| FR-033 | Retain rejected requests permanently | FRD-FUND-002, FRD-CROSS-006 | §13, §28 |
| FR-034 | Partial fund releases | FRD-FUND-003 | §13 |
| FR-040 | Define milestones | FRD-MILE-001 | §14 |
| FR-041 | Milestone status tracking | FRD-MILE-002, §25.2 | §14, §25 |
| FR-042 | Server periodic deadline check | FRD-MILE-003 | §14 |
| FR-043 | Mark milestone Missed/Overdue | FRD-MILE-003 | §14 |
| FR-044 | Notify ACT-02 and ACT-03 on overdue | FRD-MILE-003, FRD-NOTIF-001 | §14, §22 |
| FR-045 | Milestone list view | FRD-MILE-002 | §14 |
| FR-050 | Submit fund release request | FRD-FREL-001 | §15 |
| FR-051 | Upload evidence on submission | FRD-FREL-001, FRD-DOC-001 | §15, §17 |
| FR-052 | FundReleaseRequested blockchain event | FRD-FREL-001, FRD-CHAIN-001 | §15, §18 |
| FR-053 | Monitor fund release request status | FRD-CON-002 | §9 |
| FR-054 | Resubmit after rejection | FRD-FREL-005 | §15 |
| FR-055 | Notify ACT-04 on approval/rejection | FRD-NOTIF-001 | §22 |
| FR-060 | Record physical progress update | FRD-PROG-001 | §16 |
| FR-061 | Display physical progress separately | FRD-CROSS-003 | §28 |
| FR-062 | Retain physical progress history | FRD-PROG-001, FRD-PROG-002 | §16 |
| FR-063 | Financial vs physical progress chart | FRD-DASH-001 | §24 |
| FR-070 | Server handles all IPFS uploads | FRD-DOC-001 | §17 |
| FR-071 | Store IPFS CID in Document table | FRD-DOC-001 | §17 |
| FR-072 | In-browser document viewer | FRD-DOC-002 | §17 |
| FR-073 | Evidence accessible from investigation | FRD-DOC-003, FRD-AUD-003 | §17, §20 |
| FR-074 | IPFS privacy limitation displayed | FRD-CROSS-005 | §28 |
| FR-080 | Submit 10 confirmed blockchain events | FRD-CHAIN-001 | §18 |
| FR-081 | Server-side event indexing | FRD-CHAIN-002 | §18 |
| FR-082 | Blockchain audit trail Client view | FRD-CHAIN-003 | §18 |
| FR-083 | Tamper-evident audit trail | FRD-CHAIN-003, FRD-CROSS-002 | §18, §28 |
| FR-084 | Solidity events + minimal contract state | FRD-CHAIN-001 (note; implementation in TRD) | §18 |
| FR-085 | Local Hardhat network | FRD-CHAIN-001 (note) | §18 |
| FR-090 | AI runs within Server process | FRD-AI-001 | §19 |
| FR-091 | AI triggered by lifecycle events | FRD-AI-001 | §19 |
| FR-092 | Risk score 0–100 | FRD-AI-004 | §19 |
| FR-093 | Contributing factors | FRD-AI-004 | §19 |
| FR-094 | Rule-based signals (4 capabilities) | FRD-AI-003 (AI-CAP-01 to AI-CAP-04) | §19 |
| FR-095 | Isolation Forest | FRD-AI-003 (AI-CAP-05) | §19 |
| FR-096 | AIFlag creation + AIAnomalyRecorded | FRD-AI-005 | §19 |
| FR-097 | Auditor flag queue | FRD-AUD-001 | §10 |
| FR-098 | AI disclaimer mandatory | FRD-AI-006, FRD-CROSS-004 | §19, §28 |
| FR-099 | AI validation on synthetic data | AI/ML Design scope; noted in §19 | §19 |
| FR-100 | Auditor investigation queue | FRD-AUD-001 | §10 |
| FR-101 | Investigation detail view | FRD-AUD-002 | §20 |
| FR-102 | Blockchain audit trail from investigation | FRD-AUD-002 | §20 |
| FR-103 | Write audit report (IPFS upload) | FRD-AUD-004 | §20 |
| FR-104 | AuditFindingRecorded on-chain | FRD-AUD-004 | §20 |
| FR-105 | Mark flag/escalation resolved | FRD-AUD-005 | §20 |
| FR-110 | Government Admin creates escalation | FRD-ESC-001 | §21 |
| FR-111 | Escalation reason required | FRD-ESC-001, VR-17 | §21, §26 |
| FR-112 | Server creates Escalation record | FRD-ESC-001 | §21 |
| FR-113 | Auditor notified of escalation | FRD-ESC-001, FRD-NOTIF-001 | §21, §22 |
| FR-114 | Escalation records are permanent | FRD-ESC-002 | §21 |
| FR-120 | In-app notifications for key events | FRD-NOTIF-001 | §22 |
| FR-121 | Notification centre content | FRD-NOTIF-002 | §22 |
| FR-122 | Mark notifications as read | FRD-NOTIF-002 | §22 |
| FR-123 | No email/SMS | FRD-NOTIF-001 | §22 |
| FR-130 | Citizen portal no-login access | FRD-CIT-001 | §23 |
| FR-131 | Public project list with filters | FRD-CIT-002 | §23 |
| FR-132 | Public project summary data | FRD-CIT-002, FRD-CIT-003 | §23 |
| FR-133 | Public fund event timeline | FRD-CIT-003 | §23 |
| FR-134 | Published audit summaries | FRD-CIT-005 | §23 |
| FR-135 | Sensitive data excluded from portal | FRD-CIT-004 | §23 |
| FR-140 | Financial vs physical progress chart | FRD-DASH-001 | §24 |
| FR-141 | Risk score history chart (P2) | FRD-DASH-002 | §24 |
| FR-142 | AI flag summary per project | FRD-DASH-003 | §24 |
| FR-143 | Audit finding history | FRD-DASH-004 | §24 |
| FR-144 | PDF export (stretch) | Not specified; stretch goal only | — |

### 29.2 MVP vs Stretch vs Out of Scope

| Priority | Scope | FRD Coverage |
|---|---|---|
| P1 (MVP) | All P1 requirements from PRD | Fully specified in this FRD |
| P2 (Should have) | FR-063, FR-141, FR-134, FR-099 | Specified; marked P2 where relevant |
| P3 (Stretch) | FR-144 (PDF export), XGBoost, Sepolia | Not specified; excluded from MVP scope |

---

## 30. FRD-Level Open Questions and Deferred Decisions

### 30.1 Resolved in This FRD

| PDQ | Question | Resolution |
|---|---|---|
| PDQ-01 | Milestone completion trigger | Resolved: FRD-MILE-004. Explicit ACT-03 action. Not auto-triggered by financial events. |
| PDQ-05 | Project completion authorization and conditions | Resolved: FRD-PROJ-004. Authorized actor: ACT-02 only. Explicit functional completion conditions: project must be Active, no milestones remain Active (all are Completed or Missed/Overdue; Missed/Overdue milestones do not block closure), and no fund release requests remain unresolved (no Pending, Under Review, or Pending Admin Decision requests). |

---

### 30.2 Deferred to TRD / System Architecture

| ID | Question | Target Document |
|---|---|---|
| FRD-DEF-01 (PDQ-02) | Server-side missed milestone check interval | TRD |
| FRD-DEF-02 (PDQ-03) | AI analysis sync vs. async execution model | TRD / System Architecture |
| FRD-DEF-03 (PDQ-04) | Blockchain signing model (Server-side signer vs. actor wallet per event) | TRD / System Architecture |
| FRD-DEF-04 | JWT token expiry duration and token revocation for role changes (PDQ-08 from PRD) | TRD |
| FRD-DEF-05 | Supported document MIME types and maximum file size limits | TRD |
| FRD-DEF-06 | PostgreSQL database schema (tables, columns, indexes, foreign keys) | TRD / System Architecture |
| FRD-DEF-07 | API endpoint paths, HTTP methods, request/response schemas | TRD |
| FRD-DEF-08 | Solidity function signatures, struct definitions, event parameters | TRD / System Architecture |
| FRD-DEF-09 | Isolation Forest hyperparameters, feature set, training pipeline | AI/ML Design |
| FRD-DEF-10 | IPFS gateway URL configuration (Pinata dedicated vs. public gateway) | TRD |

---

### 30.3 FRD-Level Assumptions and Deferred Decisions Requiring Review

The following design choices are explicitly designated as functional assumptions and deferred decisions rather than immutable product requirements. They establish the operational baseline for MVP and require confirmation in TRD, System Architecture, or UI/UX Design before implementation.

| ID | Assumption / Deferred Decision | Status & Baseline Context | Impact if Modified |
|---|---|---|---|
| FRD-ASS-01 | Only one active fund release request per milestone at a time (BR-04). | Functional Assumption (baseline restriction against concurrent Pending/Under Review/Pending Admin Decision requests). | If concurrent requests are intended, fund release workflow and concurrency controls require revision in TRD. |
| FRD-ASS-02 | Single assigned officer (ACT-03) per project. | Functional Assumption (1:1 project-to-officer assignment baseline). | If multiple officers or engineering teams are needed, assignment model and notification routing require revision. |
| FRD-ASS-03 | Budget allocation and project creation may be a combined action or two-step confirmation. | Deferred Design Decision (workflow sequence). | UI/UX Design and TRD must confirm whether allocation is bundled into creation or remains a distinct step. |
| FRD-ASS-04 | Physical progress % should not regress (decrease from previous entry). | Functional Assumption (monotonic progress baseline). | UI/UX Design and TRD must confirm exact enforcement mechanism (hard validation error vs. soft warning vs. justification note). |
| FRD-ASS-05 | Published audit summary mechanism: ACT-02 explicitly marks an AuditFinding as "Publish to Public Portal." | Functional Assumption (explicit publishing gate for public portal). | If publishing mechanism or authority differs, public portal and Government Admin workflows require revision in TRD. |
| FRD-ASS-06 | Platform Admin cannot create another Platform Admin via the standard user creation form. | Functional Assumption (standard user creation UI restriction). | If multiple Platform Admins or bootstrap creation is required, administrative provisioning design must be defined in TRD. |

---

*End of Document — FRD v1.0.1*

*This FRD is based on the approved Project Definition v0.3.0 and PRD v1.1.0. It is a draft pending review. No application code, scaffolding, or dependency installation shall begin until the subsequent TRD is approved.*

*The next document in the sequence is: `docs/03-TRD.md` (Technical Requirements Document). Do NOT begin the TRD until this FRD is reviewed and approved.*
