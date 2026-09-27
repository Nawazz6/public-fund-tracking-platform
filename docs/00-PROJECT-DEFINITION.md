# docs/00-PROJECT-DEFINITION.md

**Document Type:** Project Definition Analysis
**Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform
**Status:** BASELINE APPROVED - v0.3.0
**Date (created):** 2026-09-27
**Date (last updated):** 2026-09-27
**Author:** AI Engineering Agent
**Version:** 0.3.0

> **Change summary v0.2.0 -> v0.3.0:**
> All five remaining Open Questions (OQ-A through OQ-E) have been resolved and recorded as DECISIONs.
> Terminology has been standardized throughout: the React/Vite application is now called the **Client**; the Python/FastAPI application is now called the **Server**. This is a terminology correction only — the selected architecture is unchanged.
> New confirmed requirements added: CR-25 (missed milestone Server-side detection), CR-26 (formal escalation workflow), CR-27 (Solidity dual-layer contract design), CR-28 (AI validation-based calibration), CR-29 (dark mode).
> PR-04, PR-05, and PR-06 promoted from Proposed to Confirmed Requirements.
> Section 17 (Open Questions) now contains no unresolved items. A "Resolved Decisions" subsection records OQ-A through OQ-E for traceability.

---

> **Label Key used throughout this document:**
> - **FACT** - Verified from repository, instructions, or a reliable source.
> - **REQUIREMENT** - Explicitly requested or approved.
> - **DECISION** - An explicitly accepted and finalized project decision.
> - **ASSUMPTION** - Temporarily assumed because information is missing.
> - **PROPOSAL** - Recommended but not yet approved.
> - **OPEN QUESTION** - Genuinely unresolved; needs resolution before a dependent decision can be made.

