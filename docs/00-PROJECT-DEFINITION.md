# docs/00-PROJECT-DEFINITION.md

**Document Type:** Project Definition Analysis
**Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform
**Status:** BASELINE APPROVED - v0.2.0
**Date (created):** 2026-09-27
**Date (last updated):** 2026-09-27
**Author:** AI Engineering Agent
**Version:** 0.2.0

> **Change summary v0.1.0 -> v0.2.0:**
> All finalized project decisions have been incorporated. Open questions that have been answered are now closed and recorded as DECISIONs. Remaining genuinely unresolved implementation details are retained as OPEN QUESTION. The actor model, architecture section, confirmed requirements, proposed requirements, assumptions, scope calibration, and the decision table have all been updated to reflect the approved baseline.

---

> **Label Key used throughout this document:**
> - **FACT** - Verified from repository, instructions, or a reliable source.
> - **REQUIREMENT** - Explicitly requested or approved.
> - **DECISION** - An explicitly accepted and finalized project decision.
> - **ASSUMPTION** - Temporarily assumed because information is missing.
> - **PROPOSAL** - Recommended but not yet approved.
> - **OPEN QUESTION** - Genuinely unresolved; needs resolution before a dependent decision can be made.

> **Terminology standard (REQUIREMENT):**
> - The React/Vite application is always called the **front-end client**.
> - The Python/FastAPI application is always called the **backend server**.
> - Do NOT use "frontend server" or any ambiguous hybrid term.

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

- **REQUIREMENT:** AI must NOT be described as proving fraud, corruption, guilt, or wrongdoing. AI identifies anomalous or high-risk patterns that may warrant human review. The human decision is always final.
- **REQUIREMENT:** The system is NOT a payment processing platform. It tracks and records fund lifecycle events. It does not execute actual government financial transactions.
- The system is not a replacement for legal or judicial proceedings.
- The system does not integrate with real government payment systems (e.g., PFMS, Aadhaar-linked disbursement).

---

## 3. Proposed Product

**DECISION:** A web-based platform consisting of:

1. **React/Vite front-end client** - Role-specific dashboards, project views, fund release workflows, AI risk views, blockchain audit trail viewer, and a public citizen transparency portal.
2. **FastAPI backend server** - Application APIs, JWT-based authentication, role-based authorization, workflow orchestration, database access (PostgreSQL), blockchain integration, IPFS integration, and AI/ML orchestration.
3. **Solidity smart contracts (Hardhat)** - Tamper-evident on-chain anchoring of important project and fund lifecycle events.
4. **PostgreSQL database** - Stores all operational/queryable application data. Blockchain does NOT replace PostgreSQL.
5. **IPFS (Pinata)** - Stores off-chain project evidence documents. CIDs are referenced by the database and anchored on-chain.
6. **AI/ML module (Python, within the backend server)** - Hybrid rule-based + Isolation Forest anomaly and risk detection. Orchestrated by the FastAPI backend server; no separate inference service.

---

## 4. Finalized Project Baseline

This section records all decisions that were finalized during the project definition review session of 2026-09-27.

### 4.1 Technology Stack

