# docs/01-PRD.md

**Document Type:** Product Requirements Document (PRD)
**Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform
**Version:** 1.1.0
**Status:** APPROVED
**Date (created):** 2026-09-28
**Date (last updated):** 2026-09-28
**Author:** AI Engineering Agent
**Source Baseline:** Project Definition v0.3.0 (`docs/PROJECT-DEFINITION.md`)

---

## Change History

| Version | Date | Author | Summary |
|---|---|---|---|
| 1.1.0 | 2026-09-28 | AI Engineering Agent | Scope cleanup revision to defer implementation details to downstream design documents and align documentation structure. |
| 1.0.0 | 2026-09-28 | AI Engineering Agent | Initial PRD created from approved Project Definition v0.3.0. All 29 confirmed requirements (CR-01–CR-29) incorporated. |

---

> **Terminology standard (mandatory throughout this document):**
> - The React/Vite web application is always called the **Client**.
> - The Python/FastAPI application is always called the **Server**.
> - Do NOT use "front-end client", "backend server", "frontend server", or "backend" when referring to the Server.
> - Directory names `frontend/` and `backend/` in the monorepo are NOT renamed by this convention.

---

## Table of Contents

1. [Product Overview](#1-product-overview)
2. [Product Goals](#2-product-goals)
3. [Non-Goals](#3-non-goals)
4. [Actors and Access Model](#4-actors-and-access-model)
5. [Core Product Modules](#5-core-product-modules)
6. [End-to-End Product Workflows](#6-end-to-end-product-workflows)
7. [Functional Requirements](#7-functional-requirements)
8. [Non-Functional Requirements](#8-non-functional-requirements)
9. [AI Product Requirements](#9-ai-product-requirements)
10. [Blockchain Product Requirements](#10-blockchain-product-requirements)
11. [IPFS Product Requirements](#11-ipfs-product-requirements)
12. [Citizen Transparency Requirements](#12-citizen-transparency-requirements)
13. [Notification Requirements](#13-notification-requirements)
14. [UI/UX Product Requirements](#14-uiux-product-requirements)
15. [Data and Evidence Requirements](#15-data-and-evidence-requirements)
16. [Scope and Prioritization](#16-scope-and-prioritization)
17. [Acceptance Criteria](#17-acceptance-criteria)
18. [Traceability Matrix](#18-traceability-matrix)
19. [Open Issues and Future Design Decisions](#19-open-issues-and-future-design-decisions)

---

## 1. Product Overview

### 1.1 Product Purpose

This product is a web-based platform that enables transparent tracking of public infrastructure fund lifecycles, provides tamper-evident blockchain-anchored audit records, stores supporting evidence on IPFS, and uses AI/ML to flag anomalous spending and progress patterns for human auditor review.

The platform serves government administrators, department officers, contractors, auditors, and the general public.

### 1.2 Problem Being Addressed

Public infrastructure projects — roads, bridges, schools, water supply, housing — are funded through government budgets or development grants. Their fund lifecycles typically pass through multiple stages: budget allocation, release to contractors against milestones, submission of work-completion evidence, officer verification, and formal audit.

The key problems this platform addresses:

| Problem | Impact |
|---|---|
| Lack of transparency | Citizens and auditors cannot independently verify how public funds are used. |
| Opaque and manual approval chains | Fund release approvals leave no tamper-evident record. |
| Weak evidence tracking | Supporting documents are siloed in physical files or internal systems, inaccessible to auditors. |
| Fraud and misappropriation risk | Funds may be released without proportional physical progress; invoices may be duplicated or inflated. |
| Reactive and slow auditing | Audits happen years after the fact, making remediation difficult. |
| No proactive anomaly detection | Unusual spending patterns and progress mismatches go unnoticed without manual investigation. |

### 1.3 Proposed Solution

A unified platform integrating four technical pillars:

1. **PostgreSQL database** — stores all operational, financial, and progress data in a queryable relational form.
2. **Blockchain (Solidity + Hardhat)** — provides a tamper-evident, append-only audit trail of all important fund lifecycle events.
3. **IPFS (via Pinata)** — stores supporting documents (invoices, site photographs, verification reports, audit reports) off-chain; their content addresses are referenced in the database and anchored on-chain.
4. **AI/ML (hybrid rule-based + Isolation Forest)** — automatically detects anomalous or high-risk spending patterns and flags them for human auditor review.

The platform is delivered as a web Client (React + Vite) consumed by role-specific dashboards, backed by a Server (Python + FastAPI).

### 1.4 Target Users

| Actor | Summary |
|---|---|
| Platform Admin | System-level user management and configuration. |
| Government Admin | Project creation, budget allocation, fund release approvals, and escalation of concerns to Auditors. |
| Department Officer / Engineer | Milestone management, physical progress recording, contractor submission verification. |
| Contractor | Fund release request submission and evidence upload. |
| Auditor | AI flag review, formal escalation review, on-chain audit finding recording. |
| Citizen / Public | Public read-only portal — no registration required. |

### 1.5 Academic-Project Context

This is a final-year Electronics and Computer Science Engineering (ECS) college project. The platform is a **functional demonstration prototype** designed to:

- operate end-to-end in a local demo environment (developer laptop + local Hardhat network);
- demonstrate genuine integration of all four technical pillars;
- be presentable in a viva and withstand technical questioning;
- produce professional documentation covering the full software engineering lifecycle.

The platform does **not** need to meet production-government security standards, execute real financial transactions, integrate with real government identity or payment systems, or scale to enterprise-level load.

---

## 2. Product Goals

The following product-level goals are measurable or observable during demonstration and evaluation.

| Goal ID | Goal | Observable Outcome |
|---|---|---|
| G-01 | **Fund transparency** | Every fund release event — request, verification, approval, rejection — is recorded and queryable by authorised actors. No fund event is silently lost. |
| G-02 | **Tamper-evident auditability** | All important fund lifecycle events are anchored on the blockchain. On-chain events cannot be silently altered; any such event can be independently verified via the blockchain audit trail view. |
| G-03 | **Evidence traceability** | Every fund release request is linked to uploaded evidence documents (invoices, site photographs, bills). Each document has a verifiable IPFS CID. CIDs are referenced in the database and anchored on-chain. |
| G-04 | **Anomaly and risk detection** | The AI/ML module analyses fund and progress data after key lifecycle events and produces risk scores with contributing factor explanations. Designed anomaly scenarios in the demo dataset are flagged. |
| G-05 | **Human audit workflow** | Auditors have a structured queue: AI flags and formal Government Admin escalations. Auditors can inspect evidence, review the blockchain audit trail, and record on-chain audit findings. All final audit decisions are human-made. |
| G-06 | **Citizen transparency** | Any citizen can access the public portal without registration and view non-sensitive project summaries, fund event timelines, and published audit summaries. |
| G-07 | **End-to-end demonstrability** | The complete fund lifecycle — from project creation through contractor fund request, officer verification, admin approval, AI analysis, and auditor review — can be demonstrated end-to-end on a single developer laptop with a local Hardhat network. |
| G-08 | **Role-based access control** | Each actor sees only the views and performs only the actions permitted for their role. Role boundaries are enforced on the Server, not only on the Client. |
| G-09 | **Physical vs financial progress distinction** | The platform explicitly tracks physical progress (officer-entered %) and financial utilization (derived from approved fund releases) as separate data streams, enabling meaningful progress-vs-spend comparison. |

---

## 3. Non-Goals

The following are explicitly **out of scope** and must not be introduced at any stage unless the project scope is formally revised. These non-goals preserve the approved hard exclusions from the Project Definition v0.3.0.

| Non-Goal | Reason |
|---|---|
| Real government payment processing | This platform tracks and records fund lifecycle events. It does not execute actual financial transactions. |
| Integration with real government payment systems (e.g., PFMS) | Academic project scope. |
| Aadhaar or government identity integration | Academic project scope. |
| Real fraud or corruption determination | AI identifies anomalous patterns only. AI risk flags do not establish fraud or corruption. |
| Production-scale infrastructure | Not required for college demonstration. |
| Kubernetes or container orchestration | Explicitly excluded. |
| Unnecessary microservices | One Server process is sufficient. |
| Mobile native applications | Out of scope. |
| Deep learning models (e.g., neural networks, transformers) | Complexity inappropriate for this project. Rule-based + Isolation Forest is sufficient. |
| Email or SMS notification infrastructure | In-app notifications only for MVP. |
| Enterprise SSO or OAuth identity provider | JWT-based application auth is sufficient. |
| Custom blockchain consensus mechanism | Local Hardhat network with standard EVM consensus. |
| Encrypted IPFS document access control | Documents are unencrypted; this limitation is documented. |
| Full end-to-end automated test suite | Unit and integration tests are in scope; full automated E2E is not required. |
| Multi-tenant deployment across real government departments | Single-tenant demo deployment. |
| Mainnet blockchain deployment | Local Hardhat is primary; Sepolia is stretch only. |
| PDF export of audit reports | Stretch goal only. |
| Real-time streaming fraud alerts | Batch event-triggered analysis is sufficient. |

---

## 4. Actors and Access Model

### 4.1 Actor Summary

Six actors are confirmed by the approved Project Definition v0.3.0 (Section 4.2).

| Actor ID | Role Name | Auth Type | Access Category |
|---|---|---|---|
| ACT-01 | Platform Admin | Authenticated (JWT) | System administration |
| ACT-02 | Government Admin | Authenticated (JWT) | Project and fund management |
| ACT-03 | Department Officer / Engineer | Authenticated (JWT) | Milestone and progress management |
| ACT-04 | Contractor | Authenticated (JWT) | Fund release and evidence submission |
| ACT-05 | Auditor | Authenticated (JWT) | Anomaly investigation and audit findings |
| ACT-06 | Citizen / Public | No authentication | Public read-only access |

### 4.2 Role Responsibilities

**ACT-01 — Platform Admin**
- Creates and manages all user accounts.
- Assigns roles to users.
- Views system-level configuration.
- Does NOT participate in project fund workflows.
- Has access to all user management views but not to financial or project operational data.

**ACT-02 — Government Admin**
- Creates new infrastructure projects.
- Allocates budgets to projects.
- Reviews and approves or rejects contractor fund release requests.
- Views AI risk dashboards and overdue milestone alerts.
- Formally escalates projects, flags, or concerns to Auditors via an in-system escalation workflow.
- Views blockchain audit trail for any project.

**ACT-03 — Department Officer / Engineer**
- Is assigned to specific projects by Government Admin.
- Defines project milestones (name, deliverable, budget portion, due date).
- Records physical progress updates for milestones.
- Reviews contractor fund release submissions.
- Submits verification/inspection reports (uploaded to IPFS).
- Recommends approval or rejection of fund release requests.
- Receives in-app alerts for overdue milestones (determined by Server).

**ACT-04 — Contractor**
- Views their assigned project milestones.
- Submits fund release requests against milestones (partial or full milestone amount).
- Uploads supporting evidence documents (invoices, bills, site photographs) to IPFS via the platform.
- Monitors fund release request status.
- Receives in-app notifications on approval or rejection.
- Can resubmit rejected requests with revised evidence; prior rejected requests are permanently retained.

**ACT-05 — Auditor**
- Views the AI anomaly flag queue (sorted by risk score).
- Views formal Government Admin escalation queue.
- Inspects flag/escalation details: risk score, contributing factors, linked evidence, escalation reason.
- Accesses IPFS evidence documents directly from the investigation view.
- Views the full on-chain blockchain audit trail for any project.
- Records audit findings (detailed report uploaded to IPFS; AuditFindingRecorded event anchored on-chain).
- Marks flags/escalations as reviewed or escalated externally.

**ACT-06 — Citizen / Public**
- Accesses the public portal without registration or login.
- Browses active and completed infrastructure projects (filterable by region, project type).
- Views non-sensitive project summaries (budget, disbursed amount, physical progress %, milestone timeline).
- Views major fund event timeline for a project (non-sensitive events only).
- Views administrator-published audit summaries where available.
- Cannot access user account data, internal workflow details, or sensitive contractor documents.

### 4.3 Access Boundaries

| Resource | ACT-01 | ACT-02 | ACT-03 | ACT-04 | ACT-05 | ACT-06 |
|---|---|---|---|---|---|---|
| User management | ✓ | — | — | — | — | — |
| Project creation | — | ✓ | — | — | — | — |
| Budget allocation | — | ✓ | — | — | — | — |
| Milestone definition | — | — | ✓ | — | — | — |
| Physical progress entry | — | — | ✓ | — | — | — |
| Fund release submission | — | — | — | ✓ | — | — |
| Evidence upload | — | — | — | ✓ | — | — |
| Verification report submission | — | — | ✓ | — | — | — |
| Fund release approval/rejection | — | ✓ | — | — | — | — |
| Escalation to Auditor | — | ✓ | — | — | — | — |
| AI flag queue view | — | ✓ (summary) | — | — | ✓ (full) | — |
| Audit finding recording | — | — | — | — | ✓ | — |
| Blockchain audit trail view | — | ✓ | ✓ | ✓ | ✓ | — |
| Public project data | — | ✓ | ✓ | ✓ | ✓ | ✓ |

> Role boundaries are enforced by the Server. Client UI hides irrelevant actions, but the Server validates all requests independently.

---

## 5. Core Product Modules

### MOD-01 — Authentication and RBAC

Handles user login, JWT issuance, JWT validation, and role-based access enforcement across all Server endpoints and Client routes. Wallet connection is a separate concern from application authentication.

### MOD-02 — Platform Administration

Enables ACT-01 to create and manage user accounts, assign roles, and view system configuration. Isolated from all project fund workflows.

### MOD-03 — Government Project Management

Enables ACT-02 to create infrastructure projects, set project parameters (name, category, region, total budget, start/end dates), assign Department Officers, and view project dashboards showing fund and progress status.

### MOD-04 — Budget and Fund Tracking

Tracks the financial lifecycle of each project: budget allocation, fund release requests (including partial amounts), approved fund releases, rejected requests, and cumulative financial utilization. All fund events produce blockchain-anchored records.

### MOD-05 — Milestone Management

Enables ACT-03 to define project milestones (deliverable description, budget portion, due date). Milestones are the unit of fund release. The Server periodically checks milestone deadlines and determines Missed/Overdue status.

### MOD-06 — Contractor Fund Release Workflow

Enables ACT-04 to submit fund release requests against milestones, upload supporting evidence to IPFS, monitor request status, receive notifications, and resubmit rejected requests. Covers the full contractor-side fund release lifecycle.

### MOD-07 — Physical Progress Tracking

Enables ACT-03 to record physical progress updates (%) for milestones. Physical progress is distinct from financial utilization and is a key AI input signal. Progress records are timestamped and retained historically.

### MOD-08 — Evidence and Document Management

Handles evidence document uploads to IPFS via the Server. Stores IPFS CIDs in the database. Provides a document viewer in the Client. Links documents to their parent entities (fund release requests, verification reports, audit findings).

### MOD-09 — Blockchain Audit Trail

Provides the Client with a queryable, human-readable timeline of on-chain events for any project. The Server maintains an indexed copy of blockchain events in PostgreSQL via a Server-side event listener. The raw blockchain provides the source of tamper-evidence; the indexed copy provides fast Client queries.

### MOD-10 — AI Risk and Anomaly Detection

Runs within the Server process. Triggered after key fund lifecycle events. Produces risk scores (0–100 integer) with contributing factor explanations for each project. Flags high-risk cases to the Auditor queue. Uses a hybrid rule-based + Isolation Forest approach.

### MOD-11 — Auditor Investigation

Provides ACT-05 with a structured queue of AI flags and formal escalations. Enables the Auditor to inspect details, access linked evidence and blockchain audit trail, and record audit findings on-chain with an IPFS-hosted audit report.

### MOD-12 — Government Admin → Auditor Escalation

Enables ACT-02 to formally escalate a project, AI flag, or concern to an Auditor via an in-system workflow action. Creates an Escalation record in PostgreSQL. Notifies the Auditor in-app.

### MOD-13 — Notifications

In-app notification system. Delivers workflow event notifications to relevant actors. Notification types cover all key workflow events (approval, rejection, AI flag, overdue milestone, escalation, audit finding). No email or SMS.

### MOD-14 — Citizen Transparency Portal

Public-facing, no-login portal. Displays non-sensitive project summaries, fund event timelines, and published audit summaries. Filterable by region and project type.

### MOD-15 — Project and Audit Reporting

Provides views for project-level reporting: financial utilization vs. physical progress, AI risk score history, audit finding history, overdue milestone summary. Supports export features where defined (PDF export is a stretch goal).

---

## 6. End-to-End Product Workflows

### Workflow A — Project Creation

1. ACT-02 (Government Admin) logs in via JWT authentication.
2. ACT-02 creates a new infrastructure project by providing: name, category, region/district, total budget amount, start date, expected end date.
3. The Server creates a Project record in PostgreSQL.
4. The Server submits a **ProjectCreated** event to the blockchain.
5. The Server event listener indexes the confirmed on-chain event into the BlockchainEvent table in PostgreSQL.
6. ACT-02 is presented with the new project dashboard.

### Workflow B — Budget Allocation

1. ACT-02 allocates the confirmed budget to the project (may mirror project creation, or be a separate approval step).
2. The Server records the budget allocation in PostgreSQL.
3. The Server submits a **BudgetAllocated** event to the blockchain.
4. The Server assigns a Department Officer (ACT-03) to the project.

### Workflow C — Milestone Definition

1. ACT-03 (Department Officer) logs in and opens an assigned project.
2. ACT-03 defines one or more milestones for the project: milestone name, deliverable description, budget portion, due date.
3. The Server creates Milestone records in PostgreSQL.
4. For each milestone, the Server submits a **MilestoneDefined** event to the blockchain.
5. The Server event listener indexes the confirmed on-chain events.

### Workflow D — Physical Progress Updates

1. ACT-03 navigates to a milestone and records a physical progress update (e.g., "Road base laid — 40% physical progress").
2. The Server stores the progress record in the ProjectProgress table in PostgreSQL, timestamped and attributed to ACT-03.
3. Physical progress does NOT trigger a blockchain event directly. It is an internal data input used by the AI.
4. AI may use the latest physical progress % as a risk signal input when triggered by other events.

### Workflow E — Contractor Fund Release Request

1. ACT-04 (Contractor) logs in and opens an assigned milestone.
2. ACT-04 submits a fund release request specifying the requested amount (partial or full milestone amount).
3. ACT-04 uploads supporting evidence documents (invoices, bills, site photographs) — the Server uploads each to IPFS via Pinata and stores the CID in the Document table.
4. The Server submits a **FundReleaseRequested** event to the blockchain, including the IPFS CIDs of evidence documents.
5. The Server event listener indexes the confirmed on-chain event.
6. ACT-03 receives an in-app notification to review the submission.

### Workflow F — Officer Verification

1. ACT-03 opens the fund release review view for the project.
2. ACT-03 reviews the contractor's submission and evidence documents (accessed via IPFS CIDs).
3. ACT-03 prepares a verification/inspection report document.
4. The Server uploads the verification report to IPFS and stores the CID.
5. ACT-03 submits the verification report with a recommendation (recommend approval / recommend rejection).
6. The Server submits an **OfficerVerified** event to the blockchain, including the verification report IPFS CID and the recommendation outcome.
7. The Server event listener indexes the confirmed on-chain event.
8. ACT-02 receives an in-app notification that a fund release is pending their decision.

### Workflow G — Government Admin Approval or Rejection

1. ACT-02 opens the fund release approval view.
2. ACT-02 reviews the officer's verification report and recommendation, and the contractor's submitted evidence.
3. **If approved:** ACT-02 confirms approval. The Server records the approved fund release in PostgreSQL (financial utilization is updated). The Server submits a **FundReleaseApproved** event to the blockchain. The Server event listener indexes the event. ACT-04 receives an in-app approval notification. AI risk analysis is triggered (see Workflow J).
4. **If rejected:** ACT-02 records a rejection reason. The Server records the rejection in PostgreSQL. The Server submits a **FundReleaseRejected** event to the blockchain (including a hash of the rejection reason). ACT-04 receives an in-app rejection notification with the reason.

### Workflow H — Contractor Resubmission After Rejection

1. ACT-04 receives the rejection notification and views the rejection reason.
2. ACT-04 prepares revised evidence and/or a revised fund release amount.
3. ACT-04 submits a new fund release request against the same milestone, uploading revised evidence to IPFS.
4. The Server submits a new **FundReleaseRequested** event to the blockchain.
5. The previous rejected request remains permanently in the database and blockchain history. It is not deleted or overwritten.
6. The workflow proceeds from Workflow F again.

### Workflow I — Server-Side Missed Milestone Detection

1. The Server runs a periodic scheduled check (frequency to be determined in TRD).
2. For each active milestone, the Server compares: current date vs. milestone due date vs. milestone completion status.
3. If the current date is past the milestone due date and the milestone is not marked complete, the Server:
   a. Marks the milestone as **Missed/Overdue** in the Milestone table in PostgreSQL.
   b. Generates an in-app notification for ACT-03 (assigned officer) and ACT-02 (Government Admin).
4. The Missed/Overdue status is stored in PostgreSQL and used as an AI risk signal input.
5. AI does NOT determine whether a milestone deadline was missed. The Server determines this deterministically.

### Workflow J — AI Risk Analysis

1. AI risk analysis is triggered by the Server after key lifecycle events: fund release approved (Workflow G), officer verification submitted (Workflow F), new milestone defined (Workflow C).
2. The AI module (running within the Server process) loads relevant project data from PostgreSQL: physical progress %, financial utilization %, milestone Missed/Overdue status, fund release history, invoice metadata.
3. The AI runs the hybrid detection approach:
   - Rule-based signals: financial utilization vs. physical progress mismatch, budget overrun trajectory, delay risk, near-duplicate invoice detection.
   - Isolation Forest: unusual spending pattern detection.
4. The AI produces a risk score (0–100 integer) and a set of contributing factors explaining the score.
5. If the risk score exceeds the configured threshold:
   a. An AIFlag record is created in PostgreSQL with the risk score, anomaly type, contributing factors, and explanation.
   b. The Server submits an **AIAnomalyRecorded** event to the blockchain, referencing the flag ID and risk score.
   c. The Auditor (ACT-05) receives an in-app notification of the new flag.
   d. ACT-02 may view a summary of the flag in their project dashboard.
6. If the risk score is below threshold, no flag is created. Analysis results may be logged internally.

### Workflow K — Government Admin Escalation to Auditor

1. ACT-02 reviews an AI flag, overdue milestone alert, or other project concern.
2. ACT-02 selects "Escalate to Auditor" on the relevant project or flag.
3. ACT-02 enters an escalation reason.
4. The Server creates an Escalation record in PostgreSQL, linked to the project and optionally to an AIFlag record.
5. ACT-05 receives an in-app notification of the formal escalation.
6. The Escalation record is a first-class entity in the system; it is queryable and auditable.
7. No email or SMS notification is sent.

### Workflow L — Auditor Investigation

1. ACT-05 views their investigation queue: AI flags (sorted by risk score) and formal Government Admin escalations.
2. ACT-05 selects a flag or escalation to investigate.
3. ACT-05 views: risk score, contributing factors, rule/pattern explanation, linked project data, escalation reason (if applicable).
4. ACT-05 accesses linked IPFS evidence documents (contractor evidence, officer verification reports) directly from the investigation view.
5. ACT-05 views the full on-chain blockchain audit trail for the project.
6. ACT-05 forms a conclusion.

### Workflow M — Auditor Records Audit Finding

1. ACT-05 writes a detailed audit report.
2. The Server uploads the audit report document to IPFS and stores the CID in the AuditFinding record in PostgreSQL.
3. ACT-05 (via connected wallet) submits an **AuditFindingRecorded** event to the blockchain, including: project ID, flag ID, auditor wallet address, finding outcome, IPFS CID of the audit report.
4. The Server event listener indexes the confirmed on-chain event.
5. ACT-05 marks the flag or escalation status: "Reviewed — No Action Required" or "Escalated to External Authorities (outside platform)".
6. ACT-02 receives an in-app notification of the audit finding.

### Workflow N — Project Completion

1. ACT-02 or ACT-03 (with appropriate authorization) marks a project as complete after all milestones are fulfilled.
2. The Server updates the Project record in PostgreSQL to Completed status.
3. The Server submits a **ProjectCompleted** event to the blockchain.
4. The project remains visible to all authenticated actors and on the public Citizen portal in a "Completed" state.

### Workflow O — Citizen Transparency Flow

1. ACT-06 (Citizen) visits the public portal — no login required.
2. ACT-06 browses infrastructure projects, filterable by region and project type.
3. ACT-06 selects a project and views: total budget, amount disbursed, physical progress %, milestone timeline, project status.
4. ACT-06 views the major public fund event timeline (non-sensitive events: project creation, major approval milestones, completion).
5. If the Government Admin has published an audit summary for the project, ACT-06 can view it.
6. ACT-06 cannot access: user account data, internal workflow details, contractor identity, sensitive documents, or internal AI flags.

---

## 7. Functional Requirements

### Priority Key
- **P1** — Must have for MVP. Core demonstration path is broken without this.
- **P2** — Should have for MVP. Important but demo remains viable without it.
- **P3** — Stretch goal. Implement after MVP is stable.

---

### MOD-01: Authentication and RBAC

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-001 | P1 | All authenticated | The platform shall provide a login interface accepting an email/username and password. | Authenticated actors can log in and receive a JWT. | CR-07, CR-20 |
| FR-002 | P1 | All authenticated | The Server shall issue a signed JWT upon successful authentication. The JWT shall encode the user's role. | JWT is issued; subsequent API calls with the token are authorized. | CR-20 |
| FR-003 | P1 | All authenticated | The Server shall validate the JWT on every protected API request. Expired or invalid tokens are rejected. | Requests with invalid tokens return 401. | CR-20 |
| FR-004 | P1 | All authenticated | The Server shall enforce role-based access control on every API endpoint. An actor may not perform actions outside their assigned role regardless of Client-side state. | Cross-role API requests are rejected with 403. | CR-07, G-08 |
| FR-005 | P1 | All authenticated | The platform shall allow authenticated actors to log out, invalidating their session on the Client. | After logout, the actor cannot access protected views without re-authenticating. | CR-07 |
| FR-006 | P1 | ACT-04, ACT-05 | The platform shall support wallet connection (e.g., MetaMask) for actors who perform blockchain-writing operations. Wallet connection is separate from application login. | Actors requiring on-chain writes can connect a wallet. Application auth is independent of wallet connection. | CR-20, AD-15 |

---

### MOD-02: Platform Administration

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-010 | P1 | ACT-01 | The Platform Admin shall be able to create new user accounts, specifying name, email/username, and initial role. | New users can log in with their credentials. | CR-06 |
| FR-011 | P1 | ACT-01 | The Platform Admin shall be able to assign or update the role of any user account. | Role changes take effect on next login (or immediately if JWT is invalidated). | CR-06 |
| FR-012 | P1 | ACT-01 | The Platform Admin shall be able to view a list of all user accounts with their assigned roles. | ACT-01 can view all accounts in the system. | CR-06 |
| FR-013 | P2 | ACT-01 | The Platform Admin shall be able to deactivate a user account, preventing future logins. | Deactivated user receives 401 on next request. | CR-06 |
| FR-014 | P1 | ACT-01 | The Platform Admin dashboard shall provide a summary of system state (total users, total projects, system health indicators). | Dashboard loads with accurate counts. | CR-06 |

---

### MOD-03: Government Project Management

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-020 | P1 | ACT-02 | The Government Admin shall be able to create a new infrastructure project, providing: name, category, region/district, total budget, start date, expected end date. | Project is created in the database. ProjectCreated event is anchored on the blockchain. | CR-01, CR-02 |
| FR-021 | P1 | ACT-02 | The Government Admin shall be able to assign a Department Officer / Engineer (ACT-03) to a project. | ACT-03 can view and manage the assigned project. | CR-01 |
| FR-022 | P1 | ACT-02 | The Government Admin shall view a project list dashboard showing all projects with their status, budget, disbursed amount, physical progress %, AI risk score, and overdue milestone count. | Dashboard renders with accurate data. | CR-01, G-01 |
| FR-023 | P1 | ACT-02 | The Government Admin shall view a project detail page showing financial utilization vs. physical progress, AI risk summary, fund release request queue, and blockchain audit trail. | Project detail page loads with correct data. | CR-01, G-01 |
| FR-024 | P1 | ACT-02, ACT-03 | The platform shall record budget allocation to a project, including the allocated amount and the allocating authority. A BudgetAllocated event shall be anchored on the blockchain. | Budget allocation is stored in the database. BudgetAllocated event appears in the blockchain audit trail. | CR-01, CR-02 |

---

### MOD-04: Budget and Fund Tracking

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-030 | P1 | ACT-02 | The platform shall track each project's total budget, total approved fund releases (financial utilization), and remaining balance. | Financial utilization is calculated and displayed accurately. | CR-01, CR-14 |
| FR-031 | P1 | All authenticated | Financial utilization (%) shall be derived from the cumulative sum of approved fund releases divided by the total project budget. | Financial utilization percentage is accurate to approved amounts. | CR-14 |
| FR-032 | P1 | ACT-02 | Each approved fund release shall update the financial utilization figure in real time (or near-real time after approval). | After approval, updated utilization is visible in the project dashboard. | CR-01, CR-14 |
| FR-033 | P1 | ACT-02, ACT-03 | All rejected fund release requests shall be permanently retained in the database. They shall be visible in the fund release history. They shall never be deleted or overwritten. | Rejected requests appear in fund release history with their rejection reason and timestamp. | CR-16 |
| FR-034 | P1 | ACT-04 | Partial fund releases are supported. A contractor may request an amount less than the full milestone budget portion (e.g., 60% on interim completion). | Partial requests are accepted, approved or rejected, and contribute accurately to financial utilization. | CR-15 |

---

### MOD-05: Milestone Management

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-040 | P1 | ACT-03 | The Department Officer / Engineer shall be able to define project milestones, specifying: name, deliverable description, budget portion, due date. | Milestones are created in the database. MilestoneDefined event is anchored on the blockchain for each milestone. | CR-01, CR-02 |
| FR-041 | P1 | ACT-03 | Each milestone shall have an explicit status tracked in the database: Active, Completed, Missed/Overdue. | Milestone status is visible on the milestone list and project dashboard. | CR-25 |
| FR-042 | P1 | Server | The Server shall run a periodic scheduled check to compare each active milestone's due date against the current date and the milestone's completion status. | Scheduled check runs and marks overdue milestones. | CR-25 |
| FR-043 | P1 | Server | If the current date is past a milestone's due date and the milestone is not Completed, the Server shall mark it as Missed/Overdue in the database. | Missed/Overdue status appears on the milestone and project dashboard. | CR-25 |
| FR-044 | P1 | Server | When a milestone is marked Missed/Overdue, the Server shall generate in-app notifications for the assigned ACT-03 and the project's ACT-02. | Both ACT-02 and ACT-03 receive notifications. | CR-25, FR-043 |
| FR-045 | P1 | ACT-03 | The milestone list view shall display all milestones for a project with their status, due date, budget portion, and physical progress. | Milestone list renders with correct statuses. | CR-01 |

---

### MOD-06: Contractor Fund Release Workflow

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-050 | P1 | ACT-04 | The Contractor shall be able to submit a fund release request against a specific milestone, specifying the requested amount. | Fund release request is created in the database in Pending status. | CR-01, CR-15 |
| FR-051 | P1 | ACT-04 | The Contractor shall be able to upload supporting evidence documents (invoices, bills, site photographs) as part of a fund release request submission. | Documents are uploaded to IPFS via the Server; CIDs are stored in the Document table. | CR-03 |
| FR-052 | P1 | Server | Upon fund release request submission, the Server shall submit a FundReleaseRequested event to the blockchain, including the IPFS CIDs of all submitted evidence documents. | FundReleaseRequested event appears in the blockchain audit trail with correct CIDs. | CR-02, CR-03 |
| FR-053 | P1 | ACT-04 | The Contractor shall be able to monitor the status of their fund release requests (Pending / Under Review / Approved / Rejected). | Request status is visible and updates when changed by ACT-03 or ACT-02. | CR-01 |
| FR-054 | P1 | ACT-04 | After a rejection, the Contractor shall be able to submit a new fund release request against the same milestone with revised evidence. | New request is created; prior rejected request remains permanently in the history. | CR-16 |
| FR-055 | P1 | ACT-04 | The Contractor shall receive an in-app notification when their request is approved or rejected, including the rejection reason if rejected. | Notification is delivered with accurate status and reason. | CR-16 |

---

### MOD-07: Physical Progress Tracking

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-060 | P1 | ACT-03 | The Department Officer / Engineer shall be able to record a physical progress update (%) for a specific milestone. | Progress update is stored in the database with a timestamp and attributed to ACT-03. | CR-14 |
| FR-061 | P1 | All authenticated | Physical progress (%) shall be displayed alongside financial utilization (%) in all project and milestone views. These are explicitly distinct data fields. | Both fields are visible and clearly labeled as separate metrics. | CR-14, G-09 |
| FR-062 | P1 | ACT-03 | Physical progress history shall be retained over time, enabling a historical view of progress for each milestone. | Progress history is viewable per milestone. | CR-14 |
| FR-063 | P2 | ACT-02, ACT-05 | A chart comparing physical progress % vs financial utilization % over time shall be available in the project detail view. | Chart renders with correct historical data. | G-09 |

---

### MOD-08: Evidence and Document Management

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-070 | P1 | ACT-04, ACT-03, ACT-05 | The Server shall handle all IPFS document uploads. The Client shall not upload directly to IPFS. | All uploads pass through Server API routes. | CR-03, AD-10 |
| FR-071 | P1 | ACT-04, ACT-03, ACT-05 | The IPFS CID of each uploaded document shall be stored in the Document table in PostgreSQL, linked to its parent entity (fund release request, verification report, or audit finding). | CIDs are retrievable and linked to the correct parent records. | CR-03 |
| FR-072 | P1 | All authenticated | Each document associated with a fund release, verification, or audit finding shall be viewable from within the platform via a document viewer that resolves the IPFS CID. | Documents open in the in-browser document viewer using the IPFS CID. | CR-03 |
| FR-073 | P1 | ACT-05 | The Auditor investigation view shall provide direct access to all IPFS evidence linked to a flag or escalation without needing to navigate separately. | Evidence documents load directly from the investigation view. | CR-03 |
| FR-074 | P1 | All | The platform shall document the IPFS privacy limitation clearly: documents are unencrypted; anyone with a CID can access the document via a public IPFS gateway. | The limitation is displayed to users where relevant and documented in system docs. | CR-23 |

---

### MOD-09: Blockchain Audit Trail

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-080 | P1 | Server | The Server shall submit the appropriate on-chain event for each key lifecycle action (see the 10 confirmed event types: ProjectCreated, BudgetAllocated, MilestoneDefined, FundReleaseRequested, OfficerVerified, FundReleaseApproved, FundReleaseRejected, AIAnomalyRecorded, AuditFindingRecorded, ProjectCompleted). | Each lifecycle action results in a confirmed on-chain event. | CR-02 |
| FR-081 | P1 | Server | The Server shall run a blockchain event listener that indexes confirmed on-chain events into the BlockchainEvent table in PostgreSQL. | On-chain events are queryable from the PostgreSQL index within a reasonable time after confirmation. | CR-18 |
| FR-082 | P1 | ACT-02, ACT-03, ACT-04, ACT-05 | The Client shall display a blockchain audit trail view for each project, showing all indexed on-chain events in chronological order with their event type, key data, and blockchain timestamp. | Audit trail view renders all on-chain events for the project. | CR-02, G-02 |
| FR-083 | P1 | All authenticated | The blockchain audit trail shall serve as the tamper-evident record. Users may verify event integrity by referencing the blockchain directly if desired. | The audit trail is accessible and the underlying blockchain transaction hash is visible. | CR-02, G-02 |
| FR-084 | P1 | Server | The Solidity contracts shall use BOTH event emissions (for audit history) and minimal persistent contract state (for Server-side validation of transactions, such as checking project status before accepting new events). | Contract state validations prevent invalid on-chain transactions. Emitted events provide the audit history. | CR-27 |
| FR-085 | P1 | All | The platform shall function fully on a local Hardhat network. Ethereum Sepolia testnet connectivity is a stretch goal and not required for the primary demo. | All 10 event types are demonstrable on a local Hardhat node without internet connectivity. | CR-19 |

---

### MOD-10: AI Risk and Anomaly Detection

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-090 | P1 | Server | The AI module shall run within the Server process. No separate inference service or microservice shall be created. | AI analysis executes when triggered; no external inference service is required. | CR-21 |
| FR-091 | P1 | Server | AI risk analysis shall be triggered automatically by the Server after key lifecycle events: fund release approved, officer verification submitted, new milestone defined. | Analysis is triggered without manual action after each eligible event. | CR-21 |
| FR-092 | P1 | Server | The AI module shall compute a risk score (0–100 integer) for each analyzed project, incorporating rule-based signals and the Isolation Forest model. | Risk scores are produced and stored in the AIFlag record. | CR-04 |
| FR-093 | P1 | Server | The AI module shall produce contributing factor explanations alongside each risk score, identifying which signals drove the score (e.g., "Financial utilization: 82% vs Physical progress: 25%"). | Contributing factors are stored and displayable in the Auditor investigation view. | CR-13 |
| FR-094 | P1 | Server | The AI rule-based signals shall include at minimum: financial utilization vs. physical progress mismatch, budget overrun trajectory, project delay risk (using Server-determined Missed/Overdue milestone status as input), and near-duplicate invoice detection. | Each rule signal is identifiable in contributing factors when triggered. | CR-04, CR-25 |
| FR-095 | P1 | Server | The Isolation Forest model shall be used for unsupervised anomaly detection on spending patterns (transaction amounts, timing, contractor patterns). | Isolation Forest produces anomaly scores that contribute to the overall risk score. | CR-04 |
| FR-096 | P1 | Server | When a project's risk score exceeds the configured threshold, an AIFlag record shall be created in PostgreSQL and an AIAnomalyRecorded event shall be anchored on the blockchain. | AIFlag is created in the database and AIAnomalyRecorded event is visible in the blockchain audit trail. | CR-02, CR-04 |
| FR-097 | P1 | ACT-05 | The Auditor's investigation view shall display the AI flag queue sorted by risk score (highest first), with each flag showing: project name, risk score, anomaly type, contributing factors, and flag timestamp. | Queue renders correctly sorted with the required information. | CR-04, CR-13 |
| FR-098 | P1 | All | The platform shall consistently display the disclaimer: "AI risk flags indicate anomalous patterns and do not establish fraud or corruption." This disclaimer shall appear wherever AI risk results are presented. | Disclaimer is visible on all AI flag and risk score views. | CR-04, G-05 |
| FR-099 | P2 | Server | The AI model shall be trained/validated on synthetic or semi-synthetic project data. Validation metrics (precision, recall, F1-score, false-positive rate) shall be computed on the validation split and documented. | Validation metrics are available in the project's AI/ML documentation and/or viva materials. | CR-22, CR-28 |

---

### MOD-11: Auditor Investigation

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-100 | P1 | ACT-05 | The Auditor shall have a dedicated investigation queue showing AI flags and formal Government Admin escalations. | Queue renders with both AI flags and escalations, sorted and distinguishable. | CR-04, CR-26 |
| FR-101 | P1 | ACT-05 | The Auditor shall be able to view the full investigation detail for a flag or escalation: risk score, contributing factors, associated project data, escalation reason (if applicable), and linked IPFS evidence. | Investigation detail view renders with all required information. | CR-04, CR-26 |
| FR-102 | P1 | ACT-05 | The Auditor shall be able to access the blockchain audit trail directly from the investigation view. | Audit trail for the related project loads from the investigation view. | CR-02, G-05 |
| FR-103 | P1 | ACT-05 | The Auditor shall be able to write an audit report, which is uploaded to IPFS by the Server. | Audit report is uploaded; CID is stored in the AuditFinding record. | CR-17 |
| FR-104 | P1 | ACT-05 | The Auditor shall be able to submit an AuditFindingRecorded event on the blockchain (via connected wallet), referencing the project ID, flag ID, finding outcome, and IPFS CID of the audit report. | AuditFindingRecorded event appears in the blockchain audit trail with the correct CID. | CR-17, CR-02 |
| FR-105 | P1 | ACT-05 | The Auditor shall be able to mark a flag or escalation as "Reviewed — No Action Required" or "Escalated to External Authorities". | Status is updated in the database and visible on the flag/escalation record. | CR-17 |

---

### MOD-12: Government Admin → Auditor Escalation

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-110 | P1 | ACT-02 | The Government Admin shall be able to formally escalate a project concern to an Auditor from the project detail view or AI flag view. | Escalation action is available and triggers the escalation workflow. | CR-26 |
| FR-111 | P1 | ACT-02 | The escalation workflow shall require the Government Admin to enter an escalation reason before submitting. | Submission is blocked without a reason; reason is stored in the Escalation record. | CR-26 |
| FR-112 | P1 | Server | The Server shall create an Escalation record in PostgreSQL upon submission, linking it to the project and optionally to a specific AIFlag record. | Escalation record is created and queryable. | CR-26 |
| FR-113 | P1 | ACT-05 | The Auditor shall receive an in-app notification when a new formal escalation is assigned to them. | ACT-05 receives the notification and can view the escalation in their investigation queue. | CR-26 |
| FR-114 | P1 | ACT-02, ACT-05 | The Escalation record shall be a permanent, queryable entity in the system. It shall not be deleted. | Escalation history is viewable for audit purposes. | CR-26 |

---

### MOD-13: Notifications

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-120 | P1 | All authenticated | The platform shall deliver in-app notifications to relevant actors for the following events: fund release request approved, fund release request rejected (with reason), new AI flag generated, milestone marked Missed/Overdue, new formal escalation received, audit finding recorded. | Each notification type is delivered to the correct actor(s). | CR-25, CR-26, PR-01 |
| FR-121 | P1 | All authenticated | The in-app notification centre shall list all unread and recent notifications with a timestamp, event type, and a link to the relevant project or record. | Notification centre renders with the required information. | PR-01 |
| FR-122 | P1 | All authenticated | Notifications shall be marked as read when the actor views them. | Read status is persisted and reflected in the notification centre. | PR-01 |
| FR-123 | P1 | All | No email or SMS notification infrastructure shall be built for MVP. | Email/SMS are absent from the notification system. | Non-goal |

---

### MOD-14: Citizen Transparency Portal

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-130 | P1 | ACT-06 | The Citizen portal shall be accessible without registration or login. | Portal loads and displays projects without requiring authentication. | CR-07 |
| FR-131 | P1 | ACT-06 | The Citizen portal shall display a list of all active and completed infrastructure projects, filterable by region and project category. | Project list renders; filters function correctly. | G-06 |
| FR-132 | P1 | ACT-06 | Each project in the Citizen portal shall display: project name, category, region, total budget, total amount disbursed, physical progress %, and current status (Active / Completed). | Project summary card renders with the listed information. | G-06 |
| FR-133 | P1 | ACT-06 | Each project shall have a public fund event timeline showing major non-sensitive events: project creation, major approvals, project completion. | Fund event timeline renders with correct events. | G-06 |
| FR-134 | P2 | ACT-06 | Administrator-published audit summaries shall be visible on the project's public page when made available by ACT-02. | Published summaries appear on the public project page. | G-06 |
| FR-135 | P1 | All | The Citizen portal shall never expose: user account data, internal workflow details, contractor identity, raw IPFS document links, internal AI flag details, or private fund release details. | None of the listed sensitive data is accessible from the public portal API endpoints or Client views. | CR-07 |

---

### MOD-15: Project and Audit Reporting

| FR ID | Priority | Actor(s) | Requirement | Acceptance Outcome | Traces To |
|---|---|---|---|---|---|
| FR-140 | P1 | ACT-02, ACT-05 | The project detail view shall include a chart comparing financial utilization % vs. physical progress % over the project timeline. | Chart renders with accurate historical data. | CR-14, G-09 |
| FR-141 | P2 | ACT-05 | A risk score history chart shall be available per project, showing how the AI risk score has evolved over time as fund lifecycle events occurred. | Risk score history chart renders with correct historical data points. | PR-02 |
| FR-142 | P1 | ACT-02, ACT-05 | The project detail view shall display a summary of all AI flags for the project: flag count, highest risk score, flag status. | AI flag summary is visible and accurate. | CR-04 |
| FR-143 | P1 | ACT-02, ACT-05 | The Auditor audit finding history shall be viewable per project, showing all recorded findings with their outcome and IPFS CID. | Audit finding history renders correctly. | CR-17 |
| FR-144 | P3 | ACT-02, ACT-05 | The platform shall support PDF export of audit reports (stretch goal). | Not required for MVP; implement only after core MVP is stable. | PR-04 |

---

## 8. Non-Functional Requirements

### NFR-01 — Security: Authentication
The Server shall issue signed JWTs using an industry-standard signing algorithm (e.g., HS256 or RS256). Token expiry shall be enforced. Tokens shall not contain sensitive plaintext data beyond what is needed for authorization.
*Traces to: CR-20*

### NFR-02 — Security: Authorization
Role-based access control shall be enforced at the Server API layer for all protected endpoints. Client-side access guards are for UX only and are not treated as a security boundary.
*Traces to: CR-07, G-08*

### NFR-03 — Security: Password Storage
User passwords shall be stored as cryptographic hashes using a strong hashing function (e.g., bcrypt). Plaintext passwords shall never be stored or logged.
*Traces to: CR-07*

### NFR-04 — Security: Input Validation
The Server shall validate and sanitize all input data received from the Client. Malformed or unexpected inputs shall be rejected with appropriate error responses.
*Traces to: CR-08*

### NFR-05 — Security: IPFS Document Privacy Limitation
The platform shall document that IPFS documents are unencrypted. The Client and Server documentation shall clearly state this limitation. This is an accepted and intentional constraint of this academic project version.
*Traces to: CR-23*

### NFR-06 — Data Integrity: Blockchain Immutability
On-chain events shall not be modifiable after confirmation. The blockchain provides the tamper-evident guarantee. The Server-side indexed copy in PostgreSQL shall not be presented as the authoritative tamper-evident record; the blockchain is the authoritative source.
*Traces to: CR-02, G-02*

### NFR-07 — Data Integrity: Fund Release Records
All fund release request records (including rejected requests) shall be retained permanently in the database. No fund release record shall be deleted or overwritten.
*Traces to: CR-16*

### NFR-08 — Data Integrity: Blockchain Event Consistency
The Server event listener shall maintain consistency between the blockchain event log and the PostgreSQL BlockchainEvent index. Re-indexing mechanisms should be considered in the TRD for recovery from listener failures.
*Traces to: CR-18*

### NFR-09 — Performance: Page Load
All primary authenticated dashboard views (project list, project detail, Auditor queue) shall exhibit reasonable responsiveness expectations under representative demo conditions with representative demo data (≤50 projects, ≤500 fund release events).
*Traces to: CR-08*

### NFR-10 — Performance: AI Analysis Latency
AI risk analysis triggered by a lifecycle event shall exhibit reasonable responsiveness expectations under representative demo conditions. Exact async/background execution mechanism shall be deferred to the TRD/System Architecture. Longer-running analysis shall not block the Server's response to the triggering API call.
*Traces to: CR-21*

### NFR-11 — Usability: Role-Specific UX
Each actor shall see only the navigation and views relevant to their role. Irrelevant views shall not appear in the navigation. Unauthorized actions shall be unavailable or clearly disabled in the UI.
*Traces to: CR-07, G-08*

### NFR-12 — Usability: UI States
All interactive views shall implement loading, empty, error, and success states. No view shall present a blank screen or silent failure on API errors.
*Traces to: CR-09*

### NFR-13 — Usability: Form Validation
All input forms shall validate required fields before submission and display clear, labelled error messages adjacent to invalid fields. Submission shall be disabled while a request is in-flight.
*Traces to: CR-09*

### NFR-14 — Usability: Responsiveness
The Client shall function correctly on standard desktop screen sizes (≥1280px wide). Mobile layout is a best-effort bonus and is not required for MVP.
*Traces to: CR-09*

### NFR-15 — Accessibility: Basic Standards
The Client shall use semantic HTML elements, appropriate ARIA labels on interactive controls, and a minimum colour contrast ratio of 4.5:1 for standard text. Full WCAG 2.1 AA compliance is a best-effort target.
*Traces to: CR-09*

### NFR-16 — Maintainability: Code Documentation
All non-trivial Server functions, API routes, and AI model inference code shall be documented with concise inline comments or docstrings. Client components shall be self-documenting where possible.
*Traces to: CR-08*

### NFR-17 — Maintainability: Monorepo Structure
The repository shall maintain the agreed monorepo structure: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/`. New directories shall not be created without a documented rationale.
*Traces to: CR-24*

### NFR-18 — Reliability: Error Handling
The Server shall handle expected error conditions (invalid input, resource not found, unauthorized access, blockchain transaction failure, IPFS upload failure) and return structured error responses. Unhandled exceptions shall not expose stack traces to the Client.
*Traces to: CR-08*

### NFR-19 — Reliability: Blockchain Transaction Failures
If a blockchain event submission fails (e.g., Hardhat node is unavailable), the Server shall log the failure and return an appropriate error to the Client without corrupting the database state. Retry or recovery strategy shall be defined in the TRD.
*Traces to: CR-02, CR-19*

### NFR-20 — Demonstrability: Local Demo Environment
The complete platform — Client, Server, PostgreSQL database, local Hardhat network — shall be operable on a single developer laptop without external network dependencies (except for Pinata IPFS API calls, which may require internet). A local IPFS fallback should be considered for viva resilience.
*Traces to: CR-19, G-07*

### NFR-21 — Demonstrability: Demo Data
The `data/` directory shall contain synthetic demo data that covers representative scenarios: normal projects, anomalous projects designed to trigger AI flags, missed milestones, and completed projects. This data shall enable a convincing end-to-end demonstration.
*Traces to: CR-22, G-07*

---

## 9. AI Product Requirements

### 9.1 Purpose and Limitations

The AI/ML component's role is to identify anomalous or high-risk patterns in project and fund data that may warrant human investigation. The AI does NOT determine guilt, wrongdoing, fraud, or corruption. All final audit conclusions are made by humans (Auditors).

**Mandatory disclaimer (to appear wherever AI results are presented):** *"AI risk flags indicate anomalous patterns and do not establish fraud or corruption."*

### 9.2 Detection Capabilities

The AI module shall implement the following detection capabilities at minimum:

| Capability | Approach |
|---|---|
| Financial utilization vs. physical progress mismatch | Rule-based threshold: flag when financial utilization exceeds physical progress by a configurable margin. |
| Budget overrun risk | Trajectory rule: project the current spend rate vs. remaining budget and timeline. |
| Project delay risk | Date-based rule using Server-determined Missed/Overdue milestone status as an input feature. |
| Near-duplicate invoice detection | Similarity scoring on invoice metadata (amount, vendor, date, project). |
| Unusual spending patterns | Isolation Forest: unsupervised anomaly detection on transaction amounts and timing. |

XGBoost for overall risk classification is a stretch goal, contingent on data quality and validation outcomes.

### 9.3 Execution Model

- AI analysis is **event-triggered**, not nightly batch.
- Triggers: fund release approved, officer verification submitted, new milestone defined.
- The AI module runs **within the Server process**. No separate inference service or microservice.
- AI analysis shall be non-blocking with respect to the triggering HTTP response where processing time warrants.

### 9.4 Risk Score and Explainability

- Risk scores shall be expressed as integers in the range 0–100.
- Each scored project shall have associated contributing factors describing which signals drove the score.
- Explainability shall include: the rule or model signal, the feature values, and a human-readable description of the anomaly.
- SHAP values may be used for Isolation Forest explainability if feasible; rule-based explanations are sufficient for deterministic signals.

### 9.5 Data Strategy

- AI shall be trained and validated on **synthetic or semi-synthetic** project and fund data.
- Synthetic data shall include both normal scenarios and intentionally anomalous scenarios.
- This limitation (no real government data) shall be documented in the AI/ML Design document and acknowledged in the viva.

### 9.6 Calibration and Reporting

- No fixed false-positive rate is promised.
- Validation-based threshold calibration shall be used: train, validate on a held-out split, tune thresholds, report metrics.
- Metrics to report on the synthetic validation set where meaningful: precision, recall, F1-score, false-positive rate.
- Thresholds shall not be hard-coded to satisfy an arbitrary target; they shall be evidence-based from the validation process.

### 9.7 On-Chain Anchoring of Flags

- When a risk score exceeds the threshold, an **AIAnomalyRecorded** event shall be anchored on the blockchain, containing: project ID, flag ID, risk score (0–100 integer), anomaly type identifier, and timestamp.
- The full AI flag details (contributing factors, feature values) are stored in PostgreSQL, not on-chain.

---

## 10. Blockchain Product Requirements

### 10.1 Purpose

The blockchain provides a **tamper-evident, append-only audit trail** of important fund lifecycle events. It does not replace PostgreSQL. It does not store every data field. Its role is anchoring: recording that a specific event occurred at a specific time with specific identifiers, in a way that cannot be silently altered.

### 10.2 Confirmed On-Chain Event Types

All ten confirmed event types shall be emitted by the Solidity contracts:

| Event | Purpose |
|---|---|
| ProjectCreated | Anchors project creation. |
| BudgetAllocated | Anchors budget allocation. |
| MilestoneDefined | Anchors milestone creation. |
| FundReleaseRequested | Anchors contractor submission with IPFS CIDs. |
| OfficerVerified | Anchors officer verification with IPFS CID. |
| FundReleaseApproved | Anchors approval. |
| FundReleaseRejected | Anchors rejection with reason hash. |
| AIAnomalyRecorded | Anchors AI flag with risk score. |
| AuditFindingRecorded | Anchors auditor finding with IPFS CID. |
| ProjectCompleted | Anchors project completion. |

### 10.3 Contract Design

- **Solidity events** → immutable, tamper-evident audit history. All 10 event types above shall use Solidity events.
- **Minimal persistent contract state** → used only for Server-side validation (e.g., confirming a project exists and is Active before accepting new events). Do NOT duplicate the PostgreSQL database on-chain.
- Exact Solidity structs, mappings, function signatures, and event parameters shall be deferred to the TRD and System Architecture.

### 10.4 Server-Side Event Indexing

- The Server shall maintain a blockchain event listener that subscribes to on-chain events.
- Confirmed events shall be indexed into the BlockchainEvent table in PostgreSQL.
- The Client shall query the blockchain audit trail via the Server API (indexed copy), not by directly calling the blockchain node for normal views.

### 10.5 Wallet Requirements

- Wallet connection (e.g., MetaMask) is required only for actors who perform blockchain-writing operations.
- Application login (JWT) is independent of wallet connection.
- The platform shall be fully usable by ACT-01, ACT-02, ACT-03, and ACT-06 without a connected wallet for most functions.
- ACT-04 (Contractor) and ACT-05 (Auditor) require a connected wallet when submitting on-chain events (fund release submission and audit finding recording, respectively).

### 10.6 Network

- **Primary demo environment:** Local Hardhat network. All 10 event types must be demonstrable on a local Hardhat node.
- **Stretch:** Ethereum Sepolia testnet deployment. Not required for the primary demo.
- The platform must not depend on an external public testnet being available at viva time.

---

## 11. IPFS Product Requirements

### 11.1 Purpose

IPFS (via the Pinata API) provides off-chain decentralized storage for project evidence documents. This ensures documents cannot be silently deleted or altered by the platform operator after they are pinned, while avoiding storing large files directly on-chain.

### 11.2 Document Upload

- The **Server** handles all IPFS document uploads. The Client does not upload directly to Pinata or any IPFS node.
- Upon successful upload, the Server receives the IPFS CID from Pinata and stores it in the Document table in PostgreSQL.
- The CID is included in the relevant on-chain event (e.g., FundReleaseRequested, OfficerVerified, AuditFindingRecorded).

### 11.3 Document Types

Documents stored on IPFS include: contractor invoices, site photographs, bills of materials, progress reports, officer inspection/verification reports, and auditor audit reports.

### 11.4 Document Retrieval

- Documents are retrieved by the Client using their IPFS CID, typically via a public IPFS gateway URL or Pinata gateway URL.
- The document viewer in the Client shall support in-browser viewing of common document types (PDF, images).

### 11.5 Privacy Limitation (Documented)

Documents uploaded to IPFS are **unencrypted** in this academic project version. Any person who obtains a CID can access the document via a public IPFS gateway. This limitation shall be:
- Clearly documented in the Technical Requirements Document (TRD) and System Architecture document.
- Acknowledged in viva presentation materials.
- Displayed as a notice in the Client where relevant (e.g., on the evidence upload screen).

In a production system, sensitive contractor documents would require encryption and/or access-controlled storage prior to IPFS upload. This is out of scope for this project version.

### 11.6 Pinning and Retention

- Pinata is used for managed pinning. Documents shall remain pinned for the lifetime of the demo.
- A formal document retention or lifecycle management policy is out of scope for this version.
- The Pinata free tier is assumed to be sufficient for demo volumes; this is an acknowledged assumption.

---

## 12. Citizen Transparency Requirements

### 12.1 Publicly Visible Data

The following data shall be accessible on the public Citizen portal without authentication:

- Project name, category, region/district.
- Project status (Active / Completed).
- Total budget and total amount disbursed (financial utilization amount).
- Physical progress % (current value, not historical breakdown).
- Milestone names and due dates (not detailed deliverable descriptions or contractor details).
- Major public fund event timeline: project creation, key approval milestones, project completion.
- Administrator-published audit summaries (where explicitly published by ACT-02).

### 12.2 Sensitive Data — Not Publicly Exposed

The following data shall **never** be exposed on the public Citizen portal:

- User account data (names, email addresses, credentials).
- Contractor identity and contact details.
- Internal fund release request details (amounts, submission documents, rejection reasons).
- IPFS document links for contractor evidence or officer reports.
- AI flag details, risk scores, and contributing factors.
- Auditor investigation details.
- Internal workflow state (e.g., pending approval queue).
- Blockchain transaction details beyond what is already public on-chain.

### 12.3 Public Portal Access

- The public portal shall be accessible via the main platform URL without any authentication step.
- No citizen registration, login, or data collection is required.
- The public portal shall be clearly separated from the authenticated application in Client routing.

---

## 13. Notification Requirements

### 13.1 MVP Notification Mechanism

All notifications are **in-app only**. No email, SMS, push notification, or external messaging infrastructure is required for MVP.

### 13.2 Notification Events

| Event | Recipient(s) |
|---|---|
| Fund release request approved | ACT-04 (Contractor who submitted) |
| Fund release request rejected (with reason) | ACT-04 (Contractor who submitted) |
| Fund release request submitted (new review needed) | ACT-03 (Assigned officer) |
| Officer verification submitted (pending admin decision) | ACT-02 (Government Admin for that project) |
| Milestone marked Missed/Overdue | ACT-03 (Assigned officer), ACT-02 (Government Admin) |
| New AI flag created | ACT-05 (Auditor) |
| Formal escalation received | ACT-05 (Auditor) |
| Audit finding recorded | ACT-02 (Government Admin for that project) |

### 13.3 Notification Centre

- Each authenticated actor shall have a notification centre accessible from the primary navigation.
- The notification centre shall display unread and recent notifications, each with: event type, brief description, timestamp, and a link to the relevant project or record.
- Notifications shall be marked as read upon the actor viewing them.
- The unread count shall be visible as a badge on the notification centre icon.

---

## 14. UI/UX Product Requirements

### 14.1 Client Technology

The Client is built with React + Vite, styled with Tailwind CSS, and uses shadcn/ui as the component library. A custom design system is built on top of shadcn/ui to ensure visual consistency and a premium look.

### 14.2 Design Quality

The Client must look **professional, modern, and premium**. It is not a bare-bones admin panel. Evaluators, viva panels, and potential demonstrators should be impressed by the visual quality at first interaction. A low-quality UI that does not match the technical ambition of the project is not acceptable.

Design principles:
- **Dashboard-oriented:** Role-specific dashboards with KPIs, data visualizations, and structured information hierarchy.
- **Curated color palette:** Custom design tokens — not raw Tailwind defaults. Separate light and dark mode tokens.
- **Professional typography:** Font stack using Inter, Plus Jakarta Sans, or equivalent high-quality typeface.
- **Spatial rhythm:** Consistent spacing, padding, and grid alignment throughout.
- **Clear visual hierarchy:** Primary actions, secondary information, and contextual data are visually distinguished.

### 14.3 Dark Mode

The Client shall support **light mode**, **dark mode**, and **system/OS preference** detection.

Dark mode shall be implemented via Tailwind CSS + shadcn/ui design tokens. Dark mode is a UI/UX feature; it does not require separate screen implementations. Both light and dark mode must be visually polished; dark mode is not an afterthought.

### 14.4 Role-Specific Dashboards

Each actor has a distinct primary dashboard appropriate to their role. Actors shall not see navigation items or views that are irrelevant to their role.

| Actor | Primary Dashboard Content |
|---|---|
| ACT-01 | User list, role management, system indicators. |
| ACT-02 | Project portfolio overview: KPIs (active projects, total budget, disbursed, AI flag count, overdue milestones), approval queue, escalation actions. |
| ACT-03 | Assigned project list, milestone status overview, pending review queue, overdue alerts. |
| ACT-04 | Assigned milestones, fund release request status, notification centre. |
| ACT-05 | AI flag queue (sorted by risk score), escalation queue, audit finding history. |
| ACT-06 | Public project list (no dashboard; transparent portal). |

### 14.5 Key Views

The Client shall implement the following key views:

- Login screen.
- Role-specific dashboard (for each of ACT-01 through ACT-05).
- Project list view (with filters and sorting).
- Project detail view (financial utilization vs. physical progress chart, milestone list, fund release history, AI risk summary, blockchain audit trail).
- Milestone management view (ACT-03).
- Fund release submission form (ACT-04, with document upload).
- Fund release review and verification view (ACT-03).
- Fund release approval/rejection view (ACT-02).
- Escalation submission view (ACT-02).
- Auditor investigation queue (ACT-05).
- Auditor investigation detail view (ACT-05) — risk score, contributing factors, evidence, audit trail, audit finding form.
- Blockchain audit trail view (event timeline per project).
- Document viewer (in-browser IPFS document preview).
- Notification centre.
- User management view (ACT-01).
- Public Citizen project list and project detail (ACT-06).

### 14.6 Charts and Data Visualisation

- Financial utilization % vs. physical progress % over time: line or area chart on the project detail view.
- Risk score history per project (P2): line chart in the project/Auditor view.
- Budget breakdown KPI cards: total budget, disbursed, remaining.
- Milestone timeline: visual representation of milestones with due dates and completion status.

### 14.7 Tables

- Project list: sortable, filterable table with status badges, progress indicators, and risk score indicators.
- Fund release request history: sortable table with status, amount, date, and linked documents.
- AI flag queue: sortable by risk score, filterable by status and anomaly type.
- User management table: filterable by role.

### 14.8 Forms

- Project creation form.
- Milestone definition form.
- Fund release request form (with evidence upload).
- Verification/inspection report form (with report upload).
- Escalation form (reason required).
- Audit finding form (with report upload).
- User creation/edit form (ACT-01).

All forms shall have: client-side validation before submission, field-level error messages, submission loading state (disable submit button while in-flight), and success/error feedback.

### 14.9 UI States

All interactive views shall implement: **loading state** (skeleton or spinner), **empty state** (descriptive message, no blank screen), **error state** (clear error message with a recovery action), and **success state** (confirmation feedback after mutations).

---

## 15. Data and Evidence Requirements

### 15.1 Operational Data (PostgreSQL)

The following data entities are required at the product level (schema definition is a TRD and System Architecture responsibility):

| Entity | Description |
|---|---|
| User | All authenticated users, hashed credentials, role. |
| Project | Infrastructure project metadata: name, category, region, budget, status, dates, assigned officer. |
| Milestone | Project milestones: name, deliverable, budget portion, due date, status (Active / Completed / Missed/Overdue), physical progress %. |
| FundRelease | Fund release requests: all versions (including rejected), amount, status, timestamps, linked milestone, submission history. |
| Document | IPFS document references: CID, document type, uploader, timestamp, linked entity. |
| BlockchainEvent | Server-indexed on-chain events: event type, project/entity ID, blockchain transaction hash, block number, timestamp, key data fields. |
| AIFlag | AI-generated anomaly flags: risk score, anomaly type, contributing factors, explanation, status, creation timestamp, linked project. |
| AuditFinding | Auditor findings linked to AI flags or escalations: outcome, IPFS audit report CID, timestamp. |
| ProjectProgress | Physical progress records per milestone: progress %, timestamp, recorded by (ACT-03). |
| Escalation | Government Admin → Auditor escalations: reason, linked project, linked AIFlag (optional), status, timestamp. |
| Notification | In-app notification queue: event type, message, recipient, read status, timestamp, link target. |

### 15.2 Financial Data

- Approved fund release amounts are the source of financial utilization.
- Financial utilization % is derived at query time from approved fund releases, not a stored field (or if stored, it must be consistent with the fund release records).
- All fund release events (requests, approvals, rejections) contribute to the permanent audit record.

### 15.3 AI Data

- AI feature vectors and intermediate computation data are stored in PostgreSQL (or within the `ml/` layer for training artifacts).
- Trained model artifacts (Isolation Forest model file, scaler, thresholds) are stored in the `ml/` directory and loaded into the Server process at startup.
- Synthetic training and validation data is stored in the `data/` directory.

### 15.4 Blockchain Event Data

- On-chain events are the authoritative tamper-evident record.
- The BlockchainEvent table in PostgreSQL is a queryable index for Client views, not a replacement for the blockchain itself.

### 15.5 Evidence Documents

- All supporting documents uploaded to IPFS are referenced by CID in the Document table.
- CIDs are included in the relevant on-chain events.
- Document type categorization (invoice, photograph, bill, report, audit report) is stored in the Document entity.

---

## 16. Scope and Prioritization

### 16.1 MVP

The MVP must demonstrate the complete fund lifecycle end-to-end across all four technical pillars. A partial demonstration that covers only 2–3 pillars is insufficient.

**MVP scope:**
- Authentication and RBAC (all 6 actors, JWT).
- Platform Admin user management.
- Project creation with blockchain anchoring (ProjectCreated, BudgetAllocated).
- Milestone definition with blockchain anchoring (MilestoneDefined).
- Physical progress recording.
- Contractor fund release request with IPFS evidence upload (FundReleaseRequested).
- Officer verification with IPFS report upload (OfficerVerified).
- Government Admin fund release approval/rejection (FundReleaseApproved, FundReleaseRejected).
- Contractor resubmission of rejected requests.
- Server-side missed milestone detection (Missed/Overdue status, in-app alerts).
- AI risk analysis: rule-based signals + Isolation Forest (event-triggered, within Server).
- AI flag creation with blockchain anchoring (AIAnomalyRecorded).
- AI flag displayed in Auditor queue with risk score and contributing factors.
- Government Admin → Auditor formal escalation (in-system, in-app notification).
- Auditor investigation view (flag queue, detail, evidence access, audit trail).
- Auditor audit finding with IPFS report upload and blockchain anchoring (AuditFindingRecorded).
- Project completion with blockchain anchoring (ProjectCompleted).
- In-app notifications for all key events.
- Citizen public portal (project list, project summary, event timeline).
- Blockchain audit trail view (all 10 event types).
- Client: role-specific dashboards, charts, tables, forms, all UI states.
- Client: light mode and dark mode.
- Synthetic demo dataset covering normal and anomalous scenarios.

### 16.2 Stretch Goals

| Stretch Goal | Rationale |
|---|---|
| Ethereum Sepolia testnet deployment | If local demo is solid, Sepolia adds additional credibility. Not required for primary viva. |
| XGBoost risk classifier | If synthetic data quality is sufficient for meaningful training. |
| Risk score history chart per project | Useful for trend visibility; implement after core MVP is stable. |
| In-browser PDF document preview | Useful UX; deprioritized relative to core functionality. |
| PDF export of audit reports | Low priority stretch. |
| Published audit summary on public portal | Useful for citizen transparency; deprioritized relative to core. |
| Physical progress history chart (per milestone over time) | Data is captured; chart is an enhancement. |

### 16.3 Out of Scope

All items listed in Section 3 (Non-Goals) are out of scope and shall not be introduced.

---

## 17. Acceptance Criteria

The following product-level acceptance criteria define what a successful MVP demonstration looks like. These shall be converted into test scenarios in the FRD/Testing plan.

| AC ID | Acceptance Criterion |
|---|---|
| AC-01 | A new user account can be created by ACT-01 with an assigned role. The new user can log in with their credentials. |
| AC-02 | ACT-02 can create an infrastructure project. The project record is created in the database. A ProjectCreated event is visible in the blockchain audit trail. |
| AC-03 | ACT-03 (assigned officer) can define milestones for the project. A MilestoneDefined event is visible in the blockchain audit trail for each milestone. |
| AC-04 | ACT-04 can submit a fund release request against a milestone, upload evidence documents, and confirm that each document has a verifiable IPFS CID stored in the database. A FundReleaseRequested event with the IPFS CIDs is visible in the blockchain audit trail. |
| AC-05 | ACT-03 can submit an officer verification report with IPFS upload. An OfficerVerified event with the IPFS CID is visible in the blockchain audit trail. |
| AC-06 | ACT-02 can approve a fund release request. Financial utilization is updated correctly. A FundReleaseApproved event is visible in the blockchain audit trail. |
| AC-07 | ACT-02 can reject a fund release request with a reason. The rejection reason is stored. A FundReleaseRejected event is visible in the blockchain audit trail. The rejected request is permanently retained in the history. |
| AC-08 | ACT-04 can resubmit a rejected request with new evidence. The resubmission creates a new request. The prior rejected request remains in the history. |
| AC-09 | With a milestone whose due date is in the past and completion status is not Completed, the Server's periodic check marks it as Missed/Overdue. ACT-02 and ACT-03 receive in-app notifications. No AI action is required to trigger this. |
| AC-10 | After a qualifying fund lifecycle event (e.g., fund release approved), the AI module runs automatically and produces a risk score for the project. |
| AC-11 | A project with designed anomalous data (e.g., financial utilization: 80%, physical progress: 20%) produces an AI flag with a risk score above the threshold and a contributing factor explanation identifying the mismatch. |
| AC-12 | An AIAnomalyRecorded event is visible in the blockchain audit trail when an AI flag is created. |
| AC-13 | ACT-05 can view the AI flag queue, inspect a flag's risk score and contributing factors, access the linked IPFS evidence, and view the project's blockchain audit trail from the investigation view. |
| AC-14 | ACT-02 can formally escalate a concern to ACT-05. An Escalation record is created. ACT-05 receives an in-app notification and the escalation appears in their investigation queue. |
| AC-15 | ACT-05 can record an audit finding: upload an audit report to IPFS, and submit an AuditFindingRecorded event on-chain via their connected wallet. The event is visible in the blockchain audit trail. |
| AC-16 | ACT-02 can mark a project as Completed. A ProjectCompleted event is visible in the blockchain audit trail. |
| AC-17 | ACT-06 can access the Citizen portal without authentication and view project summaries, financial data, and the public fund event timeline. Sensitive data (contractor documents, AI flags, user accounts) is not visible. |
| AC-18 | The blockchain audit trail view in the Client displays all 10 event types for a project that has completed the full fund lifecycle. |
| AC-19 | The Client renders correctly in both light mode and dark mode. Switching between modes does not break any view. |
| AC-20 | Cross-role API access is rejected: e.g., ACT-04 cannot call an endpoint reserved for ACT-02; the Server returns 403. |
| AC-21 | AI risk flags display the disclaimer: "AI risk flags indicate anomalous patterns and do not establish fraud or corruption." |
| AC-22 | All forms display appropriate validation errors when required fields are empty or invalid, and the submission button is disabled during a pending request. |
| AC-23 | All primary dashboard views load without perceptible delay under representative demo data and local demo conditions. No hard time limit is specified. |

---

## 18. Traceability Matrix

| Confirmed Req. | Description (Summary) | PRD Functional Req(s) | Product Module(s) | Next Document |
|---|---|---|---|---|
| CR-01 | Fund lifecycle tracking | FR-020–024, FR-030–034, FR-040–045, FR-050–055, FR-060–063 | MOD-03, MOD-04, MOD-05, MOD-06, MOD-07 | FRD, System Architecture |
| CR-02 | Blockchain tamper-evident audit trail (10 event types) | FR-080–085 | MOD-09 | System Architecture, TRD |
| CR-03 | IPFS document storage; CIDs on-chain and in DB | FR-070–074 | MOD-08 | TRD, System Architecture |
| CR-04 | AI detects anomalous patterns; does not prove fraud | FR-090–098 | MOD-10 | AI/ML Design |
| CR-05 | PostgreSQL stores all operational data; blockchain does not replace it | FR-080, FR-081, §15 | MOD-04, MOD-09 | System Architecture, TRD |
| CR-06 | Six actor roles | FR-010–014, §4 | MOD-02 | FRD |
| CR-07 | Citizen no-login; all others JWT | FR-001–006, FR-130–135 | MOD-01, MOD-14 | FRD |
| CR-08 | End-to-end functional and demonstrable | AC-01–AC-23, NFR-09–NFR-21 | All | 07-TESTING.md |
| CR-09 | Client: modern, professional, visually polished | §14, NFR-11–NFR-15 | Client | UI/UX Design |
| CR-10 | Documentation-first process | (this document) | N/A | FRD |
| CR-11 | No production-scale complexity | §3 (Non-Goals) | N/A | System Architecture |
| CR-12 | Large documents on IPFS; CIDs on-chain | FR-070–074, FR-080 | MOD-08, MOD-09 | System Architecture |
| CR-13 | AI provides explainable contributing factors | FR-093, FR-097, FR-098 | MOD-10 | AI/ML Design |
| CR-14 | Physical progress % and financial utilization % are separate | FR-031, FR-060–063, FR-140 | MOD-07, MOD-04 | FRD, System Architecture |
| CR-15 | Partial fund releases per milestone | FR-034, FR-050 | MOD-06, MOD-04 | FRD |
| CR-16 | Contractor resubmission; rejected requests retained | FR-033, FR-054, FR-055 | MOD-06, MOD-04 | FRD |
| CR-17 | Auditors record audit findings on-chain | FR-103–105 | MOD-11 | System Architecture, FRD |
| CR-18 | Server-side blockchain event listener indexes to PostgreSQL | FR-081 | MOD-09 | TRD, System Architecture |
| CR-19 | Platform demonstrable on local Hardhat | FR-085, NFR-20 | MOD-09 | System Architecture |
| CR-20 | JWT app auth; wallet separate and only for on-chain writes | FR-001–006 | MOD-01 | TRD |
| CR-21 | AI event-triggered; runs within Server | FR-090, FR-091 | MOD-10 | AI/ML Design, TRD |
| CR-22 | AI data: synthetic/semi-synthetic | FR-099, NFR-21 | MOD-10 | AI/ML Design |
| CR-23 | IPFS documents unencrypted; limitation documented | FR-074, NFR-05 | MOD-08 | TRD, System Architecture |
| CR-24 | Monorepo: frontend/, backend/, contracts/, ml/, docs/, data/ | NFR-17 | N/A | System Architecture |
| CR-25 | Server-side missed milestone detection | FR-041–044 | MOD-05 | FRD, TRD |
| CR-26 | Formal Government Admin → Auditor escalation workflow | FR-110–114 | MOD-12 | FRD |
| CR-27 | Solidity events + minimal contract state; no DB duplication | FR-084 | MOD-09 | System Architecture |
| CR-28 | AI calibration: validation-based thresholds; no fixed FPR | FR-099 | MOD-10 | AI/ML Design |
| CR-29 | Dark mode (light + dark + system preference via Tailwind/shadcn/ui) | §14.3 | Client | UI/UX Design |

---

## 19. Open Issues and Future Design Decisions

The following are genuinely new questions that emerged from writing this PRD. They do NOT reopen OQ-A through OQ-E (all resolved in Project Definition v0.3.0). Each is tagged with the downstream document responsible for resolving it.

> These questions do not block the PRD. They are captured here for the responsible downstream documents to address.

---

**PDQ-01 — FRD Question: Milestone completion trigger**
*When is a milestone considered "Completed"? Is it triggered by a Government Admin approval of a fund release for that milestone? Or is it a separate explicit action by ACT-03 or ACT-02? The FRD must define the exact milestone lifecycle state machine.*

**PDQ-02 — TRD Question: Server-side missed milestone check frequency**
*At what interval does the Server run its periodic deadline check? (e.g., every hour, every midnight)? This is a configuration decision for the TRD. The choice affects how quickly Missed/Overdue status is detected after a due date passes.*

**PDQ-03 — TRD/System Architecture Question: AI trigger coordination with fund release approval**
*AI analysis is triggered after a fund release is approved. Should the AI analysis run synchronously within the same request, or asynchronously (e.g., in a background task)? This has implications for API response time. The TRD and System Architecture shall define the execution model.*

**PDQ-04 — TRD/System Architecture Question: On-chain event submission actor**
*For events that are submitted by application users (FundReleaseRequested, OfficerVerified, FundReleaseApproved, FundReleaseRejected, ProjectCreated, etc.), does the Server submit them using a platform-controlled wallet (i.e., a Server-side signer), or does each actor's connected wallet sign and submit? Only AuditFindingRecorded is explicitly confirmed as requiring the Auditor's wallet. The TRD and System Architecture shall define the signing model for all 10 events.*

**PDQ-05 — FRD Question: Government Admin project completion authorization**
*FR-N identifies project completion as an ACT-02 or ACT-03 action "with appropriate authorization." The FRD must specify the exact rule: who can mark a project as Completed, and what conditions must be met (e.g., all milestones completed, no pending fund releases)?*

---

*End of Document — PRD v1.1.0*

*This PRD is based on the approved Project Definition v0.3.0. It is approved as of 2026-09-28. No application code, scaffolding, or dependency installation shall begin until the subsequent FRD is approved.*

*The next document in the sequence is: `docs/02-FRD.md` (Functional Requirements Document).*