> **Terminology standard (REQUIREMENT):**
> - The React/Vite application is always called the **Client**.
> - The Python/FastAPI application is always called the **Server**.
> - Do NOT use "front-end client", "backend server", "frontend server", or "backend" when referring to the Server component.
> - Directory names `frontend/` and `backend/` in the monorepo are NOT renamed. This correction applies to architectural descriptions only.
>
> **Correct examples:** "Client (React + Vite)", "Server (Python + FastAPI)", "Server-side event listener", "AI runs within the Server process."
> **Incorrect examples:** "front-end client", "backend server", "frontend server."

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Problem Understanding](#2-problem-understanding)
3. [Proposed Product](#3-proposed-product)
4. [Finalized Project Baseline](#4-finalized-project-baseline)
5. [Target Actors](#5-target-actors)
6. [Preliminary User Journeys](#6-preliminary-user-journeys)
7. [Preliminary Fund Lifecycle](#7-preliminary-fund-lifecycle)
8. [Blockchain Responsibility](#8-blockchain-responsibility)
9. [IPFS Responsibility](#9-ipfs-responsibility)
10. [AI Responsibility](#10-ai-responsibility)
11. [Database Responsibility](#11-database-responsibility)
12. [UI/UX Expectations](#12-uiux-expectations)
13. [Academic Scope](#13-academic-scope)
14. [Confirmed Requirements](#14-confirmed-requirements)
15. [Proposed Requirements (Pending Confirmation)](#15-proposed-requirements-pending-confirmation)
16. [Assumptions](#16-assumptions)
17. [Open Questions](#17-open-questions)
18. [Scope Risks](#18-scope-risks)
19. [Finalized Architecture Decisions](#19-finalized-architecture-decisions)
20. [Recommended Next Steps](#20-recommended-next-steps)

---

## 1. Project Overview

**FACT:** The project title is: *AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform*.

**FACT:** This is a final-year Electronics and Computer Science Engineering (ECS) college project.

**REQUIREMENT:** The project must be technically substantial, realistic, well-architected, properly documented, fully demonstrable, visually polished, and actually functional. It must be strong enough to withstand final-year evaluation, viva, and presentation.

**REQUIREMENT:** The project must avoid unnecessary production-scale complexity (microservices, Kubernetes, enterprise IAM, custom blockchain infrastructure, real government payment integration, excessive DevOps) while also avoiding artificial simplification.

**REQUIREMENT:** The project integrates four core technical pillars:
- A PostgreSQL database for operational/queryable application data
- Blockchain (Solidity / Hardhat / local Hardhat network) for tamper-evident auditability
- IPFS (Pinata) for decentralized document/evidence storage
- AI/ML (hybrid rule-based + Isolation Forest) for anomaly and risk detection

---

## 2. Problem Understanding

### 2.1 The Core Problem

Public infrastructure projects - roads, bridges, schools, water supply, housing, etc. - are funded by government budgets or development grants. The lifecycle of these funds typically involves:

- Initial government budget allocation
- Distribution to departments or implementing agencies
- Release to contractors against milestones
- Submission of work-completion evidence and invoices
- Verification by officials or engineers
- Audit reviews

**Problems this system aims to address:**

| Problem | Description |
|---|---|
| Lack of transparency | Citizens and independent auditors cannot easily verify how public funds are used. |
| Manual and opaque approval chains | Fund releases and approvals happen through paper-based or siloed digital systems with no tamper-evident record. |
| Weak evidence tracking | Supporting documents (invoices, site photos, bills) are stored in departmental silos or physical files. |
| Fraud and misappropriation risk | Funds may be released without proportional physical progress; invoices may be duplicated or inflated. |
| Reactive auditing | Audits happen after the fact, often years later, making remediation difficult. |
| No structured anomaly detection | Anomalies in spending patterns, progress mismatches, and unusual contractor behaviour go undetected without manual investigation. |

### 2.2 What the System Is NOT Intended to Do

- **REQUIREMENT:** AI must NOT be described as proving fraud, corruption, guilt, or wrongdoing. AI identifies anomalous or high-risk patterns that may warrant human review. The human decision is always final. AI risk flags indicate anomalous patterns and do not establish fraud or corruption.
- **REQUIREMENT:** The system is NOT a payment processing platform. It tracks and records fund lifecycle events. It does not execute actual government financial transactions.
- The system is not a replacement for legal or judicial proceedings.
- The system does not integrate with real government payment systems (e.g., PFMS, Aadhaar-linked disbursement).

---

## 3. Proposed Product

**DECISION:** A web-based platform consisting of:

1. **Client (React + Vite)** - Role-specific dashboards, project views, fund release workflows, AI risk views, blockchain audit trail viewer, and a public citizen transparency portal.
2. **Server (Python + FastAPI)** - Application APIs, JWT-based authentication, role-based authorization, workflow orchestration, database access (PostgreSQL), blockchain integration, IPFS integration, AI/ML orchestration, and Server-side deadline detection.
3. **Solidity smart contracts (Hardhat)** - Tamper-evident on-chain anchoring of important project and fund lifecycle events.
4. **PostgreSQL database** - Stores all operational/queryable application data. Blockchain does NOT replace PostgreSQL.
5. **IPFS (Pinata)** - Stores off-chain project evidence documents. CIDs are referenced by the database and anchored on-chain.
6. **AI/ML module (Python, within the Server)** - Hybrid rule-based + Isolation Forest anomaly and risk detection. Runs within the Server process; no separate inference service.

---

## 4. Finalized Project Baseline

This section records all decisions that were finalized during the project definition review sessions of 2026-09-27.

### 4.1 Technology Stack

| Layer | Decision | Notes |
|---|---|---|
| **Client** | React + Vite | Not Next.js; no SSR requirement. |
| **Client styling** | Tailwind CSS + shadcn/ui | Custom design system on top of shadcn/ui. Premium, polished, dashboard-oriented. Dark mode supported. |
| **Server** | Python + FastAPI | Single Server process. Hosts APIs, auth, workflow, blockchain integration, IPFS integration, deadline detection, and AI/ML. |
| **Database** | PostgreSQL | Primary operational data store. Not replaced by blockchain. |
| **Smart contracts** | Solidity | Ethereum/EVM-compatible. Dual-layer: Solidity events for audit history + minimal contract state for validation. |
| **Smart contract toolchain** | Hardhat | Development, testing, and deployment. |
| **Blockchain network** | Local Hardhat network (primary) | Ethereum Sepolia is an optional stretch deployment only. The platform must remain demonstrable without a live public testnet. |
| **IPFS provider** | Pinata API | Managed pinning. Documents are unencrypted for this version; limitation explicitly documented. |
| **AI/ML approach** | Hybrid (rule-based + Isolation Forest) | XGBoost added only if later justified by data/requirements. Validation-based threshold calibration. |
| **AI explainability** | Feature-based and rule-based explanations | Risk results must show contributing factors and the reason a flag was generated. |
| **Authentication** | JWT-based application auth + role-based authorization | Wallet connection is separate from application auth. Not wallet-only. |
| **Repository structure** | Monorepo | Directories: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/`. Directory names are not renamed; "Client" and "Server" are architectural descriptions. |
| **Dark mode** | In scope | Implemented via Tailwind CSS + shadcn/ui design tokens. Not a separate architectural subsystem. |

### 4.2 Actor Model

**DECISION:** Six actors are confirmed.

| Actor ID | Role Name | Access Type |
|---|---|---|
| ACT-01 | Platform Admin | Authenticated (JWT). System-level user management and configuration. |
| ACT-02 | Government Admin | Authenticated (JWT). Project creation, budget allocation, fund release approvals, formal Auditor escalation. |
| ACT-03 | Department Officer / Engineer | Authenticated (JWT). Milestone management, physical progress recording, contractor submission verification. |
| ACT-04 | Contractor | Authenticated (JWT). Fund release requests, evidence document uploads to IPFS. |
| ACT-05 | Auditor | Authenticated (JWT). Reviews AI-flagged anomalies and formal escalations, records audit findings on-chain. |
| ACT-06 | Citizen / Public | No authentication required. Public read-only access to non-sensitive project data. |

### 4.3 Fund Lifecycle Decisions

**DECISION:** Partial fund releases per milestone are supported (e.g., 60% on interim verification, 40% on final completion). This is fund-tracking/recording, NOT actual government payment processing.

**DECISION:** A contractor can resubmit a rejected fund release request. Previous rejected requests remain part of the permanent historical record and are not deleted.

**DECISION:** Physical progress and financial utilization are kept as separate, explicit concepts:
- Physical progress (%) is entered and verified by the Department Officer / Engineer.
- Financial utilization (%) is derived from fund release amounts vs. total project budget.
- Example: Physical 25% / Financial 82% constitutes a candidate AI risk signal.

### 4.4 Blockchain Identity

**DECISION:** Application users have normal application identities (JWT/credential-based). Blockchain interactions use wallet addresses where required (e.g., when submitting on-chain events). The application is NOT wallet-only - normal users can use the application without a connected wallet for most functions; wallet connection is required only for blockchain-writing operations.

### 4.5 Blockchain Indexing Architecture

**DECISION:** A Server-side blockchain event listener/indexer syncs on-chain events to PostgreSQL. The Client does NOT directly query the blockchain for normal application views.

Data flow:
```
Blockchain
  -> Server event listener
  -> PostgreSQL (BlockchainEvent table)
  -> Client (via Server API)
```

### 4.6 Auditor Blockchain Writes

**DECISION:** Auditors can record audit findings on-chain. The blockchain stores the tamper-evident reference/event (AuditFindingRecorded), not the full audit report. Detailed audit reports are stored off-chain on IPFS; their CID is referenced in the on-chain event.

### 4.7 AI Execution

**DECISION:** AI scoring is event-triggered. Analysis runs after important fund lifecycle events (fund release approved, officer verification submitted, new milestone defined). A nightly batch infrastructure is NOT built unless later justified. The AI module runs within the Server process; no separate inference service or microservice is created.

### 4.8 IPFS Document Privacy

**DECISION:** Documents uploaded to IPFS are unencrypted for this college-project version. This limitation is explicitly acknowledged in the documentation. In a real system, sensitive contractor documents would require encryption and access control before upload.

### 4.9 Missed Milestone Deadline Detection (Resolved from OQ-A)

**DECISION:** Milestone deadline detection is a deterministic Server-side responsibility. AI does NOT determine whether a milestone deadline was missed.

Mechanism:
- The Server periodically checks each active milestone: due date vs. current date vs. milestone completion status vs. physical progress/completion status.
- If the current date is past the milestone due date and the milestone is not completed, the Server marks the milestone as **Missed/Overdue** in PostgreSQL and generates an in-app alert/notification.
- AI may use the Missed/Overdue status as one input risk signal when calculating project risk scores — but it is not the source of the state.

Architectural separation:
- **Server** = authoritative workflow and deadline state (deterministic, rule-based).
- **AI** = anomaly/risk analysis (uses Server-determined state as an input feature).

### 4.10 Formal Government Admin → Auditor Escalation (Resolved from OQ-B)

**DECISION:** Government Admin → Auditor escalation is a formal in-system workflow action.

Workflow:
1. Government Admin selects a project, AI flag, or concern.
2. Government Admin selects "Escalate to Auditor" and enters an escalation reason.
3. The Server creates an escalation record in PostgreSQL (queryable from the application database).
4. Auditor receives an in-app notification.
5. Auditor reviews the project, evidence (IPFS), blockchain audit trail, and any linked AI flag.
6. Auditor records an audit finding and outcome (AuditFindingRecorded event on-chain with IPFS CID).

No email/SMS infrastructure is required for MVP. The escalation record is a first-class application entity stored in PostgreSQL.

### 4.11 Solidity Contract Design Strategy (Resolved from OQ-C)

**DECISION:** Use BOTH Solidity event emissions AND minimal persistent contract state, with a clear separation of responsibilities.

- **Solidity events** → immutable, tamper-evident audit history. The confirmed event model (ProjectCreated, BudgetAllocated, MilestoneDefined, FundReleaseRequested, OfficerVerified, FundReleaseApproved, FundReleaseRejected, AIAnomalyRecorded, AuditFindingRecorded, ProjectCompleted) remains unchanged.
- **Minimal contract state** (mappings/structs) → Server-side validation only. Examples: `projectId → project status`, `projectId → relevant authority address`, `milestoneId → milestone status`. Use only where the contract requires current state to validate a transaction.
- **PostgreSQL** → all application/query/reporting data.
- **Server-side event listener/indexer** → synchronizes blockchain events into PostgreSQL for fast Client querying.

Do NOT duplicate the PostgreSQL database on-chain. The exact structs, mappings, event parameters, and function signatures remain a TRD/Blockchain Design responsibility.

### 4.12 AI False-Positive Calibration Strategy (Resolved from OQ-D)

**DECISION:** Do not promise or hard-code a fixed false-positive percentage. Use validation-based threshold calibration.

Process:
1. Generate realistic synthetic or semi-synthetic project data including normal and intentionally anomalous scenarios.
2. Split data for development/validation.
3. Evaluate the hybrid detection approach (rule-based signals + Isolation Forest).
4. Tune thresholds using validation results.
5. Produce risk scores and explainable contributing factors.
6. Surface high-risk cases to the human Auditor queue.

Reporting (where meaningful on synthetic validation set): precision, recall, F1-score, false-positive rate, representative detection examples.

Do NOT invent an arbitrary threshold requirement (e.g., "FPR < 5%") unless validation evidence justifies it. Risk thresholds may be refined during AI/ML Design and implementation.

The system must always state: *"AI risk flags indicate anomalous patterns and do not establish fraud or corruption."*

### 4.13 Dark Mode (Resolved from OQ-E)

**DECISION:** Dark mode is in scope for the Client.

- Supports: light mode, dark mode, system/OS preference where practical.
- Implemented via Tailwind CSS + shadcn/ui design tokens.
- Dark mode is a UI/UX design feature, NOT a separate architectural subsystem.
- Does NOT create separate screen implementations.

### 4.14 Scope Exclusions (Hard)

The following are explicitly out of scope and must not be introduced:
- Kubernetes or container orchestration
- Unnecessary microservices (one Server process)
- Enterprise IAM
- Custom blockchain networks
- Real government payment integration
- Real Aadhaar or government financial system integration
- Deep learning models (unless later justified by evidence)
- Mobile applications
- Email/SMS notification infrastructure (in-app notifications only for MVP)
- Real-world fraud determination (AI never proves fraud)
- Excessive DevOps infrastructure

---

## 5. Target Actors

**DECISION:** Six actors are confirmed. See Section 4.2 for the full table.

### 5.1 Actor Responsibilities (Detailed)

| Actor | Key Capabilities |
|---|---|
| Platform Admin | User account management, role assignment, system configuration. Not involved in project fund workflows. |
| Government Admin | Create projects, allocate budgets, define project parameters, review and approve/reject fund release requests, view AI risk dashboards, formally escalate projects/flags to Auditor (in-system workflow). |
| Department Officer / Engineer | Manage assigned projects, define milestones, record physical progress updates, review contractor submissions, submit verification/inspection reports, recommend approval or rejection. |
| Contractor | View assigned milestones, submit fund release requests with evidence (invoices, bills, site photos uploaded to IPFS), resubmit rejected requests, monitor request status, receive in-app notifications. |
| Auditor | View AI-flagged anomaly queue and formal Government Admin escalations, inspect anomaly details (risk score, contributing factors, explanation), access linked IPFS evidence, access blockchain audit trail, record audit findings (on-chain reference + IPFS report). |
| Citizen / Public | Browse active and completed projects (public portal, no login), view project summaries (budget, disbursed amount, physical progress, timeline), view major fund events timeline (non-sensitive data only). |

---

## 6. Preliminary User Journeys

These are preliminary and will be formalized in the FRD.

### 6.1 Government Admin Journey

1. Log in (JWT authentication).
2. Create a new infrastructure project (name, category, district/region, total budget, start/end dates).
3. Allocate project to a Department Officer / Engineer.
4. View project dashboard (physical progress vs. financial utilization, AI risk score, active flags, overdue milestone alerts).
5. Review fund release requests submitted for approval.
6. Approve or reject fund release requests (approval/rejection event recorded on-chain).
7. Review AI-flagged anomaly summaries.
8. Formally escalate a project, AI flag, or concern to an Auditor using the in-system escalation workflow (enters reason; system creates escalation record; Auditor notified in-app).

### 6.2 Department Officer / Engineer Journey

1. Log in and view assigned projects.
2. Define project milestones (name, deliverable description, budget allocation, due date) - MilestoneDefined recorded on-chain.
3. Record physical progress updates for a milestone (entered manually, verified by officer) - stored in PostgreSQL.
4. Review contractor fund release submissions.
5. Submit verification/inspection report (uploaded to IPFS via Server; CID recorded on-chain via OfficerVerified event).
6. Recommend approval or rejection of a fund release request.
7. Receive in-app alert when a milestone is marked Missed/Overdue by the Server.

### 6.3 Contractor Journey

1. Log in and view assigned project milestones.
2. Submit a fund release request against a milestone (partial or full milestone amount).
3. Upload supporting evidence (invoices, bills, site photographs) to IPFS via the platform (Server handles upload) - CIDs recorded on-chain via FundReleaseRequested event.
4. Monitor fund release request status (pending / under review / approved / rejected).
5. Receive in-app notification on approval or rejection (with reason).
6. If rejected, resubmit with revised evidence. Previous rejected request remains in the permanent record.

### 6.4 Auditor Journey

1. Log in and view:
   - AI anomaly/flag queue (sorted by risk score).
   - Formal escalations received from Government Admin.
2. Inspect anomaly/escalation details: type, risk score, contributing factors (rule-based explanation), supporting data, escalation reason (if applicable).
3. Access linked IPFS evidence documents directly from the flag/escalation view.
4. Access the full on-chain audit trail for the project.
5. Record an audit finding: write a detailed audit report (uploaded to IPFS via Server), submit AuditFindingRecorded event on-chain with IPFS CID reference.
6. Mark flag/escalation status: "Reviewed - No Action Required" or "Escalated to Authorities (outside platform)".

### 6.5 Citizen Journey

1. Visit the public portal - no login or registration required.
2. Browse active and completed infrastructure projects, filterable by region or project type.
3. View project summary card: total budget, amount disbursed, physical progress %, milestone timeline.
4. View major fund event timeline for a project (non-sensitive: creation, major approvals, completion).
5. View published audit summaries where available (administrator-published summaries only).

### 6.6 Platform Admin Journey

1. Log in.
2. Create and manage user accounts; assign roles.
3. View system health and configuration.
4. Does NOT participate in project fund workflows.

---

## 7. Preliminary Fund Lifecycle

**DECISION:** The following lifecycle reflects all confirmed decisions including partial releases, contractor resubmission, physical/financial progress distinction, Server-side deadline detection, and formal escalation workflow.

```
[Project Created]
       | -> ProjectCreated event recorded on-chain
       v
[Budget Allocated]
       | -> BudgetAllocated event recorded on-chain
       v
[Milestones Defined by Officer]
       | -> MilestoneDefined event recorded on-chain per milestone
       v
[Server: Periodic Deadline Check]
       | -> Server checks: due date vs. current date vs. milestone completion
       | -> If overdue and incomplete: milestone marked Missed/Overdue in PostgreSQL
       |    -> in-app alert generated for Officer and Government Admin
       |    -> AI may use Missed/Overdue status as a risk signal input
       v
[Physical Progress Updated by Officer]
       | -> stored in PostgreSQL (ProjectProgress table)
       v
[Contractor Submits Fund Release Request]
       | -> evidence uploaded to IPFS (invoices, photos, bills) via Server
       | -> FundReleaseRequested event recorded on-chain (includes IPFS CIDs)
       v
[Officer Reviews and Submits Verification Report]
       | -> report uploaded to IPFS via Server
       | -> OfficerVerified event recorded on-chain (includes IPFS CID)
       v
[Government Admin Approves / Rejects]
       | -> FundReleaseApproved or FundReleaseRejected recorded on-chain
       v
[If Rejected: Contractor May Resubmit]
       | -> previous rejected request retained in permanent record
       | -> new FundReleaseRequested event recorded on-chain
       v
[If Approved: Fund Release Recorded]
       | -> financial utilization updated in PostgreSQL
       v
[AI Risk Analysis Triggered]
       | -> event-triggered after approval/verification events
       | -> uses: physical progress %, financial utilization %, Missed/Overdue flags, invoice data
       | -> compares physical progress % vs financial utilization %
       | -> checks budget trajectory, delay risk, invoice patterns
       v
[Anomaly Detected?]
      |-- No  -> Normal continuation
      |-- Yes -> AIAnomalyRecorded event recorded on-chain
                    | -> AI flag with risk score and contributing factors stored in PostgreSQL
                    v
[Government Admin Reviews / Optionally Escalates]
                    | -> if concern warrants formal review:
                    | -> Government Admin selects "Escalate to Auditor"
                    | -> enters escalation reason
                    | -> Server creates Escalation record in PostgreSQL
                    | -> Auditor receives in-app notification
                    v
             [Auditor Reviews Flag / Escalation]
                    | -> inspects evidence (IPFS), audit trail (blockchain), contributing factors
                    v
             [Auditor Records Audit Finding]
                    | -> audit report uploaded to IPFS via Server
                    | -> AuditFindingRecorded event on-chain (IPFS CID)
                    v
             [Human Decision / Escalation outside platform]
                    v
[Project Completion / Settlement]
       | -> ProjectCompleted event recorded on-chain
       v
[Project Archived]
```

---

## 8. Blockchain Responsibility

### 8.1 Justified Purpose

**DECISION:** Blockchain provides a tamper-evident, auditable record of important project and fund lifecycle events. It does NOT store every database field, and does NOT replace PostgreSQL.

**REQUIREMENT:** Large documents must NOT be stored directly on-chain. Document content is stored on IPFS; only the IPFS CID and minimal metadata are referenced on-chain.

The blockchain's role is **anchoring** - recording that a specific event occurred at a specific time with specific identifiers in a way that cannot be silently altered after the fact.

### 8.2 Finalized On-Chain Event Names and Data

**DECISION:** The following event names are confirmed for the smart contract design:

| Event Name | Key On-Chain Data |
|---|---|
| ProjectCreated | Project ID, Government Admin wallet address, creation timestamp, total budget |
| BudgetAllocated | Project ID, allocation amount, timestamp, allocating authority wallet address |
| MilestoneDefined | Project ID, milestone ID, milestone budget portion, due date hash |
| FundReleaseRequested | Project ID, milestone ID, contractor wallet address, requested amount, IPFS CID(s) of evidence |
| OfficerVerified | Project ID, milestone ID, officer wallet address, verification outcome, IPFS CID of verification report |
| FundReleaseApproved | Project ID, milestone ID, approving authority wallet address, approved amount, timestamp |
| FundReleaseRejected | Project ID, milestone ID, rejecting authority wallet address, reason hash, timestamp |
| AIAnomalyRecorded | Project ID, flag ID, risk score (integer 0-100), anomaly type identifier, timestamp |
| AuditFindingRecorded | Project ID, flag ID, auditor wallet address, finding status, IPFS CID of audit report |
| ProjectCompleted | Project ID, completion timestamp, final financial utilization amount |

### 8.3 Solidity Contract Design Strategy (OQ-C Resolved)

**DECISION:** Use BOTH Solidity event emissions AND minimal persistent contract state.

| Layer | Responsibility |
|---|---|
| Solidity events | Immutable, tamper-evident audit history. All 10 confirmed event types above. |
| Minimal contract state (mappings/structs) | Server-side validation only. Examples: `projectId → status`, `projectId → authority address`, `milestoneId → status`. |
| PostgreSQL | All application/query/reporting data. |
| Server-side event listener/indexer | Synchronizes blockchain events into PostgreSQL for fast Client querying. |

Do NOT duplicate the PostgreSQL database on-chain. Exact contract structs, mappings, event parameters, and function signatures are a TRD/Blockchain Design responsibility.

### 8.4 What Stays Off-Chain (PostgreSQL or IPFS)

| Data | Storage |
|---|---|
| User profile data (names, contacts, credentials) | PostgreSQL |
| Full project descriptions and notes | PostgreSQL |
| Milestone details and progress tracking | PostgreSQL |
| Financial time-series data for dashboards | PostgreSQL |
| AI feature vectors, model state, training data | PostgreSQL / `ml/` layer |
| Internal application workflow state | PostgreSQL |
| Escalation records (Government Admin → Auditor) | PostgreSQL |
| Document files (invoices, photos, reports) | IPFS |
| Detailed audit reports | IPFS |
| AI flag explanations and contributing factors | PostgreSQL |

### 8.5 Blockchain Network

**DECISION:** Local Hardhat network is the primary development and viva/demo environment. The platform must be fully demonstrable without a live public testnet. Ethereum Sepolia is an optional stretch deployment only, not a dependency.

---

## 9. IPFS Responsibility

### 9.1 Justified Purpose

**DECISION:** IPFS (via Pinata API) stores off-chain project evidence documents. The Server handles all IPFS uploads; the Client does not directly upload to IPFS. Pinned CIDs are stored in PostgreSQL (Document table) and referenced in on-chain events.

**REQUIREMENT:** IPFS is not a blockchain. A CID is a content address. Persistence depends on pinning via Pinata.

### 9.2 Document Types Stored on IPFS

| Document Type | Uploaded By | Triggered By |
|---|---|---|
| Contractor invoices | Contractor (via Server) | Fund release request |
| Site photographs | Contractor (via Server) | Fund release request |
| Bill of materials | Contractor (via Server) | Fund release request |
| Progress report | Contractor / Officer (via Server) | Milestone submission |
| Officer inspection / verification report | Department Officer (via Server) | Verification step |
| Audit report | Auditor (via Server) | Audit finding |

### 9.3 Privacy Limitation (Documented)

**DECISION:** Documents uploaded to IPFS are unencrypted for this college-project version. Any person who knows a CID can access the document via a public IPFS gateway. This is acknowledged as a limitation: in a production system, sensitive contractor documents would require encryption and/or access-controlled storage before IPFS upload. This limitation must be explicitly stated in the TRD and Security Design documents.

### 9.4 Document Retention

**DECISION:** A formal document retention/lifecycle policy is out of scope for this project version. Pinata pinning will be maintained for the lifetime of the demo. This is explicitly documented as a limitation.

---

## 10. AI Responsibility

### 10.1 Justified Purpose

**REQUIREMENT:** The AI/ML component identifies anomalous or high-risk patterns in project and fund data that may warrant human investigation. It does NOT prove fraud, corruption, guilt, or wrongdoing. All final decisions are made by humans (Auditors, Government Admins). AI risk flags indicate anomalous patterns and do not establish fraud or corruption.

### 10.2 Confirmed AI Capabilities (Initial Scope)

**DECISION:** The following AI capabilities are confirmed for the initial implementation:

| Capability | Approach | Signal Type |
|---|---|---|
| Financial utilization vs. physical progress mismatch | Rule-based threshold + scoring | Deterministic |
| Budget overrun risk | Trajectory-based rule (spend rate vs. remaining budget/time) | Deterministic |
| Project delay risk | Date-based rule (milestone deadlines vs. current date / submission history; uses Server-determined Missed/Overdue state) | Deterministic |
| Duplicate / near-duplicate invoice detection | Similarity scoring on invoice metadata (amount, vendor, date, project) | Rule-based + optional ML |
| Unusual spending patterns | Isolation Forest (unsupervised anomaly detection on transaction amounts/timing) | ML-based |

**PROPOSAL (Stretch goal):** XGBoost classifier for overall project risk scoring, if sufficient synthetic training data can be generated and the model can be meaningfully validated.

### 10.3 AI Approach

**DECISION:** Hybrid approach:
- **Deterministic / rule-based signals** for conditions that have clear, explainable thresholds (progress mismatch, overrun trajectory, delay detection).
- **Isolation Forest** for unsupervised pattern-based anomaly detection on spending data.
- XGBoost is NOT included in the initial scope. It may be added later if the data and requirements justify it.

**REQUIREMENT:** Deep learning models are out of scope. Complexity must be appropriate for a college-project evaluation.

### 10.4 AI Execution

**DECISION:** AI scoring is event-triggered. Analysis runs after important fund lifecycle events (fund release approved, officer verification submitted, new milestone defined). A nightly batch job infrastructure is NOT built unless later justified.

The AI module runs within the Server process. No separate inference server or microservice is created.

### 10.5 AI Explainability

**DECISION:** Risk results presented to Auditors must show:
- The overall risk score (0-100 integer scale, PROPOSAL for exact scale).
- The contributing factors (e.g., "Financial utilization: 82% vs Physical progress: 25%").
- The rule or pattern that generated the flag (e.g., "Mismatch threshold exceeded by 35 percentage points").

SHAP values may be used for the Isolation Forest model if feasible; rule-based explanations are sufficient for deterministic signals.

### 10.6 Data Strategy and False-Positive Calibration (OQ-D Resolved)

**DECISION:** AI will be trained and demonstrated on synthetic/semi-synthetic data modelled on realistic public fund management scenarios. Real government data is unavailable. This limitation must be clearly documented in the AI/ML Design document and acknowledged in the viva.

**DECISION:** No fixed false-positive rate is promised or hard-coded. Validation-based threshold calibration is used:
1. Generate realistic synthetic data (normal and anomalous scenarios).
2. Evaluate the hybrid detection approach on a validation split.
3. Tune thresholds using validation results.
4. Report precision, recall, F1-score, and false-positive rate on the synthetic validation dataset where meaningful.

Do NOT invent an arbitrary threshold requirement unless validation evidence justifies it.

### 10.7 Data Storage (AI-Related)

| Data | Storage |
|---|---|
| AI feature vectors | PostgreSQL |
| AI flag records (score, type, status, explanation) | PostgreSQL (AIFlag table) |
| Synthetic training data | `data/` directory in monorepo |
| Trained model artifacts | `ml/` directory in monorepo (or loaded at Server startup) |
| On-chain anomaly reference | Blockchain (AIAnomalyRecorded event) |

---

## 11. Database Responsibility

### 11.1 Justified Purpose

**REQUIREMENT:** PostgreSQL stores all queryable, relational, high-frequency, and operational application data. Blockchain does NOT replace PostgreSQL.

### 11.2 Proposed Database Entities (Preliminary)

| Entity | Description |
|---|---|
| User | All authenticated users (all roles), hashed credentials, role assignment |
| Project | Infrastructure project metadata, budget, status, dates, assigned officer |
| Milestone | Project milestones, budget portions, due dates, status (including Missed/Overdue), physical progress % |
| FundRelease | Fund release requests (all versions including rejected), amounts, status, submission history |
| Document | IPFS document references (CID, document type, uploader, timestamp, linked entity) |
| BlockchainEvent | Server-indexed on-chain events for fast Client querying |
| AIFlag | AI-generated anomaly flags (type, risk score, contributing factors, status, auditor action) |
| AuditFinding | Auditor decisions linked to AI flags or escalations, IPFS audit report CID |
| ProjectProgress | Physical progress records per milestone over time (officer-entered, timestamped) |
| Escalation | Government Admin → Auditor escalation records (reason, linked project/flag, status) |
| Notification | In-app notification queue (for approval/rejection/flag/escalation/overdue events) |

### 11.3 Relationship Between Data Layers

```
+-------------------+   CID reference    +------------------+
|   IPFS (Pinata)   |<-------------------| PostgreSQL       |
| (documents/files) |                    | (operational     |
+-------------------+                    |  data)           |
                                         +--------+---------+
                                                  | key events (anchoring)
                                                  v
                                         +------------------+
                                         | Blockchain       |
                                         | (Hardhat local)  |
                                         | (audit trail /   |
                                         |  anchoring)      |
                                         +--------+---------+
                                                  | Server event listener
                                                  v
                                         +------------------+
                                         | PostgreSQL       |
                                         | (BlockchainEvent |
                                         |  index table)    |
                                         +------------------+
                                                  |
                                                  v (via Server API)
                                         +------------------+
                                         | Client           |
                                         | (React + Vite)   |
                                         +------------------+
```

---

## 12. UI/UX Expectations

### 12.1 Technology Decisions

**DECISION:** Client uses React + Vite + Tailwind CSS + shadcn/ui with a custom design system built on top. Dark mode is in scope, implemented via Tailwind + shadcn/ui design tokens.

### 12.2 Design Requirements

**REQUIREMENT:** The final product must look professional, modern, and premium. UI/UX is not an afterthought.

| Category | Requirement |
|---|---|
| Visual Quality | Premium dashboard-oriented design. Not a bare-bones admin panel. |
| Typography | Professional font stack (e.g., Inter, Plus Jakarta Sans). |
| Color System | Cohesive, curated color palette. Not raw Tailwind defaults. Custom tokens defined in the design system. Separate light/dark mode tokens. |
| Spacing | Consistent spatial rhythm using the design system. |
| Hierarchy | Clear visual hierarchy on all views. |
| Dashboards | Role-specific dashboards with real data visualization (charts, KPIs, risk indicators, overdue alerts). |
| Charts | Budget vs. actual spend, physical vs. financial progress, risk score trends. |
| Tables | Sortable, filterable project and fund data tables. |
| Forms | Validated, well-labelled forms for project creation, fund release submissions. |
| States | Loading, empty, error, and success states on all interactive views. |
| Responsive | Must function on desktop; mobile-responsiveness is a bonus. |
| Role-Specific | Each actor sees only what is relevant to their role. |
| Dark Mode | In scope. Light mode, dark mode, and system preference supported via design tokens. |

### 12.3 Key Views (Preliminary)

| View | Primary Actor(s) |
|---|---|
| Platform Admin Dashboard | Platform Admin |
| Government Admin Dashboard (project overview, budget KPIs, AI flag count, overdue alerts, approval queue, escalation actions) | Government Admin |
| Project Detail View (role determines visible actions) | All authenticated roles |
| Fund Release Workflow View (submission -> verification -> approval) | Contractor, Officer, Government Admin |
| Auditor Queue (AI flags + formal Government Admin escalations, risk scores, contributing factor explanations) | Auditor |
| Blockchain Audit Trail View (on-chain event timeline per project) | All authenticated roles |
| Public Citizen Portal (project list, project summary, fund events) | Citizen (no login) |
| Document Viewer (IPFS-hosted documents in-browser) | All authenticated roles |

---

## 13. Academic Scope

### 13.1 What This Is

- A **functional demonstration prototype** for final-year engineering evaluation.
- It must work end-to-end within a demo environment (developer laptop + local Hardhat network).
- It must be presentable in a viva and survive technical questioning on all four pillars.
- It must demonstrate genuine integration of all four technical pillars.

### 13.2 What This Is NOT

- Not a production government system.
- Not required to pass government security audits.
- Not required to execute actual financial transactions.
- Not required to deploy at scale.
- Not required to be multi-tenant across hundreds of departments.
- Not integrated with real government identity or payment systems.

### 13.3 Scope Calibration

| Feature | In Scope | Out of Scope |
|---|---|---|
| Client (React + Vite) | Yes | Mobile native apps |
| Server (Python + FastAPI) | Yes | Microservices architecture |
| Role-based access control (JWT) | Yes | Enterprise SSO / OAuth provider |
| Blockchain event anchoring (local Hardhat) | Yes | Custom consensus mechanism |
| Ethereum Sepolia testnet deployment | Stretch only | Mainnet deployment |
| IPFS document upload/retrieval (Pinata) | Yes | Encrypted document access control |
| AI anomaly detection (hybrid) | Yes | Deep learning models |
| AI - rule-based signals | Yes | Real-time streaming fraud alerts |
| AI - Isolation Forest | Yes | |
| AI - XGBoost | Stretch only | |
| Synthetic training data | Yes | Live government data ingestion |
| Validation-based AI threshold calibration | Yes | Fixed/guaranteed false-positive rate |
| Public citizen portal (no login) | Yes | Full open government data API |
| In-app notification system | Yes | SMS, push, email notifications |
| Server-side missed milestone deadline detection | Yes | AI-determined deadline status |
| Formal Government Admin → Auditor escalation | Yes | |
| Dark mode (Client) | Yes | Separate mobile-specific dark theme |
| Auditor on-chain finding recording | Yes | |
| Partial milestone fund releases | Yes | |
| Contractor resubmission of rejected requests | Yes | |
| Physical vs. financial progress tracking | Yes | |
| PDF export of audit reports | Stretch goal | |
| Unit and integration tests | Yes | Full end-to-end automated test suite |
| Kubernetes / container orchestration | No | |
| Real government payment integration | No | |
| Aadhaar / government identity integration | No | |
| Email / SMS notifications | No | |

---

## 14. Confirmed Requirements

Requirements that are explicitly stated, approved, or derived from finalized decisions. OQ-A through OQ-E have been resolved and relevant requirements added below.

| ID | Requirement |
|---|---|
| CR-01 | The system tracks public infrastructure project funds through a defined lifecycle. |
| CR-02 | Blockchain provides a tamper-evident audit trail for important fund events (10 defined event types). |
| CR-03 | IPFS (Pinata) stores supporting documents; CIDs are anchored on-chain and referenced in PostgreSQL. |
| CR-04 | AI/ML detects anomalous/high-risk patterns; it does not prove fraud, corruption, or wrongdoing. AI risk flags indicate anomalous patterns and do not establish fraud or corruption. |
| CR-05 | PostgreSQL stores all operational/queryable application data. Blockchain does NOT replace PostgreSQL. |
| CR-06 | Six actor roles: Platform Admin, Government Admin, Department Officer/Engineer, Contractor, Auditor, Citizen. |
| CR-07 | Citizen access is public/no-login. All other roles require JWT authentication. |
| CR-08 | The product must be fully functional and demonstrable end-to-end. |
| CR-09 | The Client UI must be modern, professional, and visually polished (React + Vite + Tailwind + shadcn/ui). |
| CR-10 | Documentation-first development process (PRD -> FRD -> TRD -> Architecture -> Implementation -> Development). |
| CR-11 | No production-scale complexity (no Kubernetes, no microservices, no enterprise IAM, no real payment integration). |
| CR-12 | Large documents must not be stored directly on-chain. Documents go to IPFS; CIDs go on-chain. |
| CR-13 | AI must provide explainable risk indicators showing contributing factors, not opaque verdicts. |
| CR-14 | Physical progress % and financial utilization % are separate, explicitly tracked concepts. Physical progress is entered/verified by the Department Officer. |
| CR-15 | Partial fund releases per milestone are supported. This is tracking, not actual payment processing. |
| CR-16 | Contractors can resubmit rejected fund release requests. Rejected requests are permanently retained in the record. |
| CR-17 | Auditors can record audit findings on-chain (AuditFindingRecorded event with IPFS CID of audit report). |
| CR-18 | A Server-side blockchain event listener indexes on-chain events to PostgreSQL. The Client does not directly query the blockchain for normal views. |
| CR-19 | The platform must be fully demonstrable on a local Hardhat network without depending on a live public testnet. |
| CR-20 | Application auth is JWT-based. Wallet connection is separate and required only for blockchain-writing operations. |
| CR-21 | AI is event-triggered (not nightly batch). The AI module runs within the Server process. |
| CR-22 | AI data source is synthetic/semi-synthetic. This limitation is explicitly documented. |
| CR-23 | IPFS documents are unencrypted for this version. This limitation is explicitly documented. |
| CR-24 | Monorepo structure: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/`. |
| CR-25 | Server-side periodic deadline detection: the Server determines Missed/Overdue milestone status and generates in-app alerts. AI does NOT determine deadline status; AI may use it as a risk signal input. |
| CR-26 | Government Admin → Auditor escalation is a formal in-system workflow action. The Server creates an Escalation record in PostgreSQL. The Auditor receives an in-app notification. No email/SMS is required. |
| CR-27 | Solidity contracts use BOTH event emissions (audit history) and minimal contract state (Server-side validation only). Do NOT duplicate PostgreSQL data on-chain. |
| CR-28 | AI false-positive calibration uses validation-based threshold tuning on synthetic data. No fixed FPR is promised. Reports precision, recall, F1-score on the validation dataset where meaningful. |
| CR-29 | Client supports light mode, dark mode, and system preference via Tailwind CSS + shadcn/ui design tokens. Dark mode is a UI/UX feature, not a separate architectural subsystem. |

---

## 15. Proposed Requirements (Pending Confirmation)

| ID | Proposed Requirement | Rationale |
|---|---|---|
| PR-01 | In-app notification system for workflow events (approval, rejection, new AI flag, overdue milestone, escalation) | Core workflow UX; essential without email infrastructure. |
| PR-02 | Risk score history chart per project over time | Auditor needs trend visibility, not just current score. |
| PR-03 | Document preview in-browser (PDF/image viewer) | IPFS documents should be inspectable without download. |
| PR-04 | PDF export of audit reports | Stretch goal; low priority. |
| PR-05 | Ethereum Sepolia testnet deployment | Stretch goal; not required for primary demo. |
| PR-06 | XGBoost risk classifier | Stretch goal; contingent on synthetic data quality and validation results. |

> **Note:** PR-04 (missed milestone detection), PR-05 (formal escalation), and PR-06 (dark mode) from v0.2.0 have been promoted to Confirmed Requirements CR-25, CR-26, and CR-29 respectively and are no longer proposed.

---

## 16. Assumptions

| ID | Assumption | Status | Risk if Wrong |
|---|---|---|---|
| AS-01 | The primary demo environment is a developer laptop with local Hardhat network. | Consistent with DECISION | Low |
| AS-02 | Real government fund data is unavailable. Synthetic data is used for AI. | Consistent with DECISION | Low; must be documented for viva |
| AS-03 | MetaMask (or compatible browser wallet) will be required for blockchain-writing operations in the demo. | Active assumption | Medium; requires evaluators to have MetaMask installed |
| AS-04 | A single Server process is sufficient; no microservices needed. | Consistent with DECISION | Low |
| AS-05 | The platform is a web application accessible via a modern browser. | Consistent with DECISION | Low |
| AS-06 | English is the primary UI language. Localisation is out of scope. | Active assumption | Low |
| AS-07 | Pinata free tier is sufficient for the volume of documents generated in a college demo. | Active assumption | Low; can upgrade tier if needed |
| AS-08 | The AI module's synthetic training data can be designed to produce realistic and non-trivial anomaly detection results. | Active assumption | Medium; requires careful data design |

---

## 17. Open Questions

As of v0.3.0, all five previously identified Open Questions (OQ-A through OQ-E) have been resolved. There are no remaining blocking open questions.

### 17.1 Resolved Decisions (Traceability)

| ID | Question | Resolution | Added As |
|---|---|---|---|
| OQ-A | What triggers the missed milestone deadline state? | Server-side periodic check. Server determines Missed/Overdue state. AI uses it as input only. | CR-25, §4.9, §7 |
| OQ-B | Is Government Admin → Auditor escalation a formal in-system action? | Yes. Formal in-system workflow with Escalation record in PostgreSQL and in-app notification. No email/SMS. | CR-26, §4.10, §6.1, §6.4, §7 |
| OQ-C | Solidity events vs. persistent contract state? | Both, with clear separation: events for audit history, minimal state for Server-side validation only. | CR-27, §4.11, §8.3 |
| OQ-D | AI false-positive calibration strategy? | Validation-based threshold tuning on synthetic data. No fixed FPR promised. Reports metrics on validation set. | CR-28, §4.12, §10.6 |
| OQ-E | Is dark mode in scope? | Yes. Light + dark + system preference via Tailwind + shadcn/ui design tokens. Not a separate architectural subsystem. | CR-29, §4.13, §12 |

---

## 18. Scope Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| SR-01 | Pillar overload: Integrating Blockchain + IPFS + AI + Web in one timeline is ambitious. Incomplete integration of any pillar will weaken the viva evaluation. | Medium | High | Phase integration carefully. Each pillar must have a minimal but functional demo path before polish work begins. |
| SR-02 | AI without realistic data: If synthetic data is not realistic enough, anomaly detection will produce trivial or unconvincing results. | Medium | High | Design synthetic data generation carefully; document the methodology; demonstrate meaningful detection at viva. Report validation metrics honestly. |
| SR-03 | Blockchain demo fragility: MetaMask or Hardhat node issues during viva could block the demo. | Low-Medium | High | Local Hardhat is the primary environment (no network dependency). Practice demo with full setup. Prepare fallback screenshots. |
| SR-04 | Wallet friction in demo: Evaluators or audience may not have MetaMask installed. | Medium | Medium | Prepare a demo account with pre-configured MetaMask. Consider a "read-only" demo mode for Auditor/Citizen views. |
| SR-05 | Scope creep: Adding mobile, deep learning, PDF export, real payment integration beyond MVP scope could delay delivery. | High | High | Maintain a strict MVP scope. Stretch goals are clearly labelled and not treated as requirements. |
| SR-06 | Documentation-implementation drift: If code diverges from the documents, the viva argument weakens. | Medium | Medium | Update documents deliberately whenever a decision changes. Documents are the source of truth during design phase; code is the source of truth after implementation. |
| SR-07 | UI complexity underestimated: Role-specific dashboards with charts, tables, workflows, and blockchain views take longer than expected. | Medium | Medium | UI/UX specification must be completed before major Client implementation. Use shadcn/ui to accelerate component development. |
| SR-08 | Pinata dependency: If Pinata free tier limits are exceeded during demo, IPFS uploads will fail. | Low | Medium | Monitor Pinata usage. Prepare local IPFS fallback if needed. Keep evidence files small in demo scenarios. |

---

## 19. Finalized Architecture Decisions

All decisions listed here are finalized. Terminology uses Client (React + Vite) and Server (Python + FastAPI) throughout.

| ID | Component | Decision |
|---|---|---|
| AD-01 | Client | React + Vite |
| AD-02 | Client styling | Tailwind CSS + shadcn/ui + custom design system (light + dark mode) |
| AD-03 | Server | Python + FastAPI (single process) |
| AD-04 | Database | PostgreSQL |
| AD-05 | ORM / database access | To be confirmed in TRD (SQLAlchemy or similar Python ORM is expected) |
| AD-06 | Smart contract language | Solidity |
| AD-07 | Smart contract toolchain | Hardhat |
| AD-08 | Blockchain network (primary) | Local Hardhat network |
| AD-09 | Blockchain network (stretch) | Ethereum Sepolia testnet |
| AD-10 | IPFS provider | Pinata API |
| AD-11 | AI/ML approach | Hybrid: rule-based deterministic signals + Isolation Forest |
| AD-12 | AI/ML library | scikit-learn (Isolation Forest); pandas/numpy for features. XGBoost is a stretch. |
| AD-13 | AI execution model | Event-triggered within Server process. No separate inference service. |
| AD-14 | Authentication | JWT-based application auth + role-based authorization |
| AD-15 | Wallet integration | Separate from application auth; required for blockchain-writing operations only |
| AD-16 | Blockchain indexing | Server-side event listener syncs on-chain events to PostgreSQL |
| AD-17 | Repository structure | Monorepo: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/` |
| AD-18 | Terminology | "Client" (React + Vite), "Server" (Python + FastAPI). Not "front-end client", "backend server", or "frontend server". |
| AD-19 | Deadline detection | Server-side periodic check determines Missed/Overdue milestone state. AI uses this state as input only. |
| AD-20 | Escalation | Formal in-system Government Admin → Auditor escalation. Escalation record in PostgreSQL. In-app notification. |
| AD-21 | Contract design | Dual-layer: Solidity events (audit history) + minimal contract state (Server-side validation only). |
| AD-22 | AI calibration | Validation-based threshold tuning on synthetic data. No fixed FPR promised. |
| AD-23 | Dark mode | In scope. Tailwind + shadcn/ui design tokens. Not a separate architectural subsystem. |

---

## 20. Recommended Next Steps

**DECISION:** No application code, scaffolding, or dependency installation should begin until the PRD is reviewed and approved.

1. **All Open Questions are resolved.** No blocking questions remain before the PRD.

2. **Begin the Product Requirements Document (PRD).**
   - The PRD formalizes: all actors (with ACT IDs), complete feature list, functional requirements (with FR IDs), non-functional requirements, and scope boundaries.
   - The PRD should trace functional requirements back to the confirmed requirements in this document.
   - The PRD must use "Client" and "Server" terminology consistently.

3. **After PRD review, proceed to:** FRD → TRD → System Architecture → Implementation Plan.

4. **Do not begin coding until the Implementation Plan is approved.**

---

*End of Document — v0.3.0*

*This document reflects the fully resolved project baseline as of 2026-09-27. OQ-A through OQ-E are closed. No open questions remain. All changes to decisions recorded here must be reflected in this document and all downstream documents that depend on them.*