| Layer | Decision | Notes |
|---|---|---|
| **Front-end client** | React + Vite | Not Next.js; no SSR requirement. |
| **Styling** | Tailwind CSS + shadcn/ui | Custom design system on top of shadcn/ui. Premium, polished, dashboard-oriented. |
| **Backend server** | Python + FastAPI | Single backend service. Hosts APIs, auth, workflow, blockchain integration, IPFS integration, and AI/ML. |
| **Database** | PostgreSQL | Primary operational data store. Not replaced by blockchain. |
| **Smart contracts** | Solidity | Ethereum/EVM-compatible. |
| **Smart contract toolchain** | Hardhat | Development, testing, and deployment. |
| **Blockchain network** | Local Hardhat network (primary) | Ethereum Sepolia is an optional stretch deployment only. The platform must remain demonstrable without a live public testnet. |
| **IPFS provider** | Pinata API | Managed pinning. Documents are unencrypted for this version; limitation explicitly documented. |
| **AI/ML approach** | Hybrid (rule-based + Isolation Forest) | XGBoost added only if later justified by data/requirements. No separate inference service. |
| **AI explainability** | Feature-based and rule-based explanations | Risk results must show contributing factors and the reason a flag was generated. |
| **Authentication** | JWT-based application auth + role-based authorization | Wallet connection is separate from application auth. Not wallet-only. |
| **Repository structure** | Monorepo | Directories: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/`. |

### 4.2 Actor Model

**DECISION:** Six actors are confirmed.

| Actor ID | Role Name | Access Type |
|---|---|---|
| ACT-01 | Platform Admin | Authenticated (JWT). System-level user management and configuration. |
| ACT-02 | Government Admin | Authenticated (JWT). Project creation, budget allocation, fund release approvals. |
| ACT-03 | Department Officer / Engineer | Authenticated (JWT). Milestone management, physical progress recording, contractor submission verification. |
| ACT-04 | Contractor | Authenticated (JWT). Fund release requests, evidence document uploads to IPFS. |
| ACT-05 | Auditor | Authenticated (JWT). Reviews AI-flagged anomalies, records audit findings on-chain. |
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

**DECISION:** A backend-server-side blockchain event listener/indexer syncs on-chain events to PostgreSQL. The front-end client does NOT directly query the blockchain for normal application views.

Data flow:
```
Blockchain
  -> FastAPI backend server event listener
  -> PostgreSQL (BlockchainEvent table)
  -> React/Vite front-end client (via API)
