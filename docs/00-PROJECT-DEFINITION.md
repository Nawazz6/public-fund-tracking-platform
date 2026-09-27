# docs/00-PROJECT-DEFINITION.md

**Document Type:** Project Definition Analysis
**Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform
**Status:** DRAFT - Awaiting User Review
**Date:** 2026-09-27
**Author:** AI Engineering Agent
**Version:** 0.1.0

---

> **Label Key used throughout this document:**
> - **FACT** - Verified from repository, instructions, or a reliable source.
> - **REQUIREMENT** - Explicitly requested or approved.
> - **DECISION** - Explicitly accepted project decision.
> - **ASSUMPTION** - Temporarily assumed because information is missing.
> - **PROPOSAL** - Recommended but not yet approved.
> - **OPEN QUESTION** - Needs resolution before a dependent decision can be made.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Problem Understanding](#2-problem-understanding)
3. [Proposed Product](#3-proposed-product)
4. [Target Actors](#4-target-actors)
5. [Preliminary User Journeys](#5-preliminary-user-journeys)
6. [Preliminary Fund Lifecycle](#6-preliminary-fund-lifecycle)
7. [Blockchain Responsibility](#7-blockchain-responsibility)
8. [IPFS Responsibility](#8-ipfs-responsibility)
9. [AI Responsibility](#9-ai-responsibility)
10. [Database Responsibility](#10-database-responsibility)
11. [UI/UX Expectations](#11-uiux-expectations)
12. [Academic Scope](#12-academic-scope)
13. [Confirmed Requirements](#13-confirmed-requirements)
14. [Proposed Requirements](#14-proposed-requirements)
15. [Assumptions](#15-assumptions)
16. [Open Questions](#16-open-questions)
17. [Scope Risks](#17-scope-risks)
18. [Architectural Questions](#18-architectural-questions)
19. [Recommended Next Steps](#19-recommended-next-steps)
20. [Decisions Requiring User Approval](#20-decisions-requiring-user-approval)

---

## 1. Project Overview

**FACT:** The project title is: *AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform*.

**FACT:** This is a final-year Electronics and Computer Science Engineering (ECS) college project.

**REQUIREMENT:** The project must be technically substantial, realistic, well-architected, properly documented, fully demonstrable, visually polished, and actually functional. It must be strong enough to withstand final-year evaluation, viva, and presentation.

**REQUIREMENT:** The project must avoid unnecessary production-scale complexity (microservices, Kubernetes, enterprise IAM, custom blockchain infrastructure, excessive DevOps) while also avoiding artificial simplification.

**REQUIREMENT:** The project integrates four core technical pillars:
- A conventional database layer
- Blockchain for tamper-evident auditability
- IPFS for decentralized document/evidence storage
- AI/ML for anomaly and risk detection

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
| Fraud and misappropriation | Funds may be released without proportional physical progress; invoices may be duplicated or inflated. |
| Reactive auditing | Audits happen after the fact, often years later, making remediation difficult. |
| No structured anomaly detection | Anomalies in spending patterns, progress mismatches, and unusual contractor behaviour go undetected without manual investigation. |

### 2.2 What the System Is NOT Intended to Do

- **REQUIREMENT:** AI must NOT be described as proving corruption. AI identifies patterns warranting human investigation.
- The system is not a payment processing platform; it tracks and records fund events rather than executing actual financial transactions.
- The system is not a replacement for legal or judicial proceedings.

---

## 3. Proposed Product

**PROPOSAL:** A web-based platform that enables:

1. **Project Management** - Government administrators can create public infrastructure projects with budgets, timelines, and milestone definitions.
2. **Fund Lifecycle Tracking** - Every significant fund event (allocation, release request, approval, utilization report) is recorded with an on-chain audit trail.
3. **Evidence Management** - Contractors upload supporting documents (invoices, site photographs, progress reports) stored on IPFS; document content identifiers (CIDs) are anchored on-chain.
4. **AI-Assisted Risk Detection** - An ML layer continuously analyses project data and surfaces anomalies, mismatches, and high-risk patterns to auditors.
5. **Role-Based Dashboards** - Different actors see contextually relevant views, charts, and action queues.
6. **Public Transparency View** - ASSUMPTION: a read-only, publicly accessible view of non-sensitive project information to demonstrate transparency.

---

## 4. Target Actors

The following actors are **proposed** based on the instructions. They require validation.

| Actor ID | Role Name | Proposed Responsibilities |
|---|---|---|
| ACT-01 | Government / Municipal Administrator | Create projects, allocate budgets, approve/reject milestone fund releases. |
| ACT-02 | Department Officer / Engineer | Manage project execution, set milestones, review contractor submissions, submit verification reports. |
| ACT-03 | Contractor | Submit fund release requests against milestones, upload evidence documents to IPFS. |
| ACT-04 | Auditor | Review flagged anomalies from the AI system, conduct manual inspections, issue audit findings. |
| ACT-05 | Citizen / Public | Read-only view of project status, fund utilization summary, and major events (non-sensitive data). |

### 4.1 Actor Validation Questions

**OPEN QUESTION (OQ-01):** Should citizens require registration/login, or should the citizen view be fully public without authentication?

**OPEN QUESTION (OQ-02):** Is there an internal system administrator role (user management, system configuration) distinct from the Government Administrator actor?

**OPEN QUESTION (OQ-03):** Can one person hold multiple roles (e.g., an administrator who is also an auditor)? If so, how are permissions handled?

**OPEN QUESTION (OQ-04):** Is the contractor always an external entity, or can a government department act as its own contractor?

**OPEN QUESTION (OQ-05):** Does the auditor have write access to the blockchain (e.g., recording audit findings on-chain), or are they read-only consumers?

---

## 5. Preliminary User Journeys

These are preliminary and require validation. They will be formalized in the FRD.

### 5.1 Administrator Journey

1. Log in to the platform.
2. Create a new infrastructure project (name, type, district, total budget, start/end dates).
3. Allocate project to a department/officer.
4. Review and approve/reject milestone fund release requests.
5. View project dashboard (progress vs. expenditure, AI risk score, alerts).
6. Review AI-flagged anomalies.
7. Escalate serious anomalies to the Auditor.

### 5.2 Department Officer / Engineer Journey

1. Log in and view assigned projects.
2. Define project milestones (milestone name, expected deliverable, budget portion, due date).
3. Review contractor submissions against milestones.
4. Submit verification/inspection report with on-site findings.
5. Recommend approval or rejection of a fund release.

### 5.3 Contractor Journey

1. Log in and view assigned project milestones.
2. When a milestone is ready, submit a fund release request.
3. Upload supporting evidence (invoices, bills, photos) to IPFS via the platform.
4. Monitor the status of submitted requests.
5. Receive notifications on approval or rejection (with reasons).

### 5.4 Auditor Journey

1. Log in and view AI-flagged anomaly queue.
2. Inspect anomaly details (what triggered the flag, supporting data, risk score, explanation).
3. Access linked evidence documents (IPFS).
4. Access the blockchain audit trail for the project.
5. Record audit finding and decision.
6. Escalate to human authorities outside the platform (out of system scope).

### 5.5 Citizen Journey

1. Visit the public portal (no login required - ASSUMPTION).
2. Browse active and completed infrastructure projects by region or type.
3. View project summary: budget, total disbursed, physical progress, timeline.
4. View major fund events timeline (anonymized or aggregated where sensitive).
5. View any public anomaly/audit summaries published by the administrator.

---

## 6. Preliminary Fund Lifecycle

```
[Project Created]
       |
       v
[Budget Allocated on-chain]
       |
       v
[Milestones Defined]
       |
       v
[Contractor Submits Fund Release Request]
       |    -> uploads evidence -> IPFS -> CID recorded on-chain
       v
[Officer Reviews and Verifies]
       |    -> submits verification report
       v
[Administrator Approves / Rejects]
       |    -> approval/rejection event recorded on-chain
       v
[Fund Released (recorded on-chain)]
       |
       v
[AI Risk Analysis]
       |    -> continuous; runs on project data, spending, progress
       v
[Anomaly Detected?]
      |-- No  -> Normal continuation
      |-- Yes -> AI Risk Flag
                    |
                    v
             [Auditor Review]
                    |
                    v
             [Audit Finding Recorded]
                    |
                    v
             [Human Decision / Escalation]
                    |
                    v
[Project Completion / Settlement]
       |    -> final settlement event recorded on-chain
       v
[Project Archived]
```

**OPEN QUESTION (OQ-06):** Is partial fund release per milestone supported (e.g., 60% on interim verification, 40% on final completion)?

**OPEN QUESTION (OQ-07):** Should rejected fund release requests be resubmittable by the contractor? With what constraints?

**OPEN QUESTION (OQ-08):** What happens when a milestone deadline passes without a submission? Is this a system alert, an automatic AI flag, or a manual process?

**OPEN QUESTION (OQ-09):** Does the platform record physical progress percentages separately from financial utilization percentages? This distinction is critical for the AI mismatch analysis.

---

## 7. Blockchain Responsibility

### 7.1 Justified Purpose

**REQUIREMENT:** Blockchain provides a tamper-evident, auditable record of important project and fund events. It does NOT store every database field.

**REQUIREMENT:** Large documents must NOT be stored directly on-chain.

The blockchain's role is anchoring - recording that a specific event occurred at a specific time with specific identifiers, in a way that cannot be silently altered.

### 7.2 Proposed On-Chain Events

| Event | On-Chain Data |
|---|---|
| Project Created | Project ID, administrator address, creation timestamp, total budget |
| Budget Allocated | Project ID, allocation amount, timestamp, allocating authority address |
| Milestone Defined | Project ID, milestone ID, milestone budget, due date hash |
| Fund Release Requested | Project ID, milestone ID, contractor address, requested amount, IPFS CID of evidence bundle |
| Officer Verification Submitted | Project ID, milestone ID, officer address, verification outcome, verification report CID |
| Fund Release Approved | Project ID, milestone ID, approving authority address, approved amount, timestamp |
| Fund Release Rejected | Project ID, milestone ID, rejecting authority address, reason hash, timestamp |
| AI Anomaly Flagged | Project ID, flag ID, risk score, anomaly type hash, timestamp |
| Audit Finding Recorded | Project ID, flag ID, auditor address, finding summary hash, timestamp |
| Project Completed | Project ID, completion timestamp, final utilization amount |

**OPEN QUESTION (OQ-10):** Should all events be emitted as Solidity events (cheaper, not permanently queryable without a node) or persisted as contract state (more expensive, but directly queryable)? This is a design trade-off to evaluate.

**OPEN QUESTION (OQ-11):** Which blockchain network is most appropriate?
- Ethereum Sepolia testnet (widely supported, mature tooling)
- Polygon Mumbai / Amoy testnet (lower gas, EVM-compatible)
- A local Hardhat or Anvil network (no external dependency, ideal for demo)
- A public testnet + local fallback (best of both)

**OPEN QUESTION (OQ-12):** Should actor addresses on-chain be wallet addresses (MetaMask) or system-generated identifiers mapped to wallet addresses?

### 7.3 What Stays Off-Chain

- All user profile data (names, contact details, credentials)
- Full document contents
- Detailed project descriptions and notes
- Queryable financial time-series data used for dashboards
- AI feature vectors and training data
- Internal application state and workflow status

---

## 8. IPFS Responsibility

### 8.1 Justified Purpose

**REQUIREMENT:** IPFS stores appropriate off-chain project documents and evidence such as invoices, bills, progress reports, inspection documents, and photographs.

**REQUIREMENT:** The blockchain records the corresponding IPFS CID and metadata as an anchor, enabling independent verification that the referenced document has not been altered.

### 8.2 Document Types Proposed for IPFS

| Document Type | Actor | Triggered By |
|---|---|---|
| Contractor invoices | Contractor | Fund release request |
| Site photographs | Contractor | Fund release request |
| Bill of materials | Contractor | Fund release request |
| Progress report | Contractor / Officer | Milestone submission |
| Officer inspection report | Department Officer | Verification step |
| Audit report | Auditor | Audit finding |

### 8.3 Important IPFS Considerations

**REQUIREMENT:** IPFS is not a blockchain. A CID is a content address, not a location guarantee. Persistence requires pinning.

**OPEN QUESTION (OQ-13):** Which IPFS solution should be used?
- Pinata API: Managed pinning service with a free tier; simplest to integrate; no local daemon required.
- Web3.Storage / Storacha: NFT.storage successor; good free tier; suitable for academic use.
- Local Kubo/IPFS node: No external dependency; good for offline demo; requires local setup.
- Helia (JS-native IPFS): Browser/Node-native; newer; less mature.

**OPEN QUESTION (OQ-14):** Should documents on IPFS be publicly accessible (unencrypted) or encrypted before upload? This has privacy implications - contractor invoices may contain commercially sensitive information.

**OPEN QUESTION (OQ-15):** Should a document retention or lifecycle policy be defined (even academically), or is it out of scope?

---

## 9. AI Responsibility

### 9.1 Justified Purpose

**REQUIREMENT:** The AI component identifies anomalous or high-risk patterns in project and fund data that may warrant human investigation. It does NOT prove fraud or corruption.

### 9.2 Candidate AI Capabilities

The following capabilities are candidates, not approved requirements. Each must be evaluated for feasibility, data availability, and genuine value.

| Capability | Description | Value | Data Needed |
|---|---|---|---|
| Spending vs. Progress Mismatch | Detect when financial utilization is disproportionately high relative to physical progress | High | Physical progress %, financial utilization % |
| Budget Overrun Risk | Detect trajectories heading toward overrun before they occur | High | Spending time series, milestone timelines |
| Delay Risk | Flag projects with repeated milestone delays | Medium | Planned vs. actual dates, delay history |
| Duplicate / Near-Duplicate Invoice Detection | Flag invoices with similar amounts, vendors, and dates across projects | High | Invoice metadata, OCR data |
| Unusual Transaction Pattern | Statistical outlier detection on transaction amounts, timing, or frequency | Medium | Transaction records |
| Contractor Behaviour Analysis | Identify contractors with repeated anomalies across multiple projects | Medium | Historical contractor data |
| Document Authenticity | Detect tampered or suspicious documents via image/metadata analysis | Medium-High | Raw document files |

### 9.3 AI Pipeline Design Questions

**OPEN QUESTION (OQ-16):** What data will the AI train and run on? Real government data is not available. The most realistic option is synthetic/semi-synthetic data modelled on realistic fund-management scenarios. This limitation must be documented.

**OPEN QUESTION (OQ-17):** Should the AI model be:
- A rule-based scoring engine (interpretable, easier to implement, lower risk of spurious results)
- A classical ML model (Isolation Forest, XGBoost, Random Forest)
- A combination (rule-based for some signals, ML for others)
- A deep learning model (highest complexity; probably disproportionate for this scope)

**OPEN QUESTION (OQ-18):** Should AI scoring be batch (e.g., nightly re-scoring of all active projects) or triggered (e.g., after each fund event)?

**OPEN QUESTION (OQ-19):** How should the AI explain its flags to auditors? Explainability (e.g., SHAP values, rule descriptions, feature importance) is important for credibility at viva.

**OPEN QUESTION (OQ-20):** What is the acceptable false-positive rate? Too many false flags will make the auditor dashboard useless. This must be addressed in the ML design.

### 9.4 Minimum Viable AI Scope (PROPOSAL)

As a practical minimum, the AI component should implement at minimum:
1. Spending vs. physical progress mismatch scoring (high value, explainable, demonstrable)
2. Budget overrun risk scoring (trajectory-based)
3. Delay risk detection (date-based)

If time permits, duplicate invoice detection can be added as a stretch goal.

---

## 10. Database Responsibility

### 10.1 Justified Purpose

**REQUIREMENT:** A conventional database is needed for all queryable, relational, high-frequency, and operational application data. The blockchain does NOT replace the database.

### 10.2 Proposed Database Entities (Preliminary)

| Entity | Description |
|---|---|
| User | Platform users (all roles), credentials, role assignment |
| Project | Infrastructure project metadata, budget, status, dates |
| Milestone | Project milestones, budget portions, due dates, status |
| FundRelease | Fund release requests, amounts, timestamps, status |
| Document | IPFS document references (CID, type, uploader, timestamp) |
| BlockchainEvent | Off-chain index of on-chain events for fast querying |
| AIFlag | AI-generated anomaly flags (type, score, explanation, status) |
| AuditFinding | Auditor decisions linked to AI flags |
| ProjectProgress | Physical progress records over time (for AI feature computation) |
| Notification | In-app notification queue |

### 10.3 Relationship Between Data Layers

```
+-----------------------+   CID reference   +------------------+
|   IPFS                |<------------------| Database         |
| (documents/files)     |                   | (operational     |
+-----------------------+                   |  data)           |
                                            +--------+---------+
                                                     | key events
                                                     v
                                            +------------------+
                                            | Blockchain       |
                                            | (audit trail /   |
                                            |  anchoring)      |
                                            +------------------+
```

**OPEN QUESTION (OQ-21):** Should a dedicated blockchain indexer/listener sync on-chain events back to the database for fast querying, or will the frontend query the chain directly for audit trail views?

---

## 11. UI/UX Expectations

**REQUIREMENT:** The final product must look professional and appealing. UI/UX is not an afterthought.

### 11.1 Design Requirements

| Category | Requirement |
|---|---|
| Visual Quality | Modern, premium design. Not a bare-bones admin panel. |
| Typography | Professional font stack (e.g., Inter, Plus Jakarta Sans). |
| Color System | Cohesive color palette. Not raw browser defaults. |
| Spacing | Consistent spatial rhythm. |
| Hierarchy | Clear visual hierarchy on all views. |
| Dashboards | Role-specific dashboards with real data visualization (charts, KPIs). |
| Charts | Budget vs. actual, progress timelines, risk score trends. |
| Tables | Sortable, filterable project and fund data tables. |
| Forms | Validated, well-labelled forms for project creation, submissions. |
| States | Loading, empty, error, and success states on all interactive views. |
| Responsive | Must function on desktop; mobile-responsiveness is a bonus. |
| Role-Specific | Each actor sees only what is relevant to their role. |

### 11.2 Key Views (Preliminary)

- Administrator Dashboard (project overview, budget summary, AI flag count, approval queue)
- Project Detail View (all stakeholders see this; role determines what actions are available)
- Fund Release Workflow View (contractor submission to officer review to administrator decision)
- Auditor Anomaly Queue (AI-flagged items with risk scores and explanations)
- Blockchain Audit Trail View (timeline of on-chain events for a project)
- Public Citizen View (sanitized project list and summary)
- Document Viewer (IPFS-hosted documents accessible in-browser)

**OPEN QUESTION (OQ-22):** Should the UI use a component library (e.g., shadcn/ui, Chakra UI, MUI, Ant Design) or be built with a design system from scratch?

**OPEN QUESTION (OQ-23):** Should dark mode be supported?

---

## 12. Academic Scope

### 12.1 What This Is

- A **functional demonstration prototype** for final-year engineering evaluation.
- It must work end-to-end within a demo environment.
- It must be presentable in a viva and survive technical questioning.
- It must demonstrate genuine integration of all four technical pillars.

### 12.2 What This Is NOT

- Not a production government system.
- Not required to pass government security audits.
- Not required to handle real financial transactions.
- Not required to deploy at scale.
- Not required to be multi-tenant across hundreds of departments.

### 12.3 Scope Calibration

| Feature | In Scope | Out of Scope |
|---|---|---|
| Web-based platform | Yes | Mobile native apps |
| Role-based access control | Yes | Enterprise SSO / OAuth provider |
| Blockchain event anchoring | Yes | Custom consensus mechanism |
| IPFS document upload/retrieval | Yes | Encrypted document access control |
| AI anomaly detection | Yes | Real-time fraud alert streaming |
| Synthetic training data | Yes | Live government data ingestion |
| Public project browser | Yes | Full open government data API |
| Local/testnet blockchain | Yes | Mainnet deployment |
| Basic in-app notifications | Yes (PROPOSAL) | SMS, push notifications |
| PDF export of reports | Stretch goal | |
| Unit and integration tests | Yes | Full end-to-end automated test suite |
| Kubernetes / container orchestration | No | |
| Microservices | No | |

---

## 13. Confirmed Requirements

| ID | Requirement |
|---|---|
| CR-01 | The system tracks public infrastructure project funds through a defined lifecycle. |
| CR-02 | Blockchain provides a tamper-evident audit trail for important fund events. |
| CR-03 | IPFS stores supporting documents; CIDs are anchored on-chain. |
| CR-04 | AI/ML detects anomalies and high-risk patterns; it does not prove fraud. |
| CR-05 | A conventional database supports operational application data. |
| CR-06 | Role-based access: Administrator, Officer, Contractor, Auditor, Citizen. |
| CR-07 | The product must be fully functional and demonstrable end-to-end. |
| CR-08 | The UI must be modern, professional, and visually polished. |
| CR-09 | Documentation-first development process (PRD to FRD to TRD to Architecture etc.). |
| CR-10 | No production-scale complexity (no Kubernetes, no microservices, no enterprise IAM). |
| CR-11 | Large documents must not be stored directly on-chain. |
| CR-12 | AI must provide explainable risk indicators, not opaque verdicts. |
| CR-13 | Physical progress must be tracked separately from financial utilization. |

---

## 14. Proposed Requirements

| ID | Proposed Requirement | Rationale |
|---|---|---|
| PR-01 | Public read-only citizen portal (no login) | Transparency is a stated project goal. |
| PR-02 | In-app notification system for approval/rejection events | Essential for workflow without email infrastructure. |
| PR-03 | Blockchain event listener that indexes events to the database | Required for practical audit trail querying at demo speed. |
| PR-04 | Batch AI scoring triggered after each fund release event | Balance between real-time complexity and stale-data risk. |
| PR-05 | Synthetic dataset generation script for AI training and demo | No real government data available. |
| PR-06 | Risk score history chart per project | Auditor needs trend visibility, not just current score. |
| PR-07 | Document preview in-browser (PDF/image) | IPFS documents should be inspectable without download. |
| PR-08 | Contractor cannot resubmit a rejected request without officer guidance | Workflow integrity. |
| PR-09 | Auditor can mark a flag as reviewed/no-action or escalated | Needed for queue management. |
| PR-10 | All on-chain interactions require a connected wallet (MetaMask or equivalent) | Blockchain interaction mechanism. |

---

## 15. Assumptions

| ID | Assumption | Risk if Wrong |
|---|---|---|
| AS-01 | A testnet or local blockchain environment will be used; no mainnet deployment is required. | Low risk; mainnet would require real funds for gas. |
| AS-02 | Real government fund data is unavailable; synthetic data will be used for AI training. | Low risk; must be clearly documented for viva. |
| AS-03 | The citizen portal is public-facing with no authentication required. | Medium risk; may expose project metadata inappropriately. |
| AS-04 | A single backend service is sufficient (no microservices). | Low risk; aligns with scope. |
| AS-05 | The platform is a web application accessible via a modern browser. | Low risk. |
| AS-06 | The primary demo environment is a developer laptop, not a remote server. | Low risk; affects deployment instructions. |
| AS-07 | English is the primary language for the UI. Localisation is out of scope. | Low risk for academic evaluation. |
| AS-08 | MetaMask (or a compatible browser wallet) will be the blockchain interaction mechanism for privileged users. | Medium risk; requires evaluators to have MetaMask installed for live demo. |
| AS-09 | The AI model will run as part of the backend service, not as a separate inference server. | Low risk at this scale. |
| AS-10 | Document encryption before IPFS upload is out of scope for this version. | Medium risk; privacy concern for any real-world use. |

---

## 16. Open Questions

| ID | Question | Blocks |
|---|---|---|
| OQ-01 | Is the citizen view fully public (no login) or does it require registration? | PR-01, Actor model |
| OQ-02 | Is there a separate system admin role distinct from Government Administrator? | Actor model, auth design |
| OQ-03 | Can one user hold multiple roles simultaneously? | Auth/permission design |
| OQ-04 | Is the contractor always external, or can a government department self-execute? | Actor model, workflow |
| OQ-05 | Does the auditor write to the blockchain (audit findings on-chain)? | Blockchain design |
| OQ-06 | Is partial fund release per milestone supported? | Workflow design, smart contract |
| OQ-07 | Can contractors resubmit rejected fund release requests? | Workflow design |
| OQ-08 | What happens on missed milestone deadlines (alert vs. auto-flag)? | Workflow, AI design |
| OQ-09 | Are physical progress percentages entered manually or derived from documents? | Data model, AI features |
| OQ-10 | Events as Solidity events vs. persistent contract state? | Smart contract design |
| OQ-11 | Which blockchain network: local Hardhat/Anvil, Sepolia, Polygon Amoy? | Smart contract, wallet, infrastructure |
| OQ-12 | Are on-chain addresses wallet addresses or system identifiers? | Smart contract, auth design |
| OQ-13 | Which IPFS solution: Pinata, Web3.Storage, local Kubo, Helia? | IPFS integration |
| OQ-14 | Are IPFS documents public (unencrypted) or encrypted before upload? | Privacy, IPFS design |
| OQ-15 | Is a document retention policy in or out of scope? | IPFS design |
| OQ-16 | What data will the AI use? Synthetic only, or is any real data available? | AI design, data pipeline |
| OQ-17 | AI approach: rule-based, classical ML, or hybrid? | AI design, feasibility |
| OQ-18 | AI scoring trigger: batch/nightly or event-triggered? | AI design, backend design |
| OQ-19 | How does AI explain its flags to auditors? (SHAP, rules, features?) | AI explainability, UI |
| OQ-20 | What is the acceptable false positive rate strategy? | AI design |
| OQ-21 | Dedicated blockchain indexer vs. direct chain queries from frontend? | Backend architecture |
| OQ-22 | UI component library vs. custom design system? | Frontend tech stack |
| OQ-23 | Is dark mode in scope? | Frontend/UI scope |

---

## 17. Scope Risks

| ID | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| SR-01 | Pillar overload: Integrating Blockchain + IPFS + AI + Web in one timeline is ambitious. Poor integration or incomplete pillars will fail at viva. | Medium | High | Phase integration carefully; ensure each pillar has a minimal but functional demo path first. |
| SR-02 | AI without data: If synthetic data is not realistic enough, the AI anomaly detection will be trivial or unconvincing. | Medium | High | Design synthetic data generation carefully; document methodology. |
| SR-03 | Blockchain demo fragility: Live testnet calls can fail during demo (network issues, gas, latency). | Medium | High | Prioritise local Hardhat/Anvil for demo; optional testnet deployment as a stretch. |
| SR-04 | IPFS availability: Documents pinned via a third-party service may become unavailable if the service changes. | Low | Medium | Use Pinata with a secondary local IPFS node for demo resilience. |
| SR-05 | Wallet requirement for demo: Requiring MetaMask during viva adds friction and a potential point of failure. | Medium | Medium | Consider whether wallet interaction can be simulated server-side for demo mode. |
| SR-06 | Scope creep: Adding features (mobile app, PDF export, multi-language, email alerts) could delay core delivery. | High | High | Maintain a strict MVP scope with labelled stretch goals. |
| SR-07 | Documentation-implementation drift: If documentation and implementation diverge, the viva argument weakens. | Medium | Medium | Update documents deliberately whenever a decision changes. |
| SR-08 | UI complexity underestimated: Role-specific dashboards with charts, tables, and workflows take longer than expected. | Medium | Medium | Begin UI/UX specification early; use a component library to accelerate development. |

---

## 18. Architectural Questions

| ID | Question | Options | Impact |
|---|---|---|---|
| AQ-01 | Frontend Framework | React (Vite), Next.js (SSR/SSG), Vue.js | Affects routing, SSR needs, deployment |
| AQ-02 | Backend Language/Framework | Python (FastAPI, Django), Node.js (Express, Fastify), Go (Gin) | Affects AI integration, team familiarity |
| AQ-03 | Database Engine | PostgreSQL, MySQL, SQLite, MongoDB | Affects schema design, relational queries |
| AQ-04 | Smart Contract Language | Solidity (Ethereum/EVM) | Effectively the only real choice for EVM chains |
| AQ-05 | Smart Contract Dev Framework | Hardhat, Foundry, Truffle (deprecated) | Affects testing, deployment scripts |
| AQ-06 | Blockchain Network | Local Anvil/Hardhat, Sepolia, Polygon Amoy | Affects demo resilience, gas costs |
| AQ-07 | AI/ML Library | scikit-learn, XGBoost, PyTorch | Affects complexity, explainability |
| AQ-08 | IPFS Provider | Pinata, Web3.Storage, local Kubo | Affects setup complexity, availability |
| AQ-09 | Monorepo Structure | Single repo with frontend/, backend/, contracts/, ml/ | Affects tooling, CI, developer experience |
| AQ-10 | Authentication Mechanism | JWT-based (custom), session-based, Wallet-only | Affects security design, user experience |
| AQ-11 | Blockchain Indexing | Custom event listener, The Graph (hosted), direct RPC queries | Affects query speed and infrastructure |

> **Note on AQ-02:** If Python is chosen as the backend language, AI/ML integration becomes significantly simpler. A JavaScript/TypeScript backend would require calling a Python subprocess or a separate ML service. This is an important trade-off.

---

## 19. Recommended Next Steps

1. **Review and approve this Project Definition document.**
   Specifically, resolve the decisions listed in Section 20 before moving to the PRD.

2. **Resolve the highest-blocking Open Questions first:**
   - OQ-11 (Blockchain network) - affects smart contract toolchain choice.
   - OQ-02/03 (Actor/role model) - affects auth design and all user journeys.
   - AQ-01/02 (Frontend and backend framework) - affects all subsequent scaffolding.
   - OQ-13 (IPFS provider) - affects integration design.

3. **Begin the Product Requirements Document (PRD).**
   The PRD formalises: actors, feature list, functional requirements, non-functional requirements, and scope boundaries.

4. **Do not scaffold, install, or write code until the PRD is reviewed.**

---

## 20. Decisions Requiring User Approval

The following decisions materially affect the product architecture or scope. **None of these have been finalized.** User input or approval is required before proceeding.

| # | Decision | Options | Recommendation |
|---|---|---|---|
| D-01 | Blockchain network for development and demo | Local Hardhat/Anvil / Ethereum Sepolia testnet / Polygon Amoy | PROPOSAL: Local Hardhat/Anvil for primary demo; Sepolia as optional stretch. Local is more reliable for viva. |
| D-02 | Backend language and framework | Python/FastAPI / Node.js/Express / Node.js/Fastify | PROPOSAL: Python/FastAPI - simplifies AI/ML integration without a separate inference service. |
| D-03 | Frontend framework | React/Vite / Next.js / Vue.js | PROPOSAL: React/Vite - standard, well-supported, simpler than Next.js without SSR requirements. |
| D-04 | Database engine | PostgreSQL / SQLite / MongoDB | PROPOSAL: PostgreSQL - relational integrity, strong query support, well-supported with Python ORMs. |
| D-05 | IPFS provider | Pinata API / Web3.Storage / Local Kubo node | PROPOSAL: Pinata API for simplicity + local IPFS node as demo fallback. |
| D-06 | Smart contract toolchain | Hardhat (JavaScript) / Foundry (Rust-based) | PROPOSAL: Hardhat - more widely documented, JavaScript-based ecosystem. |
| D-07 | AI approach | Rule-based scoring / Classical ML (Isolation Forest, XGBoost) / Hybrid | PROPOSAL: Hybrid - rule-based for deterministic signals, Isolation Forest for pattern anomalies. |
| D-08 | UI component library | shadcn/ui / Chakra UI / MUI / Custom | PROPOSAL: shadcn/ui - modern, unstyled components with full control; good for premium custom design. |
| D-09 | Citizen portal access | Fully public (no login) / Requires registration | PROPOSAL: Fully public (no login) - aligns with transparency goal; simpler. |
| D-10 | Document privacy (IPFS) | Unencrypted (public CID) / Encrypted before upload | PROPOSAL: Unencrypted for this version with the limitation documented; encryption adds significant complexity. |
| D-11 | Auditor blockchain writes | Auditor findings recorded on-chain / Off-chain only | PROPOSAL: On-chain - strengthens the tamper-evident narrative and adds depth to the blockchain justification. |
| D-12 | Monorepo structure | Yes (single repo, multiple directories) / Separate repos per layer | PROPOSAL: Monorepo - single repo with frontend/, backend/, contracts/, ml/, docs/ directories. |

---

*End of Document*

**Next action required:** User review and approval of this Project Definition, followed by resolution of the Decisions in Section 20, before the PRD is drafted.