```

### 4.6 Auditor Blockchain Writes

**DECISION:** Auditors can record audit findings on-chain. The blockchain stores the tamper-evident reference/event (AuditFindingRecorded), not the full audit report. Detailed audit reports are stored off-chain on IPFS; their CID is referenced in the on-chain event.

### 4.7 AI Execution

**DECISION:** AI scoring is event-triggered - analysis runs after important project/fund lifecycle events (e.g., after a fund release is approved, after a milestone verification is submitted). A nightly batch infrastructure is NOT built unless later justified.

### 4.8 IPFS Document Privacy

**DECISION:** Documents uploaded to IPFS are unencrypted for this college-project version. This limitation is explicitly acknowledged in the documentation. In a real system, sensitive contractor documents would require encryption and access control before upload.

### 4.9 Scope Exclusions (Hard)

The following are explicitly out of scope and must not be introduced:
- Kubernetes or container orchestration
- Unnecessary microservices (one backend server)
- Enterprise IAM
- Custom blockchain networks
- Real government payment integration
- Real Aadhaar or government financial system integration
- Deep learning models (unless later justified)
- Mobile applications
- Excessive DevOps infrastructure

---

## 5. Target Actors

**DECISION:** Six actors are confirmed. See Section 4.2 for the full table.

### 5.1 Actor Responsibilities (Detailed)

| Actor | Key Capabilities |
|---|---|
| Platform Admin | User account management, role assignment, system configuration. Not involved in project fund workflows. |
| Government Admin | Create projects, allocate budgets, define project parameters, review and approve/reject fund release requests, view AI risk dashboards, escalate anomalies to Auditor. |
| Department Officer / Engineer | Manage assigned projects, define milestones, record physical progress updates, review contractor submissions, submit verification/inspection reports, recommend approval or rejection. |
| Contractor | View assigned milestones, submit fund release requests with evidence (invoices, bills, site photos uploaded to IPFS), resubmit rejected requests, monitor request status, receive notifications. |
| Auditor | View AI-flagged anomaly queue, inspect anomaly details (risk score, contributing factors, explanation), access linked IPFS evidence, access blockchain audit trail, record audit findings (on-chain reference + IPFS report). |
| Citizen / Public | Browse active and completed projects (public portal, no login), view project summaries (budget, disbursed amount, physical progress, timeline), view major fund events timeline (non-sensitive data only). |

---

## 6. Preliminary User Journeys

These are preliminary and will be formalized in the FRD.

### 6.1 Government Admin Journey

1. Log in (JWT authentication).
2. Create a new infrastructure project (name, category, district/region, total budget, start/end dates).
3. Allocate project to a Department Officer / Engineer.
4. View project dashboard (physical progress vs. financial utilization, AI risk score, active flags).
5. Review fund release requests submitted for approval.
6. Approve or reject fund release requests (approval/rejection event recorded on-chain).
7. Review AI-flagged anomaly summaries.
8. Escalate serious anomalies to an Auditor.

### 6.2 Department Officer / Engineer Journey

1. Log in and view assigned projects.
2. Define project milestones (name, deliverable description, budget allocation, due date) - MilestoneDefined recorded on-chain.
3. Record physical progress updates for a milestone (entered manually, verified by officer).
4. Review contractor fund release submissions.
5. Submit verification/inspection report (IPFS upload, CID recorded on-chain via OfficerVerified event).
6. Recommend approval or rejection of a fund release request.

### 6.3 Contractor Journey

1. Log in and view assigned project milestones.
2. Submit a fund release request against a milestone (partial or full milestone amount).
3. Upload supporting evidence (invoices, bills, site photographs) to IPFS via the platform - CIDs recorded on-chain via FundReleaseRequested event.
4. Monitor fund release request status (pending / under review / approved / rejected).
5. Receive in-app notification on approval or rejection (with reason).
6. If rejected, resubmit with revised evidence. Previous rejected request remains in the record.

### 6.4 Auditor Journey

1. Log in and view the AI anomaly/flag queue (sorted by risk score).
2. Inspect anomaly details: anomaly type, risk score, contributing factors (rule-based explanation), supporting data.
3. Access linked IPFS evidence documents directly from the flag view.
4. Access the full on-chain audit trail for the flagged project.
5. Record an audit finding: write a detailed audit report (uploaded to IPFS), submit AuditFindingRecorded event on-chain with IPFS CID reference.
6. Mark flag status: "Reviewed - No Action Required" or "Escalated to Authorities".

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

**DECISION:** The following lifecycle reflects confirmed decisions including partial releases, contractor resubmission, and the physical/financial progress distinction.

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
[Physical Progress Updated by Officer]
       | -> stored in PostgreSQL (ProjectProgress table)
       v
[Contractor Submits Fund Release Request]
       | -> evidence uploaded to IPFS (invoices, photos, bills)
       | -> FundReleaseRequested event recorded on-chain (includes IPFS CIDs)
       v
[Officer Reviews and Submits Verification Report]
       | -> report uploaded to IPFS
       | -> OfficerVerified event recorded on-chain (includes IPFS CID)
       v
[Government Admin Approves / Rejects]
       | -> FundReleaseApproved or FundReleaseRejected recorded on-chain
       v
[If Rejected: Contractor May Resubmit]
       | -> previous rejected request retained in record
       | -> new FundReleaseRequested event recorded on-chain
       v
[If Approved: Fund Release Recorded]
       | -> financial utilization updated in PostgreSQL
       v
[AI Risk Analysis Triggered]
       | -> event-triggered after approval/verification events
       | -> compares physical progress % vs financial utilization %
       | -> checks budget trajectory, delay risk, invoice patterns
       v
[Anomaly Detected?]
      |-- No  -> Normal continuation
      |-- Yes -> AIAnomalyRecorded event recorded on-chain
                    | -> AI flag with risk score and contributing factors
                    v
             [Auditor Reviews Flag]
                    | -> inspects evidence (IPFS), audit trail (blockchain)
                    v
             [Auditor Records Audit Finding]
                    | -> audit report uploaded to IPFS
                    | -> AuditFindingRecorded event on-chain (IPFS CID)
                    v
             [Human Decision / Escalation outside platform]
                    v
[Project Completion / Settlement]
       | -> ProjectCompleted event recorded on-chain
       v
[Project Archived]
```

**OPEN QUESTION (OQ-A):** What exactly triggers the "missed milestone deadline" state - is it a system-generated alert (backend server checks date against today) or an AI flag, or both? The mechanism needs to be specified in the FRD.

**OPEN QUESTION (OQ-B):** Is there a formal "Government Admin escalates to Auditor" action that creates a system record and notification, or is escalation purely informal (e.g., outside the platform)?

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

### 8.3 Solidity Event vs. State Storage

**OPEN QUESTION (OQ-C):** Should on-chain records be implemented as Solidity `event` emissions (cheaper, permanent in logs but not directly queryable without a node) or as persistent contract state mappings (more expensive, but directly queryable)? This is a smart contract design trade-off to be resolved in the TRD/Blockchain Design document.

**Note:** Given that the backend server runs an event listener that indexes events to PostgreSQL, Solidity events are likely sufficient for most use cases. This should be confirmed during smart contract design.

### 8.4 What Stays Off-Chain (PostgreSQL or IPFS)

| Data | Storage |
|---|---|
| User profile data (names, contacts, credentials) | PostgreSQL |
| Full project descriptions and notes | PostgreSQL |
| Milestone details and progress tracking | PostgreSQL |
| Financial time-series data for dashboards | PostgreSQL |
| AI feature vectors, model state, training data | PostgreSQL / `ml/` layer |
| Internal application workflow state | PostgreSQL |
| Document files (invoices, photos, reports) | IPFS |
| Detailed audit reports | IPFS |
| AI flag explanations and contributing factors | PostgreSQL |

### 8.5 Blockchain Network

**DECISION:** Local Hardhat network is the primary development and viva/demo environment. The platform must be fully demonstrable without a live public testnet. Ethereum Sepolia is an optional stretch deployment only, not a dependency.

---

## 9. IPFS Responsibility

### 9.1 Justified Purpose

**DECISION:** IPFS (via Pinata API) stores off-chain project evidence documents. The FastAPI backend server handles all IPFS uploads; the front-end client does not directly upload to IPFS. Pinned CIDs are stored in PostgreSQL (Document table) and referenced in on-chain events.

**REQUIREMENT:** IPFS is not a blockchain. A CID is a content address. Persistence depends on pinning via Pinata.

### 9.2 Document Types Stored on IPFS

| Document Type | Uploaded By | Triggered By |
|---|---|---|
| Contractor invoices | Contractor (via backend server) | Fund release request |
| Site photographs | Contractor (via backend server) | Fund release request |
| Bill of materials | Contractor (via backend server) | Fund release request |
| Progress report | Contractor / Officer (via backend server) | Milestone submission |
| Officer inspection / verification report | Department Officer (via backend server) | Verification step |
| Audit report | Auditor (via backend server) | Audit finding |

### 9.3 Privacy Limitation (Documented)

**DECISION:** Documents uploaded to IPFS are unencrypted for this college-project version. Any person who knows a CID can access the document via a public IPFS gateway. This is acknowledged as a limitation: in a production system, sensitive contractor documents would require encryption and/or access-controlled storage before IPFS upload. This limitation must be explicitly stated in the TRD and Security Design documents.

### 9.4 Document Retention

**DECISION:** A formal document retention/lifecycle policy is out of scope for this project version. Pinata pinning will be maintained for the lifetime of the demo. This is explicitly documented as a limitation.

---

## 10. AI Responsibility

### 10.1 Justified Purpose

**REQUIREMENT:** The AI/ML component identifies anomalous or high-risk patterns in project and fund data that may warrant human investigation. It does NOT prove fraud, corruption, guilt, or wrongdoing. All final decisions are made by humans (Auditors, Government Admins).

### 10.2 Confirmed AI Capabilities (Initial Scope)

**DECISION:** The following AI capabilities are confirmed for the initial implementation:

| Capability | Approach | Signal Type |
|---|---|---|
| Financial utilization vs. physical progress mismatch | Rule-based threshold + scoring | Deterministic |
| Budget overrun risk | Trajectory-based rule (spend rate vs. remaining budget/time) | Deterministic |
| Project delay risk | Date-based rule (milestone deadlines vs. current date / submission history) | Deterministic |
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

The AI module runs within the FastAPI backend server process. No separate inference server or microservice is created.

### 10.5 AI Explainability

**DECISION:** Risk results presented to Auditors must show:
- The overall risk score (0-100 integer scale, PROPOSAL).
- The contributing factors (e.g., "Financial utilization: 82% vs Physical progress: 25%").
- The rule or pattern that generated the flag (e.g., "Mismatch threshold exceeded by 35 percentage points").

SHAP values may be used for the Isolation Forest model if feasible; rule-based explanations are sufficient for deterministic signals.

### 10.6 Data Strategy

**DECISION:** AI will be trained and demonstrated on synthetic/semi-synthetic data modelled on realistic public fund management scenarios. Real government data is unavailable. This limitation must be clearly documented in the AI/ML Design document and acknowledged in the viva.

**OPEN QUESTION (OQ-D):** What is the target false-positive rate strategy? Too many false flags will render the auditor queue useless. A threshold and calibration strategy must be defined in the AI/ML Design document.

### 10.7 Data Storage (AI-Related)

| Data | Storage |
|---|---|
| AI feature vectors | PostgreSQL |
| AI flag records (score, type, status, explanation) | PostgreSQL (AIFlag table) |
| Synthetic training data | `data/` directory in monorepo |
| Trained model artifacts | `ml/` directory in monorepo (or loaded at backend server startup) |
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
| Milestone | Project milestones, budget portions, due dates, status, physical progress % |
| FundRelease | Fund release requests (all versions including rejected), amounts, status, submission history |
| Document | IPFS document references (CID, document type, uploader, timestamp, linked entity) |
| BlockchainEvent | Backend-server-indexed on-chain events for fast front-end querying |
| AIFlag | AI-generated anomaly flags (type, risk score, contributing factors, status, auditor action) |
| AuditFinding | Auditor decisions linked to AI flags, IPFS audit report CID |
| ProjectProgress | Physical progress records per milestone over time (officer-entered, timestamped) |
| Notification | In-app notification queue (for approval/rejection/flag events) |

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
                                                  | event listener
                                                  v
                                         +------------------+
                                         | PostgreSQL       |
                                         | (BlockchainEvent |
                                         |  index table)    |
                                         +------------------+
```

---

## 12. UI/UX Expectations

### 12.1 Technology Decisions

**DECISION:** Front-end client uses React + Vite + Tailwind CSS + shadcn/ui with a custom design system built on top.

### 12.2 Design Requirements

**REQUIREMENT:** The final product must look professional, modern, and premium. UI/UX is not an afterthought.

| Category | Requirement |
|---|---|
| Visual Quality | Premium dashboard-oriented design. Not a bare-bones admin panel. |
| Typography | Professional font stack (e.g., Inter, Plus Jakarta Sans). |
| Color System | Cohesive, curated color palette. Not raw Tailwind defaults. Custom tokens defined in the design system. |
| Spacing | Consistent spatial rhythm using the design system. |
| Hierarchy | Clear visual hierarchy on all views. |
| Dashboards | Role-specific dashboards with real data visualization (charts, KPIs, risk indicators). |
| Charts | Budget vs. actual spend, physical vs. financial progress, risk score trends. |
| Tables | Sortable, filterable project and fund data tables. |
| Forms | Validated, well-labelled forms for project creation, fund release submissions. |
| States | Loading, empty, error, and success states on all interactive views. |
| Responsive | Must function on desktop; mobile-responsiveness is a bonus. |
| Role-Specific | Each actor sees only what is relevant to their role. |
| Dark Mode | OPEN QUESTION (OQ-E): Is dark mode in scope? |

### 12.3 Key Views (Preliminary)

| View | Primary Actor(s) |
|---|---|
| Platform Admin Dashboard | Platform Admin |
| Government Admin Dashboard (project overview, budget KPIs, AI flag count, approval queue) | Government Admin |
| Project Detail View (role determines visible actions) | All authenticated roles |
| Fund Release Workflow View (submission -> verification -> approval) | Contractor, Officer, Government Admin |
| Auditor Anomaly Queue (AI flags, risk scores, contributing factor explanations) | Auditor |
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
| React/Vite front-end client | Yes | Mobile native apps |
| FastAPI backend server | Yes | Microservices architecture |
| Role-based access control (JWT) | Yes | Enterprise SSO / OAuth provider |
| Blockchain event anchoring (local Hardhat) | Yes | Custom consensus mechanism |
| Ethereum Sepolia testnet deployment | Stretch only | Mainnet deployment |
| IPFS document upload/retrieval (Pinata) | Yes | Encrypted document access control |
| AI anomaly detection (hybrid) | Yes | Deep learning models |
| AI - rule-based signals | Yes | Real-time streaming fraud alerts |
| AI - Isolation Forest | Yes | |
| AI - XGBoost | Stretch only | |
| Synthetic training data | Yes | Live government data ingestion |
| Public citizen portal (no login) | Yes | Full open government data API |
| In-app notification system | Yes (PROPOSAL) | SMS, push, email notifications |
| Auditor on-chain finding recording | Yes | |
| Partial milestone fund releases | Yes | |
| Contractor resubmission of rejected requests | Yes | |
| Physical vs. financial progress tracking | Yes | |
| PDF export of reports | Stretch goal | |
| Unit and integration tests | Yes | Full end-to-end automated test suite |
| Kubernetes / container orchestration | No | |
| Real government payment integration | No | |
| Aadhaar / government identity integration | No | |

---

## 14. Confirmed Requirements

Requirements that are explicitly stated, approved, or derived from finalized decisions.

| ID | Requirement |
|---|---|
| CR-01 | The system tracks public infrastructure project funds through a defined lifecycle. |
| CR-02 | Blockchain provides a tamper-evident audit trail for important fund events (10 defined event types). |
| CR-03 | IPFS (Pinata) stores supporting documents; CIDs are anchored on-chain and referenced in PostgreSQL. |
| CR-04 | AI/ML detects anomalous/high-risk patterns; it does not prove fraud, corruption, or wrongdoing. |
| CR-05 | PostgreSQL stores all operational/queryable application data. Blockchain does NOT replace PostgreSQL. |
| CR-06 | Six actor roles: Platform Admin, Government Admin, Department Officer/Engineer, Contractor, Auditor, Citizen. |
| CR-07 | Citizen access is public/no-login. All other roles require JWT authentication. |
| CR-08 | The product must be fully functional and demonstrable end-to-end. |
| CR-09 | The UI must be modern, professional, and visually polished (React/Vite + Tailwind + shadcn/ui). |
| CR-10 | Documentation-first development process (PRD -> FRD -> TRD -> Architecture -> Implementation -> Development). |
| CR-11 | No production-scale complexity (no Kubernetes, no microservices, no enterprise IAM, no real payment integration). |
| CR-12 | Large documents must not be stored directly on-chain. Documents go to IPFS; CIDs go on-chain. |
| CR-13 | AI must provide explainable risk indicators showing contributing factors, not opaque verdicts. |
| CR-14 | Physical progress % and financial utilization % are separate, explicitly tracked concepts. Physical progress is entered/verified by the Department Officer. |
| CR-15 | Partial fund releases per milestone are supported. This is tracking, not actual payment processing. |
| CR-16 | Contractors can resubmit rejected fund release requests. Rejected requests are permanently retained in the record. |
| CR-17 | Auditors can record audit findings on-chain (AuditFindingRecorded event with IPFS CID of audit report). |
| CR-18 | A backend-server-side blockchain event listener indexes on-chain events to PostgreSQL. The front-end client does not directly query the blockchain for normal views. |
| CR-19 | The platform must be fully demonstrable on a local Hardhat network without depending on a live public testnet. |
| CR-20 | Application auth is JWT-based. Wallet connection is separate and required only for blockchain-writing operations. |
| CR-21 | AI is event-triggered (not nightly batch). The AI module runs within the FastAPI backend server. |
| CR-22 | AI data source is synthetic/semi-synthetic. This limitation is explicitly documented. |
| CR-23 | IPFS documents are unencrypted for this version. This limitation is explicitly documented. |
| CR-24 | Monorepo structure: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/`. |

---

## 15. Proposed Requirements (Pending Confirmation)

| ID | Proposed Requirement | Rationale |
|---|---|---|
| PR-01 | In-app notification system for workflow events (approval, rejection, new AI flag) | Core workflow UX; essential without email infrastructure. |
| PR-02 | Risk score history chart per project over time | Auditor needs trend visibility, not just current score. |
| PR-03 | Document preview in-browser (PDF/image viewer) | IPFS documents should be inspectable without download. |
| PR-04 | Missed milestone deadline detection and alert generation | OQ-A must be resolved first; then this can be confirmed. |
| PR-05 | Formal Government Admin -> Auditor escalation action with system record | OQ-B must be resolved first. |
| PR-06 | Dark mode support | OQ-E must be resolved first. |
| PR-07 | PDF export of audit reports | Stretch goal; low priority. |
| PR-08 | Ethereum Sepolia testnet deployment | Stretch goal; not required for primary demo. |
| PR-09 | XGBoost risk classifier | Stretch goal; contingent on synthetic data quality. |

---

## 16. Assumptions

| ID | Assumption | Status | Risk if Wrong |
|---|---|---|---|
| AS-01 | The primary demo environment is a developer laptop with local Hardhat network. | Consistent with DECISION | Low |
| AS-02 | Real government fund data is unavailable. Synthetic data is used for AI. | Consistent with DECISION | Low; must be documented for viva |
| AS-03 | MetaMask (or compatible browser wallet) will be required for blockchain-writing operations in the demo. | Active assumption | Medium; requires evaluators to have MetaMask installed |
| AS-04 | A single FastAPI backend server process is sufficient; no microservices needed. | Consistent with DECISION | Low |
| AS-05 | The platform is a web application accessible via a modern browser. | Consistent with DECISION | Low |
| AS-06 | English is the primary UI language. Localisation is out of scope. | Active assumption | Low |
| AS-07 | Pinata free tier is sufficient for the volume of documents generated in a college demo. | Active assumption | Low; can upgrade tier if needed |
| AS-08 | The AI module's synthetic training data can be designed to produce realistic and non-trivial anomaly detection results. | Active assumption | Medium; requires careful data design |

---

## 17. Open Questions

Only genuinely unresolved items that require decisions before the dependent design or implementation can proceed.

| ID | Question | Blocks | Priority |
|---|---|---|---|
| OQ-A | What exactly triggers the "missed milestone deadline" state? Backend server scheduled check? AI flag? System alert? Both? | PR-04, FRD workflow definition | High |
| OQ-B | Is the "Government Admin escalates to Auditor" action a formal system action (creates a record, sends notification) or purely informal/out-of-platform? | PR-05, FRD workflow definition | Medium |
| OQ-C | Should on-chain records use Solidity `event` emissions or persistent contract state mappings (or both)? | Smart contract design (TRD) | High |
| OQ-D | What false-positive rate target/strategy will be used for AI flags? How will the detection threshold be calibrated? | AI/ML Design document | High |
| OQ-E | Is dark mode in scope for the front-end client? | Front-end client design, UI/UX spec | Low |

---

## 18. Scope Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| SR-01 | Pillar overload: Integrating Blockchain + IPFS + AI + Web in one timeline is ambitious. Incomplete integration of any pillar will weaken the viva evaluation. | Medium | High | Phase integration carefully. Each pillar must have a minimal but functional demo path before polish work begins. |
| SR-02 | AI without realistic data: If synthetic data is not realistic enough, anomaly detection will produce trivial or unconvincing results. | Medium | High | Design synthetic data generation carefully; document the methodology; demonstrate meaningful detection at viva. |
| SR-03 | Blockchain demo fragility: MetaMask or Hardhat node issues during viva could block the demo. | Low-Medium | High | Local Hardhat is the primary environment (no network dependency). Practice demo with full setup. Prepare fallback screenshots. |
| SR-04 | Wallet friction in demo: Evaluators or audience may not have MetaMask installed. | Medium | Medium | Prepare a demo account with pre-configured MetaMask. Consider a "read-only" demo mode for Auditor/Citizen views. |
| SR-05 | Scope creep: Adding mobile, deep learning, PDF export, real payment integration beyond MVP scope could delay delivery. | High | High | Maintain a strict MVP scope. Stretch goals are clearly labelled and not treated as requirements. |
| SR-06 | Documentation-implementation drift: If code diverges from the documents, the viva argument weakens. | Medium | Medium | Update documents deliberately whenever a decision changes. Documents are the source of truth during design phase; code is the source of truth after implementation. |
| SR-07 | UI complexity underestimated: Role-specific dashboards with charts, tables, workflows, and blockchain views take longer than expected. | Medium | Medium | UI/UX specification must be completed before major frontend implementation. Use shadcn/ui to accelerate component development. |
| SR-08 | Pinata dependency: If Pinata free tier limits are exceeded during demo, IPFS uploads will fail. | Low | Medium | Monitor Pinata usage. Prepare local IPFS fallback if needed. Keep evidence files small in demo scenarios. |

---

## 19. Finalized Architecture Decisions

This section replaces the previous "Architectural Questions" section. All decisions listed here are finalized.

| ID | Component | Decision |
|---|---|---|
| AD-01 | Front-end client | React + Vite |
| AD-02 | Front-end styling | Tailwind CSS + shadcn/ui + custom design system |
| AD-03 | Backend server | Python + FastAPI (single service) |
| AD-04 | Database | PostgreSQL |
| AD-05 | ORM / database access | To be confirmed in TRD (SQLAlchemy or similar Python ORM is expected) |
| AD-06 | Smart contract language | Solidity |
| AD-07 | Smart contract toolchain | Hardhat |
| AD-08 | Blockchain network (primary) | Local Hardhat network |
| AD-09 | Blockchain network (stretch) | Ethereum Sepolia testnet |
| AD-10 | IPFS provider | Pinata API |
| AD-11 | AI/ML approach | Hybrid: rule-based deterministic signals + Isolation Forest |
| AD-12 | AI/ML library | scikit-learn (Isolation Forest); pandas/numpy for features. XGBoost is a stretch. |
| AD-13 | AI execution model | Event-triggered within FastAPI backend server process. No separate inference service. |
| AD-14 | Authentication | JWT-based application auth + role-based authorization |
| AD-15 | Wallet integration | Separate from application auth; required for blockchain-writing operations only |
| AD-16 | Blockchain indexing | Backend server event listener syncs on-chain events to PostgreSQL |
| AD-17 | Repository structure | Monorepo: `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, `data/` |
| AD-18 | Terminology | "front-end client" (React/Vite), "backend server" (FastAPI). Not "frontend server". |

---

## 20. Recommended Next Steps

**DECISION:** No application code, scaffolding, or dependency installation should begin until the PRD is reviewed and approved.

1. **Resolve the 5 remaining Open Questions (OQ-A through OQ-E).**
   - OQ-A and OQ-C are high-priority blockers for FRD workflow definition and smart contract design respectively.
   - OQ-D is a high-priority blocker for the AI/ML Design document.

2. **Update AGENTS.md** with the confirmed monorepo structure and a note that the stack has been selected (but no toolchain commands yet, as no code exists).

3. **Begin the Product Requirements Document (PRD).**
   - The PRD formalizes: all actors (with ACT IDs), complete feature list, functional requirements (with FR IDs), non-functional requirements, and scope boundaries.
   - The PRD should trace requirements back to the confirmed requirements in this document.

4. **After PRD review, proceed to FRD, then TRD, then System Architecture.**

5. **Do not begin coding until the Implementation Plan is approved.**

---

*End of Document — v0.2.0*

*This document reflects the project baseline as of 2026-09-27. Any changes to decisions recorded here must be reflected in this document and all downstream documents that depend on them.*
