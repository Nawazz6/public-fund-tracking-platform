# docs/03-TRD.md

**Document Type:** Technical Requirements Document (TRD)  
**Project:** AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform  
**Version:** 1.0.0  
**Status:** DRAFT — PENDING REVIEW  
**Date (created):** 2026-09-28  
**Date (last updated):** 2026-09-28  
**Author:** AI Engineering Agent  
**Source Baselines:**  
- Project Definition v0.3.0 (`docs/00-PROJECT-DEFINITION.md`) — BASELINE APPROVED  
- PRD v1.1.0 (`docs/01-PRD.md`) — APPROVED  
- FRD v1.0.1 (`docs/02-FRD.md`) — APPROVED FOR NEXT STAGE  

---

## 30. TRD Change History

| Version | Date | Author | Summary |
|---|---|---|---|
| 1.0.0 | 2026-09-28 | AI Engineering Agent | Initial TRD created from approved PRD v1.1.0 and FRD v1.0.1. Resolves technical design questions PDQ-02, PDQ-03, PDQ-04, PDQ-08, and FRD deferred items (FRD-DEF-01 through FRD-DEF-10). Establishes technical architectures for Client, Server, PostgreSQL data model, Solidity smart contracts, hybrid signing model, blockchain event indexer, Pinata IPFS integration, AI/ML pipeline, and local dev/test environment. |

---

> **Mandatory Terminology Standard:**
> - The React + Vite web application is always called the **Client**.
> - The Python + FastAPI application is always called the **Server**.
> - Operational and queryable data is stored in the **PostgreSQL** relational database.
> - The **Blockchain** (Solidity on local Hardhat network) provides the tamper-evident audit layer; it is NOT the primary application database.
> - Supporting evidence documents are stored on **IPFS via Pinata**; content identifiers (CIDs) are indexed in PostgreSQL and anchored on-chain.
> - The **AI/ML module** is an in-process Python component within the Server combining rule-based heuristics with scikit-learn Isolation Forest.
> - Application authentication (**JWT**) is strictly independent from blockchain **Wallet** authentication/signing.
> - Monorepo directory names `frontend/`, `backend/`, `contracts/`, `ml/`, `docs/`, and `data/` are unaffected by terminology standards.

---

## Table of Contents

1. [Document Control](#1-document-control)
2. [Purpose and Scope](#2-purpose-and-scope)
3. [Technical Baselines and Constraints](#3-technical-baselines-and-constraints)
4. [System Technical Overview](#4-system-technical-overview)
5. [Technology Stack](#5-technology-stack)
6. [Client Technical Requirements](#6-client-technical-requirements)
7. [Server Technical Requirements](#7-server-technical-requirements)
8. [Authentication and Authorization Technical Design](#8-authentication-and-authorization-technical-design)
9. [PostgreSQL Technical Requirements](#9-postgresql-technical-requirements)
10. [API Technical Requirements](#10-api-technical-requirements)
11. [Blockchain Technical Requirements](#11-blockchain-technical-requirements)
12. [Smart Contract Technical Requirements](#12-smart-contract-technical-requirements)
13. [Blockchain Transaction and Signing Model](#13-blockchain-transaction-and-signing-model)
14. [Blockchain Event Indexing](#14-blockchain-event-indexing)
15. [IPFS and Pinata Technical Requirements](#15-ipfs-and-pinata-technical-requirements)
16. [AI/ML Technical Requirements](#16-aiml-technical-requirements)
17. [AI Execution and Background Processing](#17-ai-execution-and-background-processing)
18. [Scheduled Deadline Detection](#18-scheduled-deadline-detection)
19. [Notification Technical Requirements](#19-notification-technical-requirements)
20. [Data Validation and Consistency](#20-data-validation-and-consistency)
21. [Error Handling and Recovery](#21-error-handling-and-recovery)
22. [Security Technical Requirements](#22-security-technical-requirements)
23. [Logging and Auditability](#23-logging-and-auditability)
24. [Configuration and Environment Management](#24-configuration-and-environment-management)
25. [Local Development Environment](#25-local-development-environment)
26. [Testing-Oriented Technical Requirements](#26-testing-oriented-technical-requirements)
27. [Performance and Responsiveness Requirements](#27-performance-and-responsiveness-requirements)
28. [Technical Traceability](#28-technical-traceability)
29. [Technical Decisions, Assumptions, and Deferred Items](#29-technical-decisions-assumptions-and-deferred-items)
30. [TRD Change History](#30-trd-change-history)

---

## 1. Document Control

### 1.1 Identification
- **Document Title:** Technical Requirements Document (TRD)
- **Document File:** `docs/03-TRD.md`
- **Document Version:** 1.0.0
- **Release Status:** DRAFT — PENDING REVIEW
- **Author:** AI Engineering Agent
- **Target Audience:** Engineering team, academic evaluators, and downstream AI implementation agents.

### 1.2 Relationship to Existing Documents
This document directly succeeds and implements:
- `docs/00-PROJECT-DEFINITION.md` (v0.3.0) — Core project scope, constraints, and baseline decisions.
- `docs/01-PRD.md` (v1.1.0) — Product features, actor workflows, acceptance criteria, and product design questions (PDQ-01 through PDQ-08).
- `docs/02-FRD.md` (v1.0.1) — Detailed functional behavior, state machines, business rules, validation rules, and deferred items (FRD-DEF-01 through FRD-DEF-10).

---

## 2. Purpose and Scope

### 2.1 Purpose
The purpose of this Technical Requirements Document (TRD) is to translate the approved functional requirements into actionable technical architecture, data structures, communication protocols, interface contracts, and engineering constraints. It establishes the technical blueprint required to implement the platform cleanly without technical ambiguity, scope creep, or architectural drift.

### 2.2 Scope of the Technical Specification
This TRD specifies:
- System technical architecture and multi-tier interaction patterns.
- Detailed technology selection and package ecosystems for Client and Server.
- Client structure, routing, state management, and wallet integration.
- Server modular architecture, dependency injection, and background processing.
- Authentication mechanisms (JWT) and authorization enforcement (RBAC).
- Relational data model for PostgreSQL at the entity, field, constraint, and index level.
- Server REST API contracts, path structures, payload formats, and status codes.
- Solidity smart contract architecture, state variables, events, and gas boundaries.
- Hybrid transaction signing model (Server relayer vs. browser wallet).
- Blockchain event indexing mechanism and query acceleration.
- Pinata IPFS evidence upload, metadata linking, and retrieval workflows.
- Hybrid AI/ML risk scoring model (rule-based + Isolation Forest), feature engineering, explainability generation, and asynchronous execution.
- In-process scheduled deadline detection for missed milestones.
- In-app notification processing and non-atomic lifecycle decoupling.
- Data consistency mechanisms between relational and on-chain storage.
- Error handling, security controls, configuration, and local developer environment.

### 2.3 Hard Technical Boundaries (Out of Scope)
Consistent with the approved Project Definition and PRD, this TRD explicitly excludes:
- **No Kubernetes, container orchestration, or microservices:** The Server is a single-process FastAPI application; PostgreSQL and Hardhat run locally.
- **No custom blockchain network or public mainnet deployments:** Development and demonstration target a local Hardhat network (`localhost:8545`). Sepolia is an optional stretch deployment only.
- **No enterprise IAM or government identity integrations:** No Aadhaar, DigiLocker, or OAuth2 social log-ins. Authentication uses local JWT.
- **No real banking or government financial system integrations:** No PFMS, RBI, or real fiat disbursement gateways. Fund disbursements are simulated state transitions.
- **No external law-enforcement or court dispatch integrations:** Status `Escalated Externally` is an internal audit record denoting off-platform referral; no API integration with external case-management systems exists.
- **No deep learning architectures or external inference APIs:** AI relies on local Python scikit-learn and deterministic rules; no OpenAI, PyTorch, or cloud AI services.
- **No mobile native applications:** The Client is a responsive web application for desktop browsers.
- **No document encryption:** IPFS documents are unencrypted per the documented academic baseline.

---

## 3. Technical Baselines and Constraints

### 3.1 Architectural Baseline
The system is constructed as a modern, decoupled web architecture comprising:
1. **Client Tier:** React + Vite single-page application (SPA) styled with Tailwind CSS and shadcn/ui.
2. **Application Server Tier:** Python + FastAPI ASGI service running within a single process.
3. **Relational Database Tier:** PostgreSQL 15+ managing all operational and queryable domain entities.
4. **Decentralized Audit Tier:** EVM-compatible smart contract deployed on a local Hardhat node, anchoring ten immutable lifecycle events.
5. **Decentralized Storage Tier:** Pinata Cloud API providing pinning services for off-chain evidence documents.
6. **Analytics Tier:** Scikit-learn and rule-based anomaly detection executing within the Server process.

### 3.2 Monorepo Directory Standard
All code and assets shall be structured strictly within the planned repository folders:
```
public-fund-tracking-platform/
├── frontend/         # Client (React + Vite + Tailwind CSS + shadcn/ui)
├── backend/          # Server (Python + FastAPI + SQLAlchemy + Web3.py)
├── contracts/        # Smart Contracts (Solidity + Hardhat)
├── ml/               # AI/ML scripts, synthetic datasets, and serialized models
├── docs/             # Canonical project documentation (PRD, FRD, TRD, etc.)
└── data/             # Synthetic demo datasets and seed fixtures
```

### 3.3 Core Technical Constraints
- **Single-Machine Evaluation (CR-01):** The entire system (Client, Server, PostgreSQL, Hardhat local node) must run and be fully demonstrable on a single developer laptop without external server dependencies, except for Pinata IPFS API calls.
- **Non-Atomic Distributed State (CR-02):** PostgreSQL and the blockchain cannot be committed within a literal two-phase commit transaction. The Server orchestrates lifecycle actions so that a state change requiring on-chain anchoring is only reported as successful to the actor once the blockchain transaction has been confirmed.
- **Independent Auth Boundaries (CR-03):** Application identity is managed via JWT. Browser wallet connection is decoupled and required only for specific on-chain writing operations (Contractor fund release request submission and Auditor finding submission).
- **Human-in-the-Loop AI (CR-04):** The AI module generates risk scores and anomaly explanations; it never freezes funds, halts workflows, or declares legal fraud. All final audit determinations are human decisions made by the Auditor (ACT-05).

---

## 4. System Technical Overview

### 4.1 System Component Diagram

```mermaid
flowchart TD
    subgraph ClientTier ["Client Tier (Browser)"]
        UI["React + Vite Client<br/>(Tailwind CSS + shadcn/ui)"]
        Wallet["Browser Wallet<br/>(MetaMask / EIP-1193)"]
    end

    subgraph ServerTier ["Server Tier (Python / FastAPI)"]
        API["FastAPI REST Router"]
        AuthSvc["Auth & RBAC Service (JWT)"]
        Workflows["Lifecycle Workflow Engine"]
        Scheduler["In-Process Periodic Scheduler"]
        Indexer["Blockchain Event Indexer"]
        AIML["AI/ML Module<br/>(Rules + Isolation Forest)"]
    end

    subgraph StorageTier ["Data & Audit Tier"]
        DB[(PostgreSQL 15+<br/>Operational DB)]
        BC["Hardhat Local Node<br/>(Solidity Contract)"]
        IPFS["Pinata Cloud API<br/>(IPFS Evidence Storage)"]
    end

    %% Client Interactions
    UI <--> |"HTTPS / REST (JWT Bearer)"| API
    UI <--> |"Sign Transaction (RPC)"| Wallet
    Wallet -.-> |"Submit Signed Tx (EIP-1193)"| BC

    %% Server Internal Wiring
    API --> AuthSvc
    API --> Workflows
    Workflows --> DB
    Workflows --> |"Asynchronous Task"| AIML
    Workflows --> |"Pin Document"| IPFS
    Workflows --> |"Relayer Tx (web3.py)"| BC
    Scheduler --> |"Missed Milestone Check"| DB
    Scheduler --> |"Trigger Alerts"| Workflows

    %% Event Indexing & AI
    BC --> |"Poll Event Logs"| Indexer
    Indexer --> |"Store BlockchainEvent"| DB
    AIML --> |"Read Progress & Releases"| DB
    AIML --> |"Create AIFlag & Notification"| DB
    AIML --> |"Anchor AIAnomalyRecorded"| BC
```

### 4.2 Cross-Tier Interaction Lifecycle
1. **User Authentication:** The Client submits credentials to `POST /api/v1/auth/login`. The Server validates against PostgreSQL (bcrypt) and returns a signed JWT. Subsequent requests carry `Authorization: Bearer <token>`.
2. **Operational Read Requests:** The Client queries project summaries, milestones, progress, and notifications directly via Server REST endpoints, backed by optimized PostgreSQL queries.
3. **Blockchain-Anchored Lifecycle Actions (Server Relayer):** For administrative and verification steps (e.g. `FundReleaseApproved`), the Server validates preconditions, applies the operational change in PostgreSQL, signs and submits the on-chain transaction using the platform relayer wallet, waits for block confirmation, indexes the event, and returns success to the Client.
4. **Blockchain-Anchored Lifecycle Actions (User Wallet):** For Contractor requests and Auditor findings, the Client prepares the payload, requests signature from the connected browser wallet, submits to the Hardhat node, obtains the transaction receipt, and submits the receipt hash along with domain metadata to the Server. The Server independently verifies the transaction on-chain via Web3.py before recording the finalized state in PostgreSQL.
5. **Off-Chain Evidence Ingestion:** The Client uploads documents (invoices, photos, inspection reports) to the Server. The Server validates MIME type and size, streams the binary payload to Pinata API, obtains the IPFS CID, and references the CID in both PostgreSQL and the relevant on-chain event.
6. **Asynchronous Risk Evaluation:** Following trigger events, the Server dispatches a background task. The AI module computes deterministic risk rules and runs Isolation Forest inference against project features. If the resulting risk score meets or exceeds the threshold, an `AIFlag` is created in PostgreSQL, an `AIAnomalyRecorded` event is anchored on-chain, and an in-app notification is routed to the Auditor.

---

## 5. Technology Stack

### 5.1 Layer Specification

| Layer | Component | Technology / Library | Version | Technical Justification |
|---|---|---|---|---|
| **Client** | Runtime / Bundler | Node.js / Vite | 5.x | Ultra-fast HMR, ES modules, optimal bundling for React. |
| **Client** | UI Framework | React | 18.2+ | Component-driven architecture, robust hook ecosystem. |
| **Client** | Routing | React Router DOM | 6.22+ | Declarative nested routing, route guards, public/auth separation. |
| **Client** | Styling | Tailwind CSS | 3.4+ | Utility-first, predictable design tokens, dark mode support. |
| **Client** | Component Primitives | shadcn/ui (Radix UI) | Latest | Accessible, unstyled primitives, high aesthetic quality. |
| **Client** | Wallet Integration | ethers.js / native EIP-1193 | 6.11+ | Lightweight browser wallet detection, contract invocation, signing. |
| **Client** | HTTP Client | Axios | 1.6+ | Interceptors for JWT injection, centralized error handling. |
| **Client** | Form Validation | React Hook Form + Zod | Latest | Performant, schema-first client-side validation mirroring VR rules. |
| **Client** | Icons | Lucide React | Latest | Clean, consistent SVG icon set compatible with shadcn/ui. |
| **Server** | Language / Runtime | Python | 3.11+ | Strong typing support, native async IO, standard data science base. |
| **Server** | Web Framework | FastAPI | 0.110+ | High-performance ASGI framework, automatic OpenAPI docs, Pydantic v2. |
| **Server** | ASGI Server | Uvicorn | 0.28+ | Lightning-fast async server implementation for FastAPI. |
| **Server** | ORM / DB Access | SQLAlchemy | 2.0+ | Modern async/sync declarative ORM, unit-of-work, connection pooling. |
| **Server** | DB Migrations | Alembic | 1.13+ | Version-controlled, deterministic schema migrations. |
| **Server** | Auth / Crypto | python-jose / passlib[bcrypt] | Latest | Robust JWT encoding/decoding, secure password hashing. |
| **Server** | Web3 Integration | web3.py | 6.15+ | Comprehensive Ethereum RPC interaction, contract wrapping, log parsing. |
| **Server** | HTTP / Pinata Client | httpx | 0.27+ | Async HTTP client for streaming multipart uploads to Pinata API. |
| **Server** | Scheduler | APScheduler / asyncio loop | 3.10+ | In-process lightweight periodic job execution for milestone checks. |
| **Database** | Relational Store | PostgreSQL | 15+ | ACID compliance, JSONB support, relational integrity, indexing. |
| **Blockchain** | Smart Contracts | Solidity | 0.8.20+ | EVM standard, built-in overflow checks, custom errors. |
| **Blockchain** | Dev Toolchain | Hardhat | 2.20+ | Local EVM network, contract compilation, automated tests. |
| **Blockchain** | Base Contracts | OpenZeppelin Contracts | 5.0+ | Audited implementations of `Ownable` and `ReentrancyGuard`. |
| **Storage** | Off-Chain IPFS | Pinata REST API | API v1 | Managed IPFS pinning service, high uptime, predictable CID generation. |
| **AI/ML** | Anomaly Detection | scikit-learn | 1.4+ | Production-grade `IsolationForest`, lightweight, fast in-process CPU inference. |
| **AI/ML** | Data Manipulation | pandas / NumPy | 2.2+ | High-performance feature vector formatting and array calculations. |
| **AI/ML** | Model Persistence | joblib | 1.3+ | Efficient serialization of trained scikit-learn estimator pipelines. |

---

## 6. Client Technical Requirements

### 6.1 Architecture and Directory Structure
The Client is structured as a modular React single-page application under `frontend/`:
```
frontend/
├── public/               # Static assets, favicon, logo
├── src/
│   ├── assets/           # CSS, images, brand assets
│   ├── components/       # Reusable UI widgets
│   │   ├── ui/           # shadcn/ui primitives (button, dialog, card, etc.)
│   │   ├── layout/       # AppShell, Navbar, Sidebar, Footer
│   │   ├── shared/       # DataTable, StatusBadge, MetricCard, Disclaimer
│   │   └── feedback/     # LoadingSkeleton, ErrorBoundary, EmptyState
│   ├── contexts/         # React Contexts (AuthContext, ThemeContext, WalletContext)
│   ├── hooks/            # Custom hooks (useAuth, useWallet, useApi, useDebounce)
│   ├── layouts/          # Route layout wrappers (PublicLayout, DashboardLayout)
│   ├── pages/            # Page-level components organized by functional area
│   │   ├── auth/         # Login
│   │   ├── admin/        # User management
│   │   ├── projects/     # Project list, detail, creation
│   │   ├── milestones/   # Milestone management
│   │   ├── releases/     # Fund release workflows (submit, verify, approve)
│   │   ├── auditor/      # AI flag queue, investigation detail, findings
│   │   ├── citizen/      # Public transparency portal
│   │   └── not-found/    # 404 page
│   ├── routes/           # React Router route definitions and ProtectedRoute guards
│   ├── services/         # API abstraction layer (Axios instances, endpoint methods)
│   ├── types/            # TypeScript interfaces / type definitions
│   ├── utils/            # Helper functions (currency formatters, date formatters)
│   ├── App.jsx           # Root application component with Context Providers
│   ├── main.jsx          # Entry point mounting to DOM
│   └── index.css         # Tailwind directives and CSS variables
├── package.json
├── tailwind.config.js
└── vite.config.js
```

### 6.2 Routing Architecture and Route Protection
The Client implements declarative routing using `react-router-dom` v6:
- **Public Routes:** Unauthenticated routes accessible by any visitor, specifically `/` (landing/portal), `/projects/:id` (public project view), and `/login`.
- **Protected Routes:** Wrapped in a generic `<ProtectedRoute />` component that checks:
  1. Is the user authenticated (valid JWT in `AuthContext`)? If no, redirect to `/login` preserving intended destination in router state.
  2. Does the user's role match the required `allowedRoles` array for the route? If no, render an `Unauthorized` 403 view.
  3. Is the user account active? If deactivated, force session termination and redirect to login.

```
Route Map:
/ (Public Portal Home)
/projects/:id (Public Project Summary & Timeline)
/login (Authentication)
/dashboard (Role-based redirect to default dashboard)
/admin/users (Platform Admin - ACT-01)
/projects/new (Government Admin - ACT-02)
/projects/:id/manage (Government Admin / Officer - ACT-02, ACT-03)
/releases/submit (Contractor - ACT-04)
/releases/review (Officer / Government Admin - ACT-03, ACT-02)
/auditor/queue (Auditor - ACT-05)
/auditor/investigate/:flagId (Auditor - ACT-05)
/notifications (Authenticated Users - ACT-01..ACT-05)
```

### 6.3 State Management and Server Communication
- **API Client:** Axios instance pre-configured with `baseURL: import.meta.env.VITE_API_BASE_URL`.
- **Request Interceptor:** Automatically extracts JWT token from `localStorage` and injects `Authorization: Bearer <token>` header on all non-public requests.
- **Response Interceptor:** Intercepts `401 Unauthorized` responses; clears cached auth state and routes user to `/login` with an informational toast.
- **Data Caching / React State:** Uses local component state or lightweight custom hooks for request caching, loading states, and error handling. Skeletons are displayed during network latency.

### 6.4 Browser Wallet Integration Requirements
- **Wallet Detection:** Probes `window.ethereum` via standard EIP-1193 provider.
- **Connection Handshake:** Triggers `eth_requestAccounts` upon user request. Captures selected address and current `chainId`.
- **Network Enforcement:** Verifies connected network matches Hardhat local node (`chainId: 31337` / `0x7a69`). If mismatched, triggers `wallet_switchEthereumChain` or alerts user.
- **Role Scoping:** Wallet connection is strictly required only when ACT-04 (Contractor) submits fund release requests or ACT-05 (Auditor) records on-chain audit findings. Other roles interact via Server-signed transactions.

### 6.5 Design System and Theming
- **Tokens & Primitives:** Built on Tailwind CSS utility classes and Radix UI primitives.
- **Theme Modes:** Full light, dark, and system preference support via class-based dark mode (`<html class="dark">`). Color palette leverages neutral slates and curated semantic accents (emerald for approvals, amber for warnings/overdue, rose for flags/rejections, indigo for blockchain/audit).
- **Mandatory AI Disclaimer Display:** Every view displaying risk scores or anomaly details renders the mandatory warning badge: *"AI risk flags indicate anomalous patterns and do not establish fraud or corruption."*

---

## 7. Server Technical Requirements

### 7.1 Architecture and Directory Structure
The Server is a single-process FastAPI application under `backend/`:
```
backend/
├── app/
│   ├── api/                  # API routers grouped by resource
│   │   ├── v1/
│   │   │   ├── auth.py
│   │   │   ├── users.py
│   │   │   ├── projects.py
│   │   │   ├── milestones.py
│   │   │   ├── progress.py
│   │   │   ├── fund_releases.py
│   │   │   ├── documents.py
│   │   │   ├── audit_trail.py
│   │   │   ├── ai.py
│   │   │   ├── auditor.py
│   │   │   ├── escalations.py
│   │   │   ├── notifications.py
│   │   │   └── citizen.py
│   │   └── api_router.py     # Aggregated v1 router
│   ├── core/                 # Framework fundamentals
│   │   ├── config.py         # Pydantic BaseSettings loading .env
│   │   ├── database.py       # SQLAlchemy engine and sessionmaker
│   │   ├── security.py       # Password hashing and JWT encoding/decoding
│   │   └── exceptions.py     # Custom application exceptions and handlers
│   ├── models/               # SQLAlchemy ORM declarative models
│   │   ├── user.py
│   │   ├── project.py
│   │   ├── milestone.py
│   │   ├── progress.py
│   │   ├── fund_release.py
│   │   ├── document.py
│   │   ├── blockchain_event.py
│   │   ├── ai_flag.py
│   │   ├── audit_finding.py
│   │   ├── escalation.py
│   │   └── notification.py
│   ├── schemas/              # Pydantic validation and serialization schemas
│   │   ├── auth.py
│   │   ├── project.py
│   │   ├── milestone.py
│   │   ├── fund_release.py
│   │   ├── document.py
│   │   ├── ai.py
│   │   └── notification.py
│   ├── services/             # Core business logic workflows
│   │   ├── auth_service.py
│   │   ├── project_service.py
│   │   ├── fund_service.py
│   │   ├── ipfs_service.py
│   │   ├── blockchain_service.py
│   │   ├── indexer_service.py
│   │   ├── ai_service.py
│   │   ├── scheduler_service.py
│   │   └── notification_service.py
│   └── main.py               # FastAPI factory, lifespan handlers, CORS middleware
├── alembic/                  # Database migration scripts
├── tests/                    # Backend test suite (pytest)
├── requirements.txt          # Python dependencies
└── alembic.ini
```

### 7.2 Application Lifespan Architecture
FastAPI lifespan context manager handles graceful startup and shutdown within the single process:
1. **Startup Phase:**
   - Initialize SQLAlchemy database connection pool and verify connection.
   - Run database schema migration verification (or verify tables exist).
   - Initialize Web3.py provider connecting to local Hardhat node; verify contract deployment address and ABI.
   - Load pre-trained Isolation Forest model from `ml/models/isolation_forest.joblib` into memory.
   - Launch in-process scheduled deadline detection job (APScheduler).
   - Launch background blockchain event listener thread/task.
2. **Shutdown Phase:**
   - Gracefully terminate scheduled jobs and event listener polling loop.
   - Dispose of SQLAlchemy engine connection pool.
   - Close open HTTPX client sessions.

### 7.3 Dependency Injection Standards
- `get_db()`: Yields a scoped database session, committing on successful HTTP handling or rolling back upon unhandled exception.
- `get_current_user()`: Validates incoming Bearer token, checks user existence in PostgreSQL, ensures `is_active == True`, and returns the authenticated `User` model.
- `require_role(allowed_roles: List[Role])`: Higher-order dependency wrapping `get_current_user()` that validates role authorization at the Server boundary, returning `HTTP 403 Forbidden` if unauthorized.

---

## 8. Authentication and Authorization Technical Design

### 8.1 Password Hashing Specification
- **Algorithm:** bcrypt via `passlib.context.CryptContext(schemes=["bcrypt"], deprecated="auto")`.
- **Work Factor (Rounds):** 12 rounds (provides robust security without causing noticeable latency during login verification).
- **Storage:** Stored in `users.password_hash` as a 60-character ASCII string. Plaintext passwords are never logged or stored.

### 8.2 JWT Issuance and Claims Contract
- **Algorithm:** HMAC-SHA256 (`HS256`).
- **Signing Secret:** Loaded from `JWT_SECRET_KEY` environment variable (minimum 256-bit entropy).
- **Token Expiry (Technical Decision TD-04 / FRD-DEF-04):** Set to 60 minutes (`ACCESS_TOKEN_EXPIRE_MINUTES=60`). Refresh token complexity is omitted for this academic prototype; expired tokens require user re-authentication.
- **Payload Claims Schema:**
  ```json
  {
    "sub": "usr_01HQZ9G81N4V7K002M3B1K9P8Q",
    "email": "officer.sharma@gov.local",
    "role": "DEPARTMENT_OFFICER",
    "name": "Rajesh Sharma",
    "iat": 1711620000,
    "exp": 1711623600,
    "jti": "550e8400-e29b-41d4-a716-446655440000"
  }
  ```

### 8.3 Server-Side Authorization Boundary (RBAC)
Authorization is strictly evaluated server-side on every API call. Client route guards are UX helpers only.
The six roles map directly to server-side permissions:

| Actor Code | Role Name | System Permissions |
|---|---|---|
| `ACT-01` | `PLATFORM_ADMIN` | User account creation and status management. System monitoring. No fund workflow permissions. |
| `ACT-02` | `GOVERNMENT_ADMIN` | Project creation, budget allocation, fund release approval/rejection, formal auditor escalation, project completion, audit summary publishing. |
| `ACT-03` | `DEPARTMENT_OFFICER` | Project milestone definition, physical progress recording, contractor fund release verification and recommendation. |
| `ACT-04` | `CONTRACTOR` | Fund release request submission, supporting invoice/photo document upload. |
| `ACT-05` | `AUDITOR` | View AI anomaly flags and admin escalations, inspect project audit trails and evidence, record on-chain audit findings. |
| `ACT-06` | `CITIZEN` | Public read-only access to approved projects, non-sensitive timelines, and published audit summaries. No JWT required. |

---

## 9. PostgreSQL Technical Requirements

### 9.1 Entity Relationship Diagram

```mermaid
erDiagram
    User ||--o{ Project : creates
    User ||--o{ ProjectAssignment : assigned_to
    Project ||--o{ ProjectAssignment : has_officer
    Project ||--o{ BudgetAllocation : funded_by
    Project ||--o{ Milestone : divided_into
    Milestone ||--o{ ProjectProgress : tracks_progress
    Milestone ||--o{ FundRelease : requests_release
    User ||--o{ FundRelease : submits_contractor
    FundRelease ||--o{ Document : attaches_evidence
    Project ||--o{ Document : associates_docs
    Project ||--o{ BlockchainEvent : logs_events
    Project ||--o{ AIFlag : triggers_flags
    AIFlag ||--o{ AuditFinding : investigates
    Project ||--o{ Escalation : escalates
    Escalation ||--o{ AuditFinding : investigates_esc
    User ||--o{ AuditFinding : signs_audit
    User ||--o{ Notification : receives_alert
```

### 9.2 Core Entity Specifications (Technical Data Model)

#### 1. `users`
- `id` (UUID / String, Primary Key)
- `email` (VARCHAR(255), UNIQUE, NOT NULL, Indexed)
- `password_hash` (VARCHAR(255), NOT NULL)
- `full_name` (VARCHAR(150), NOT NULL)
- `role` (VARCHAR(50), NOT NULL) — Enum: `PLATFORM_ADMIN`, `GOVERNMENT_ADMIN`, `DEPARTMENT_OFFICER`, `CONTRACTOR`, `AUDITOR`
- `wallet_address` (VARCHAR(42), NULL, Indexed) — Validated checksum Ethereum address for ACT-04 and ACT-05
- `is_active` (BOOLEAN, DEFAULT TRUE, NOT NULL)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- `updated_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 2. `projects`
- `id` (UUID / String, Primary Key)
- `project_code` (VARCHAR(50), UNIQUE, NOT NULL, Indexed)
- `name` (VARCHAR(255), NOT NULL)
- `description` (TEXT, NOT NULL)
- `category` (VARCHAR(100), NOT NULL) — e.g. Roads, Healthcare, Education, Water
- `region_district` (VARCHAR(100), NOT NULL, Indexed)
- `total_budget` (NUMERIC(18, 2), NOT NULL, Check: `total_budget > 0`)
- `allocated_budget` (NUMERIC(18, 2), DEFAULT 0, NOT NULL)
- `disbursed_amount` (NUMERIC(18, 2), DEFAULT 0, NOT NULL)
- `start_date` (DATE, NOT NULL)
- `expected_end_date` (DATE, NOT NULL, Check: `expected_end_date >= start_date`)
- `status` (VARCHAR(30), DEFAULT 'Draft', NOT NULL, Indexed) — Enum: `Draft`, `Active`, `Completed`
- `assigned_officer_id` (UUID, References `users(id)`, NULL)
- `created_by` (UUID, References `users(id)`, NOT NULL)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- `updated_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 3. `milestones`
- `id` (UUID / String, Primary Key)
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `name` (VARCHAR(255), NOT NULL)
- `deliverable_description` (TEXT, NOT NULL)
- `budget_portion` (NUMERIC(18, 2), NOT NULL, Check: `budget_portion > 0`)
- `disbursed_amount` (NUMERIC(18, 2), DEFAULT 0, NOT NULL)
- `due_date` (DATE, NOT NULL, Indexed)
- `status` (VARCHAR(30), DEFAULT 'Active', NOT NULL, Indexed) — Enum: `Active`, `Missed/Overdue`, `Completed`
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- `completed_at` (TIMESTAMPTZ, NULL)
- `missed_at` (TIMESTAMPTZ, NULL)

#### 4. `project_progress`
- `id` (UUID / String, Primary Key)
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `milestone_id` (UUID, References `milestones(id)`, NOT NULL, Indexed)
- `progress_percentage` (INTEGER, NOT NULL, Check: `progress_percentage >= 0 AND progress_percentage <= 100`)
- `description` (TEXT, NULL)
- `recorded_by` (UUID, References `users(id)`, NOT NULL)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 5. `fund_releases`
- `id` (UUID / String, Primary Key)
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `milestone_id` (UUID, References `milestones(id)`, NOT NULL, Indexed)
- `contractor_id` (UUID, References `users(id)`, NOT NULL)
- `contractor_wallet` (VARCHAR(42), NOT NULL)
- `requested_amount` (NUMERIC(18, 2), NOT NULL, Check: `requested_amount > 0`)
- `approved_amount` (NUMERIC(18, 2), DEFAULT 0, NOT NULL)
- `status` (VARCHAR(30), DEFAULT 'Pending', NOT NULL, Indexed) — Enum: `Pending`, `Under Review`, `Pending Admin Decision`, `Approved`, `Rejected`
- `officer_recommendation` (VARCHAR(30), NULL) — Enum: `Recommended`, `Rejected`
- `officer_notes` (TEXT, NULL)
- `rejection_reason` (TEXT, NULL)
- `tx_hash` (VARCHAR(66), NULL, Indexed)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- `verified_at` (TIMESTAMPTZ, NULL)
- `decided_at` (TIMESTAMPTZ, NULL)

#### 6. `documents`
- `id` (UUID / String, Primary Key)
- `entity_type` (VARCHAR(50), NOT NULL) — Enum: `FUND_RELEASE_EVIDENCE`, `OFFICER_REPORT`, `AUDIT_REPORT`
- `entity_id` (UUID, NOT NULL, Indexed)
- `file_name` (VARCHAR(255), NOT NULL)
- `file_size_bytes` (INTEGER, NOT NULL)
- `mime_type` (VARCHAR(100), NOT NULL)
- `ipfs_cid` (VARCHAR(100), NOT NULL, Indexed)
- `uploaded_by` (UUID, References `users(id)`, NOT NULL)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 7. `blockchain_events`
- `id` (UUID / String, Primary Key)
- `event_type` (VARCHAR(50), NOT NULL, Indexed) — One of 10 confirmed event names
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `entity_id` (UUID, NULL)
- `tx_hash` (VARCHAR(66), NOT NULL, Indexed)
- `block_number` (BIGINT, NOT NULL, Indexed)
- `block_timestamp` (TIMESTAMPTZ, NOT NULL)
- `log_index` (INTEGER, NOT NULL)
- `event_data` (JSONB, NOT NULL)
- `indexed_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- **Constraint:** UNIQUE(`tx_hash`, `log_index`) to prevent duplicate event ingestion.

#### 8. `ai_flags`
- `id` (UUID / String, Primary Key)
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `trigger_event_type` (VARCHAR(50), NOT NULL)
- `risk_score` (INTEGER, NOT NULL, Check: `risk_score >= 0 AND risk_score <= 100`)
- `anomaly_types` (JSONB, NOT NULL) — Array of active anomaly capabilities (e.g. `["AI-CAP-01", "AI-CAP-05"]`)
- `contributing_factors` (JSONB, NOT NULL) — Structured explanation objects
- `status` (VARCHAR(30), DEFAULT 'Open', NOT NULL, Indexed) — Enum: `Open`, `Under Review`, `Reviewed — No Action`, `Escalated Externally`
- `tx_hash` (VARCHAR(66), NULL)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- `updated_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 9. `audit_findings`
- `id` (UUID / String, Primary Key)
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `flag_id` (UUID, References `ai_flags(id)`, NULL)
- `escalation_id` (UUID, NULL)
- `auditor_id` (UUID, References `users(id)`, NOT NULL)
- `auditor_wallet` (VARCHAR(42), NOT NULL)
- `outcome` (VARCHAR(50), NOT NULL) — Enum: `Reviewed — No Action Required`, `Escalated to External Authorities`
- `report_narrative` (TEXT, NOT NULL)
- `report_ipfs_cid` (VARCHAR(100), NOT NULL)
- `tx_hash` (VARCHAR(66), NOT NULL, Indexed)
- `is_published` (BOOLEAN, DEFAULT FALSE, NOT NULL, Indexed)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 10. `escalations`
- `id` (UUID / String, Primary Key)
- `project_id` (UUID, References `projects(id)`, NOT NULL, Indexed)
- `flag_id` (UUID, References `ai_flags(id)`, NULL)
- `reason` (TEXT, NOT NULL)
- `escalated_by` (UUID, References `users(id)`, NOT NULL)
- `status` (VARCHAR(30), DEFAULT 'Open', NOT NULL, Indexed) — Enum: `Open`, `Under Review`, `Reviewed — No Action`, `Escalated Externally`
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)
- `updated_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

#### 11. `notifications`
- `id` (UUID / String, Primary Key)
- `user_id` (UUID, References `users(id)`, NOT NULL, Indexed)
- `event_type` (VARCHAR(50), NOT NULL)
- `title` (VARCHAR(255), NOT NULL)
- `message` (TEXT, NOT NULL)
- `link_url` (VARCHAR(255), NULL)
- `is_read` (BOOLEAN, DEFAULT FALSE, NOT NULL, Indexed)
- `created_at` (TIMESTAMPTZ, DEFAULT NOW(), NOT NULL)

---

## 10. API Technical Requirements

### 10.1 Standards and Conventions
- **Base URI:** `/api/v1`
- **Data Exchange Format:** JSON (`application/json`) with standard UTF-8 encoding.
- **Multipart Form Uploads:** `multipart/form-data` for file uploads (`/documents/upload`).
- **Standard HTTP Codes:**
  - `200 OK`: Successful read or update.
  - `201 Created`: Successful resource creation.
  - `204 No Content`: Successful action with empty response body.
  - `400 Bad Request`: Malformed payload or failed business rule precondition.
  - `401 Unauthorized`: Missing, expired, or invalid JWT Bearer token.
  - `403 Forbidden`: Authenticated user role lacks permission for the endpoint.
  - `404 Not Found`: Requested resource does not exist.
  - `409 Conflict`: Concurrency conflict or duplicate record (e.g. duplicate active release request).
  - `422 Unprocessable Entity`: Request body failed Pydantic schema validation.
  - `502 Bad Gateway`: Downstream communication failure (Pinata IPFS or Hardhat RPC).
  - `500 Internal Server Error`: Unhandled server exception.

### 10.2 Endpoint Specification Directory

#### Authentication & Administration
- `POST /api/v1/auth/login`: Authenticate with email/password; returns `{ access_token, token_type: "bearer", user }`.
- `GET /api/v1/auth/me`: Returns current authenticated user profile and roles.
- `POST /api/v1/auth/logout`: Stateless client acknowledgement.
- `GET /api/v1/admin/users`: [ACT-01] List users with pagination and role filters.
- `POST /api/v1/admin/users`: [ACT-01] Create a new user (`email`, `password`, `full_name`, `role`, `wallet_address`).
- `PATCH /api/v1/admin/users/{id}/status`: [ACT-01] Activate or deactivate user account.

#### Projects & Milestones
- `GET /api/v1/projects`: [ACT-01..ACT-05] List projects filterable by status, region, category.
- `POST /api/v1/projects`: [ACT-02] Create project in `Draft` status. Anchors `ProjectCreated`.
- `GET /api/v1/projects/{id}`: [ACT-01..ACT-05] Get detailed project view.
- `POST /api/v1/projects/{id}/budget`: [ACT-02] Allocate project budget. Anchors `BudgetAllocated`.
- `POST /api/v1/projects/{id}/assign-officer`: [ACT-02] Assign single Department Officer (ACT-03).
- `POST /api/v1/projects/{id}/complete`: [ACT-02] Mark project as Completed. Anchors `ProjectCompleted`.
- `GET /api/v1/projects/{id}/milestones`: [ACT-01..ACT-05] List milestones for a project.
- `POST /api/v1/projects/{id}/milestones`: [ACT-03] Define new milestone. Anchors `MilestoneDefined`. Triggers AI.
- `PATCH /api/v1/milestones/{id}/complete`: [ACT-03] Explicitly mark milestone as Completed.

#### Progress & Fund Releases
- `POST /api/v1/milestones/{id}/progress`: [ACT-03] Record physical progress update (0–100%).
- `GET /api/v1/milestones/{id}/progress`: [ACT-01..ACT-05] View physical progress chronological history.
- `POST /api/v1/fund-releases`: [ACT-04] Submit fund release request + evidence CIDs. Anchors `FundReleaseRequested`.
- `GET /api/v1/fund-releases`: [ACT-02, ACT-03, ACT-04] List fund release requests with status filter.
- `POST /api/v1/fund-releases/{id}/verify`: [ACT-03] Submit verification report + recommendation. Anchors `OfficerVerified`. Triggers AI.
- `POST /api/v1/fund-releases/{id}/approve`: [ACT-02] Approve fund release. Anchors `FundReleaseApproved`. Triggers AI.
- `POST /api/v1/fund-releases/{id}/reject`: [ACT-02] Reject fund release with reason. Anchors `FundReleaseRejected`.

#### Documents & IPFS
- `POST /api/v1/documents/upload`: [ACT-03, ACT-04, ACT-05] Upload binary document; pins to Pinata; returns CID and record ID.
- `GET /api/v1/documents/{id}`: [ACT-01..ACT-05] Get document metadata and gateway access URL.

#### Blockchain & Audit Trail
- `GET /api/v1/projects/{id}/audit-trail`: [ACT-01..ACT-05] Retrieve indexed on-chain events for a project.

#### AI, Escalations & Investigations
- `GET /api/v1/ai/flags`: [ACT-05] List AI anomaly flags sorted by risk score descending.
- `GET /api/v1/ai/flags/{id}`: [ACT-02, ACT-05] Get flag detail, score, and contributing factors with mandatory disclaimer.
- `POST /api/v1/escalations`: [ACT-02] Submit formal concern to Auditor.
- `GET /api/v1/escalations`: [ACT-02, ACT-05] List escalations.
- `POST /api/v1/auditor/findings`: [ACT-05] Record on-chain audit finding with wallet. Anchors `AuditFindingRecorded`.
- `POST /api/v1/auditor/findings/{id}/publish`: [ACT-02] Publish summary to public citizen portal.

#### Notifications & Citizen Portal
- `GET /api/v1/notifications`: [ACT-01..ACT-05] List current user's in-app notifications.
- `GET /api/v1/notifications/unread-count`: [ACT-01..ACT-05] Get unread badge count.
- `PATCH /api/v1/notifications/{id}/read`: [ACT-01..ACT-05] Mark notification as read.
- `GET /api/v1/public/projects`: [ACT-06] Public list of Active/Completed projects.
- `GET /api/v1/public/projects/{id}`: [ACT-06] Non-sensitive project detail, public milestone timeline, published audit summaries.

---

## 11. Blockchain Technical Requirements

### 11.1 Local Hardhat Network Specifications
- **Node URL:** `http://127.0.0.1:8545`
- **Network Name:** `hardhat_local`
- **Chain ID:** `31337` (hex: `0x7a69`)
- **Block Time:** Automine enabled (transactions mined immediately upon submission; realistic demonstration).
- **Accounts:** Standard 20 test accounts pre-funded with 10,000 test ETH each.
  - Account #0: Platform Relayer / Contract Deployer (Server-side signer).
  - Account #1: Contractor Demo Wallet (ACT-04).
  - Account #2: Auditor Demo Wallet (ACT-05).

### 11.2 Dual-Layer Architecture: Events vs. State
To optimize gas efficiency and prevent smart contract bloat, the contract maintains minimal on-chain state for sanity checks and relies on typed event logs for the comprehensive tamper-evident audit history.
- **On-Chain State:** Tracks project existence, allocated budget, cumulative disbursed amount, and completion status.
- **On-Chain Events:** Emits structured events containing descriptive metadata, document CIDs, and hash digests.

### 11.3 Ten Confirmed On-Chain Event Signatures
All 10 confirmed event types are implemented with standard indexed topics for fast log filtering:

```solidity
event ProjectCreated(
    uint256 indexed projectId,
    uint256 totalBudget,
    string name,
    uint256 timestamp
);

event BudgetAllocated(
    uint256 indexed projectId,
    uint256 amount,
    address indexed authority,
    uint256 timestamp
);

event MilestoneDefined(
    uint256 indexed projectId,
    uint256 indexed milestoneId,
    uint256 budgetPortion,
    bytes32 dueDateHash,
    uint256 timestamp
);

event FundReleaseRequested(
    uint256 indexed projectId,
    uint256 indexed milestoneId,
    uint256 requestedAmount,
    string evidenceCids,
    address indexed contractor,
    uint256 timestamp
);

event OfficerVerified(
    uint256 indexed projectId,
    uint256 indexed milestoneId,
    uint8 recommendationOutcome, // 0 = Rejected, 1 = Recommended
    string reportCid,
    address indexed officer,
    uint256 timestamp
);

event FundReleaseApproved(
    uint256 indexed projectId,
    uint256 indexed milestoneId,
    uint256 approvedAmount,
    address indexed authority,
    uint256 timestamp
);

event FundReleaseRejected(
    uint256 indexed projectId,
    uint256 indexed milestoneId,
    bytes32 rejectionReasonHash,
    address indexed authority,
    uint256 timestamp
);

event AIAnomalyRecorded(
    uint256 indexed projectId,
    uint256 indexed flagId,
    uint8 riskScore,
    string anomalyType,
    uint256 timestamp
);

event AuditFindingRecorded(
    uint256 indexed projectId,
    uint256 indexed flagId,
    address indexed auditorWallet,
    uint8 findingOutcome, // 0 = NoAction, 1 = EscalatedExternal
    string reportCid,
    uint256 timestamp
);

event ProjectCompleted(
    uint256 indexed projectId,
    uint256 finalDisbursedAmount,
    uint256 timestamp
);
```

---

## 12. Smart Contract Technical Requirements

### 12.1 Contract Specification (`PublicFundTracker.sol`)
- **Solidity Version:** `^0.8.20`
- **Inheritance:** `Ownable` (from OpenZeppelin), `ReentrancyGuard` (from OpenZeppelin).
- **Core Storage Data Structures:**
  ```solidity
  struct ProjectRecord {
      uint256 totalBudget;
      uint256 allocatedBudget;
      uint256 disbursedAmount;
      bool isCompleted;
      bool exists;
  }

  mapping(uint256 => ProjectRecord) public projects;
  mapping(uint256 => mapping(uint256 => uint256)) public milestoneAllocations;
  mapping(uint256 => mapping(uint256 => uint256)) public milestoneDisbursed;
  ```

### 12.2 Method Signatures and Modifiers
1. `createProject(uint256 projectId, uint256 totalBudget, string calldata name) external onlyOwner`
2. `allocateBudget(uint256 projectId, uint256 amount) external onlyOwner`
3. `defineMilestone(uint256 projectId, uint256 milestoneId, uint256 budgetPortion, bytes32 dueDateHash) external onlyOwner`
4. `requestFundRelease(uint256 projectId, uint256 milestoneId, uint256 requestedAmount, string calldata evidenceCids) external nonReentrant`
5. `recordOfficerVerification(uint256 projectId, uint256 milestoneId, uint8 recommendationOutcome, string calldata reportCid, address officer) external onlyOwner`
6. `approveFundRelease(uint256 projectId, uint256 milestoneId, uint256 approvedAmount) external onlyOwner nonReentrant`
7. `rejectFundRelease(uint256 projectId, uint256 milestoneId, bytes32 rejectionReasonHash) external onlyOwner`
8. `recordAIAnomaly(uint256 projectId, uint256 flagId, uint8 riskScore, string calldata anomalyType) external onlyOwner`
9. `recordAuditFinding(uint256 projectId, uint256 flagId, uint8 findingOutcome, string calldata reportCid) external nonReentrant`
10. `completeProject(uint256 projectId) external onlyOwner`

---

## 13. Blockchain Transaction and Signing Model

### 13.1 Technical Resolution of Deferred Decision (PDQ-04 / FRD-DEF-03)
To balance real cryptographic accountability with practical college project feasibility, the platform implements a **Hybrid Transaction Signing Architecture**:

```mermaid
flowchart LR
    subgraph UserSigning ["Actor Wallet Direct Signing"]
        Contractor["Contractor (ACT-04)"] -->|"requestFundRelease()"| BrowserWallet1["MetaMask"]
        Auditor["Auditor (ACT-05)"] -->|"recordAuditFinding()"| BrowserWallet2["MetaMask"]
        BrowserWallet1 -->|"Direct Web3 RPC"| Contract[Smart Contract]
        BrowserWallet2 -->|"Direct Web3 RPC"| Contract
    end

    subgraph ServerRelayerSigning ["Server Relayer Signing"]
        GovAdmin["Gov Admin (ACT-02)"] -->|"API Request"| Server["FastAPI Server"]
        Officer["Officer (ACT-03)"] -->|"API Request"| Server
        AISvc["AI Module"] -->|"Background Trigger"| Server
        Server -->|"Web3.py Signer<br/>(Account #0)"| Contract
    end
```

### 13.2 Actor Wallet Signing Workflow (Contractor & Auditor)
1. User triggers action in Client (e.g. submit release request or record audit finding).
2. Client formats contract transaction arguments.
3. Client prompts browser wallet (`window.ethereum`) to sign and broadcast the transaction.
4. User confirms transaction in MetaMask; transaction is mined on Hardhat node.
5. Client captures transaction hash and receipt, and submits payload + `tx_hash` to Server REST API.
6. Server independently queries Hardhat node via Web3.py to verify:
   - Transaction was mined successfully (`receipt.status == 1`).
   - Transaction called the expected contract method with matching entity parameters.
   - Transaction was signed by the registered wallet address for the actor.
7. Upon successful on-chain verification, Server finalizes the operational record in PostgreSQL.

### 13.3 Server Relayer Signing Workflow (System & Administrative Events)
1. Actor (ACT-02 or ACT-03) triggers lifecycle action via Server API (e.g. `FundReleaseApproved`).
2. Server validates preconditions and prepares database transition.
3. Server loads relayer account private key from `BLOCKCHAIN_SIGNER_PRIVATE_KEY` environment variable.
4. Server builds, signs, and broadcasts the contract call via Web3.py.
5. Server waits synchronously for transaction receipt (1 block confirmation, < 2 sec on Hardhat).
6. If transaction succeeds: Server commits PostgreSQL state and marks action complete.
7. If transaction reverts/fails: Server logs error, aborts state transition, and returns descriptive error to Client. The action is never reported as successful.

---

## 14. Blockchain Event Indexing

### 14.1 Technical Architecture
The Server operates an asynchronous event listener service (`indexer_service.py`) running within the Server application process:
- Uses Web3.py filter APIs (`w3.eth.filter` or log polling via `w3.eth.get_logs`).
- Polling frequency: Every 2 seconds during active server execution.
- Watermark persistence: The highest indexed block number is persisted in a PostgreSQL metadata table (`system_state.last_indexed_block`).

### 14.2 Idempotency and Deduplication
- Every extracted event log is converted into a `BlockchainEvent` entity.
- The composite key `(tx_hash, log_index)` is enforced by a database UNIQUE constraint.
- Duplicate log receipts resulting from poll retries are cleanly ignored (`ON CONFLICT DO NOTHING`).

### 14.3 Audit Trail Query Acceleration
The Client never directly calls the Ethereum JSON-RPC endpoint to reconstruct history. It queries `GET /api/v1/projects/{id}/audit-trail`. The Server queries the indexed `blockchain_events` table in PostgreSQL, returning sorted, paginated event histories in under 50 milliseconds while providing transaction hashes for independent block explorer verification.

---

## 15. IPFS and Pinata Technical Requirements

### 15.1 Integration Specification
- **Service:** Pinata Cloud Pinning Service (REST API v1).
- **Authentication:** Pinata JWT (`PINATA_JWT`) injected as `Authorization: Bearer <JWT>` header.
- **Upload Endpoint:** `https://api.pinata.cloud/pinning/pinFileToIPFS`.
- **Payload:** `multipart/form-data` containing the file binary stream and Pinata metadata JSON:
  ```json
  {
    "name": "invoice_milestone_1_proj_102.pdf",
    "keyvalues": {
      "projectId": "proj_102",
      "entityType": "FUND_RELEASE_EVIDENCE",
      "uploadedBy": "usr_contractor_01"
    }
  }
  ```

### 15.2 Document Constraints (Technical Decision TD-05 / FRD-DEF-05)
- **Supported MIME Types:** `application/pdf`, `image/jpeg`, `image/png`.
- **Maximum File Size Limit:** 15 MB (15,728,640 bytes). Files exceeding this limit are rejected at the Server boundary with `HTTP 413 Payload Too Large`.
- **Privacy Limitation (CR-23):** Unencrypted storage. All uploaded evidence documents are accessible to anyone possessing the CID. The privacy disclaimer is returned in document metadata APIs.

### 15.3 Gateway Configuration (Technical Decision TD-06 / FRD-DEF-10)
- **Primary Gateway:** Configured via `PINATA_GATEWAY_URL` in `.env` (e.g. `https://gateway.pinata.cloud/ipfs/` or custom Pinata dedicated gateway).
- **Resolution Strategy:** Document retrieval API returns canonical gateway URL: `${PINATA_GATEWAY_URL}/${ipfs_cid}`.
- **Fallback Handling:** If dedicated gateway is unreachable, Client can resolve via standard public IPFS gateway (`https://ipfs.io/ipfs/${ipfs_cid}`).

---

## 16. AI/ML Technical Requirements

### 16.1 Five Confirmed Detection Capabilities

```mermaid
flowchart TD
    subgraph Inputs ["Input Feature Aggregation"]
        F1["Financial Utilization % vs<br/>Physical Progress %"]
        F2["Spend Rate vs Remaining<br/>Timeline (Burn Rate)"]
        F3["Overdue / Missed<br/>Milestone Count"]
        F4["Invoice Metadata / Text<br/>Similarity Score"]
        F5["Transaction Velocity &<br/>Disbursement Amounts"]
    end

    subgraph Detectors ["Detection Algorithms"]
        R1["Rule AI-CAP-01<br/>Progress Mismatch"]
        R2["Rule AI-CAP-02<br/>Budget Overrun"]
        R3["Rule AI-CAP-03<br/>Schedule Delay"]
        R4["Rule AI-CAP-04<br/>Fuzzy Duplicate Match"]
        IF["Isolation Forest AI-CAP-05<br/>Unsupervised Anomaly Model"]
    end

    subgraph Aggregator ["Scoring & Explainability Engine"]
        WeightedSum["Weighted Score Calculator<br/>(Range: 0 - 100)"]
        ExplainEngine["Contributing Factor Generator<br/>(Human-Readable Narratives)"]
    end

    F1 --> R1
    F2 --> R2
    F3 --> R3
    F4 --> R4
    F5 --> IF

    R1 --> WeightedSum
    R2 --> WeightedSum
    R3 --> WeightedSum
    R4 --> WeightedSum
    IF --> WeightedSum

    WeightedSum --> ExplainEngine
```

### 16.2 Feature Vector & Mathematical Specification

| Feature Code | Name | Definition & Formula | Sub-Score Range |
|---|---|---|---|
| f_1 | Utilization-Progress Mismatch | S_1 = min(100, max(0, UtilizationPct - PhysicalProgressPct) * 1.25) | 0–100 |
| f_2 | Budget Overrun Trajectory | S_2 = min(100, max(0, (Disbursed / Budget) - (ElapsedDays / TotalDays)) * 100) | 0–100 |
| f_3 | Schedule Delay Risk | S_3 = min(100, (Count of Missed Milestones) * 35) | 0–100 |
| f_4 | Invoice Similarity Score | S_4 = (Max Levenshtein / Token Similarity against prior invoices) * 100 | 0–100 |
| f_5 | Isolation Forest Anomaly | S_5 = Normalized Decision Function Score from scikit-learn (0 to 100) | 0–100 |

### 16.3 Composite Score Calculation & Weightings (Technical Decision TD-07 / FRD-DEF-09)

The final composite risk score R (integer from 0 to 100) is computed as:
```text
R = min(100, round(0.30 * S_1 + 0.20 * S_2 + 0.20 * S_3 + 0.15 * S_4 + 0.15 * S_5))
```
- **Threshold Flagging:** Configurable via `AI_RISK_THRESHOLD` (default: **65**).
  - If R >= 65: Creates AIFlag (status Open), emits AIAnomalyRecorded blockchain event, routes in-app alert to Auditor.
  - If R < 65: Records analysis log internally; no flag created, no on-chain write, no notification.

### 16.4 Isolation Forest Configuration & Offline Training
- **Algorithm:** `sklearn.ensemble.IsolationForest`
- **Hyperparameters:**
  - `n_estimators`: 100
  - `contamination`: 0.08 (calibrated baseline for synthetic public works data)
  - `max_samples`: 'auto'
  - `random_state`: 42
- **Training Strategy:** Pre-trained on `data/synthetic_transactions.csv` using script `ml/train_isolation_forest.py`.
- **Model Artifact:** Serialized via `joblib.dump()` to `ml/models/isolation_forest.joblib`. Loaded once at FastAPI application startup into memory.

### 16.5 Explainability Generation
Alongside the integer score, the AI generates a structured array of contributing factors formatted as:
```json
[
  {
    "capability": "AI-CAP-01",
    "name": "Financial-Physical Mismatch",
    "severity": "HIGH",
    "metric_observed": "Financial: 80%, Physical: 35%",
    "threshold_delta": "+45%",
    "narrative": "Financial utilization exceeds physical progress by 45 percentage points, exceeding the allowable tolerance margin."
  },
  {
    "capability": "AI-CAP-03",
    "name": "Milestone Delay",
    "severity": "MEDIUM",
    "metric_observed": "1 missed milestone",
    "narrative": "Milestone 'Foundation Works' is past due date without completion."
  }
]
```

---

## 17. AI Execution and Background Processing

### 17.1 Technical Resolution of Deferred Decision (PDQ-03 / FRD-DEF-02)
To preserve fast API response times during HTTP lifecycle transactions, AI analysis executes **asynchronously via FastAPI BackgroundTasks** within the single Server process:

```mermaid
sequenceDiagram
    autonumber
    actor Admin as Government Admin
    participant Server as FastAPI Server
    participant DB as PostgreSQL
    participant Chain as Hardhat Blockchain
    participant BG as Background Task (AI Engine)
    actor Auditor as Auditor

    Admin->>Server: POST /fund-releases/{id}/approve
    Server->>DB: Update FundRelease status to Approved
    Server->>Chain: Submit FundReleaseApproved Tx
    Chain-->>Server: Transaction Receipt Confirmed
    Server->>Server: Enqueue AI Risk Analysis Task
    Server-->>Admin: 200 OK (Lifecycle Action Complete)

    Note over BG: Asynchronous Execution (Non-Blocking)
    BG->>DB: Query Latest Features (Progress, Utilization, Dates)
    BG->>BG: Evaluate Rules + Run Isolation Forest
    alt Risk Score >= 65 (Threshold Crossed)
        BG->>DB: Insert AIFlag (Status: Open)
        BG->>Chain: Submit AIAnomalyRecorded Tx
        BG->>DB: Insert Notification for ACT-05
        Auditor->>Server: Polls /notifications (Receives Alert)
    else Risk Score < 65
        BG->>DB: Insert Audit Analysis Log (Internal Only)
    end
```

### 17.2 Error Isolation and Resilience
- If the AI task throws an unhandled exception (e.g. data feature parsing error or numerical error), the exception is caught and logged to Server error logs.
- The completed fund release approval or milestone creation is **never rolled back**.
- No visible error is shown to the user who triggered the lifecycle action.

---

## 18. Scheduled Deadline Detection

### 18.1 Technical Resolution of Deferred Decision (PDQ-02 / FRD-DEF-01)
- **Mechanism:** In-process periodic asynchronous task using APScheduler (`AsyncIOScheduler`) started during FastAPI lifespan startup.
- **Check Frequency:**
  - Production/Baseline: Every **60 minutes** (`DEADLINE_CHECK_INTERVAL_MINUTES=60`).
  - Testing/Viva Demonstration: Configurable via `.env` down to **1 minute** for rapid live demonstration.

### 18.2 Detection Query & State Update Logic
At each scheduled tick, the scheduler runs:
```sql
UPDATE milestones
SET status = 'Missed/Overdue', missed_at = NOW()
WHERE status = 'Active' 
  AND due_date < CURRENT_DATE
RETURNING id, project_id, name, due_date;
```
For every updated milestone:
1. An in-app alert notification is generated and persisted for the assigned Department Officer (ACT-03) and Government Admin (ACT-02).
2. The updated milestone state is immediately available in PostgreSQL as input signal $f_3$ for subsequent AI risk evaluations.

---

## 19. Notification Technical Requirements

### 19.1 Technical Model
- **Delivery Scope:** In-app notifications only. No SMTP email, Twilio SMS, or web push notifications for MVP.
- **Storage:** Stored in PostgreSQL `notifications` table.
- **Payload Schema:**
  - `id`: UUID Primary Key
  - `user_id`: Target recipient user ID
  - `event_type`: Categorical label (e.g. `MILESTONE_OVERDUE`, `AI_FLAG_CREATED`, `RELEASE_REQUESTED`)
  - `title`: Short title string
  - `message`: Human-readable body text
  - `link_url`: Client routing path (e.g. `/projects/102/milestones` or `/auditor/investigate/45`)
  - `is_read`: Boolean (default `false`)
  - `created_at`: Timestamp

### 19.2 Non-Atomic Lifecycle Decoupling
Notifications are created inside the service workflow in an isolated `try/except` block. A database failure during notification insertion is logged as a warning and **must not abort or rollback the underlying business transaction** (e.g. milestone creation or fund approval remains fully valid).

### 19.3 Client Consumption Contract
- Client polls `GET /api/v1/notifications/unread-count` every 30 seconds or upon route transitions.
- When user clicks notification icon, Client fetches `GET /api/v1/notifications?limit=20`.
- Clicking a notification calls `PATCH /api/v1/notifications/{id}/read` and navigates user to `link_url`.

---

## 20. Data Validation and Consistency

### 20.1 Dual-Tier Validation Contract
Validation rules defined in FRD v1.0.1 (VR-01 through VR-20) are enforced across both tiers:
1. **Client Tier (Zod schemas):** Provides instant interactive feedback, disables invalid submissions, and prevents unnecessary network round-trips.
2. **Server Tier (Pydantic v2 schemas):** Authoritative validation boundary. All inputs are re-validated before execution. Bypassing client validation results in `422 Unprocessable Entity`.

### 20.2 Distributed Non-Atomic Consistency Flow
Because PostgreSQL and Hardhat do not share an atomic distributed transaction coordinator, consistency is achieved through strict sequential orchestration:

```mermaid
flowchart TD
    Start["Receive Lifecycle API Call"] --> Val["Validate Input & Preconditions"]
    Val -->|Precondition Failed| Err1["Return 400 Bad Request"]
    Val -->|Valid| Stage["Stage Pending Record in DB<br/>(or Prepared Transition)"]
    Stage --> Chain["Submit Blockchain Transaction<br/>(web3.py or Wallet Receipt)"]
    Chain --> Conf{"Confirmed on Chain<br/>(Receipt Status == 1)?"}
    Conf -->|No / Reverted| Fail["Rollback DB Record / Set Status Failed<br/>Log Error in Detail"]
    Fail --> Err2["Return 502 Bad Gateway<br/>'Blockchain event could not be recorded.<br/>Please try again.'"]
    Conf -->|Yes| Index["Record Event in BlockchainEvent Index<br/>Finalize PostgreSQL Status"]
    Index --> Notif["Generate In-App Notification<br/>(Non-Blocking)"]
    Notif --> Success["Return 200/201 Success to Client"]
```

---

## 21. Error Handling and Recovery

### 21.1 Standardized Error Response Contract
All error responses adhere to RFC 7807 problem details JSON format:
```json
{
  "error_code": "RESOURCE_CONFLICT",
  "message": "A fund release request is already active for this milestone.",
  "timestamp": "2026-09-28T14:30:00Z",
  "path": "/api/v1/fund-releases",
  "details": {
    "milestoneId": "ms_01HQZ9G81N4V7K002M3B1K9P8Q",
    "activeReleaseId": "rel_01HQZ9J51N4V7K002M3B1K9ABC"
  }
}
```

### 21.2 Error Taxonomy and Recovery Actions

| Category | HTTP Code | Internal Error Code | Safe User-Facing Message | Recovery Behavior |
|---|---|---|---|---|
| **Validation** | 422 | `VALIDATION_ERROR` | "Invalid form input. Please correct highlighted fields." | Client highlights invalid input fields. |
| **Auth** | 401 | `INVALID_CREDENTIALS` | "Invalid email or password." | User re-enters credentials. |
| **Auth Token** | 401 | `TOKEN_EXPIRED` | "Your session has expired. Please log in again." | Client clears local token, redirects to `/login`. |
| **Authorization** | 403 | `FORBIDDEN_ACTION` | "You are not authorized to perform this action." | Action blocked; navigation suggested. |
| **Conflict** | 409 | `RESOURCE_CONFLICT` | "An active request already exists for this milestone." | User waits for existing request resolution. |
| **Blockchain** | 502 | `BLOCKCHAIN_ERROR` | "Blockchain event could not be recorded. Please try again." | Safe retry; DB state remains clean. |
| **IPFS** | 502 | `IPFS_STORAGE_ERROR` | "Evidence document upload failed. Please verify file and retry." | Safe retry; transaction not completed. |
| **AI Failure** | 500 | `AI_PROCESSING_ERROR` | (Logged internally only; no user disruption) | Background retry; lifecycle action unaffected. |

---

## 22. Security Technical Requirements

### 22.1 Authentication & Session Security
- Passwords hashed using bcrypt (12 rounds).
- JWT signed using HS256 with 256-bit entropy secret key.
- Strict token expiration enforced at 60 minutes.
- HTTPS communication required in non-local environments; standard HTTP permitted on `localhost`.

### 22.2 Transport Security & CORS
- CORS headers restricted to explicit Client origin: `http://localhost:5173`.
- `Allow-Credentials: true` with strict header whitelisting (`Authorization`, `Content-Type`).

### 22.3 Input Sanitization & Injection Defense
- **SQL Injection:** Completely mitigated via SQLAlchemy 2.0 parameterized queries and ORM mappings. No raw string concatenation in SQL.
- **XSS Defense:** React JSX natively escapes rendered variables. HTML tags in user descriptions are treated as plaintext strings.
- **Path Traversal:** File uploads do not use client-supplied filenames on the server filesystem. Files are streamed directly to Pinata API.

### 22.4 Academic Scope Security Notice
Security controls are engineered to professional modern web standards suitable for an academic final-year project prototype. The system does not implement hardware security modules (HSM), multi-sig governance smart contracts, automated DDoS mitigation, or zero-trust network architectures required for high-risk sovereign government production deployments.

---

## 23. Logging and Auditability

### 23.1 Logging Infrastructure
- Framework: Python standard `logging` configured with structured formatter.
- Console output formats timestamp, log level, module name, and message.
- Four operational log streams:
  1. `app.access`: HTTP request/response metrics, method, path, status, latency.
  2. `app.lifecycle`: Business workflow transitions (project created, fund released, etc.).
  3. `app.blockchain`: Blockchain transaction hashes, gas used, event indexing receipts.
  4. `app.ai`: Feature inputs, execution times, risk scores, and anomaly flag determinations.

### 23.2 Sensitive Data Masking
Logs strictly redact:
- Plaintext passwords and hashes.
- Full JWT tokens (logged as `Bearer eyJ...[REDACTED]`).
- Private keys (`BLOCKCHAIN_SIGNER_PRIVATE_KEY` is never printed).
- Full Pinata API secret keys.

---

## 24. Configuration and Environment Management

### 24.1 Configuration Management Strategy
All configuration is loaded via Pydantic `BaseSettings` reading from `.env` in the Server root. Default values support zero-config local testing where feasible.

### 24.2 Environment Variables Contract (`.env.example`)

```ini
# ==========================================
# SERVER & ENVIRONMENT CONFIGURATION
# ==========================================
ENVIRONMENT=development
HOST=127.0.0.1
PORT=8000
CORS_ORIGINS=["http://localhost:5173", "http://127.0.0.1:5173"]

# ==========================================
# POSTGRESQL DATABASE
# ==========================================
DATABASE_URL=postgresql+asyncpg://postgres:postgres@localhost:5432/public_fund_db
# Sync URL for Alembic migrations
DATABASE_SYNC_URL=postgresql://postgres:postgres@localhost:5432/public_fund_db

# ==========================================
# AUTHENTICATION & SECURITY
# ==========================================
JWT_SECRET_KEY=development_secret_key_change_in_production_min_32_bytes_long!
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=60

# ==========================================
# HARDHAT BLOCKCHAIN
# ==========================================
BLOCKCHAIN_RPC_URL=http://127.0.0.1:8545
BLOCKCHAIN_CHAIN_ID=31337
CONTRACT_ADDRESS=0x5FbDB2315678afecb367f032d93F642f64180aa3
# Account #0 private key from Hardhat local node
BLOCKCHAIN_SIGNER_PRIVATE_KEY=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80

# ==========================================
# PINATA IPFS STORAGE
# ==========================================
PINATA_API_KEY=your_pinata_api_key_here
PINATA_API_SECRET=your_pinata_api_secret_here
PINATA_JWT=your_pinata_jwt_token_here
PINATA_GATEWAY_URL=https://gateway.pinata.cloud/ipfs

# ==========================================
# AI / ML CONFIGURATION
# ==========================================
AI_RISK_THRESHOLD=65
AI_MODEL_PATH=../ml/models/isolation_forest.joblib

# ==========================================
# SCHEDULER CONFIGURATION
# ==========================================
DEADLINE_CHECK_INTERVAL_MINUTES=60
```

---

## 25. Local Development Environment

### 25.1 Host Prerequisites
- **Node.js:** v18.18+ or v20.x with npm 9+
- **Python:** 3.11.x or 3.12.x
- **PostgreSQL:** Local PostgreSQL server (v15 or v16)
- **Git:** 2.40+
- **Browser:** Chrome, Brave, or Firefox with MetaMask extension installed.

### 25.2 Local Execution and Port Map

| Component | Working Directory | Runtime Command | Default Local Port |
|---|---|---|---|
| **Hardhat Node** | `contracts/` | `npx hardhat node` | `http://127.0.0.1:8545` |
| **Contract Deploy** | `contracts/` | `npx hardhat run scripts/deploy.js --network localhost` | — |
| **PostgreSQL** | System Service | Native Windows / Postgres Service | `localhost:5432` |
| **Server** | `backend/` | `uvicorn app.main:app --reload --port 8000` | `http://127.0.0.1:8000` |
| **Client** | `frontend/` | `npm run dev` | `http://localhost:5173` |

---

## 26. Testing-Oriented Technical Requirements

### 26.1 Testing Architecture
1. **Smart Contracts (`contracts/test`):**
   - Mocha + Chai with Hardhat Network.
   - 100% coverage of access modifiers, event emissions, and rejection logic for all 10 lifecycle events.
2. **Server API & Business Logic (`backend/tests`):**
   - Pytest with `httpx.AsyncClient` and in-memory/test PostgreSQL instance.
   - Comprehensive test fixtures for seeded users, mock Pinata client, and mock Web3 provider.
3. **AI/ML Validation (`backend/tests/test_ai.py`):**
   - Unit tests verifying deterministic score calculations across edge feature combinations.
   - Regression tests verifying threshold crossing triggers `AIFlag` generation.
4. **Client Testing (`frontend/src/**/*.test.jsx`):**
   - Vitest + React Testing Library for form validation rules, route guards, and disclaimer display.

---

## 27. Performance and Responsiveness Requirements

### 27.1 Realistic Demonstration Targets (Aligned with PRD v1.1.0)
Consistent with academic prototype guidelines, hard real-time latency SLAs are not required. The platform targets the representative demonstration workload:
- Up to approximately **50 infrastructure projects**.
- Up to approximately **500 fund release records**.
- Concurrent demonstration users: 1 to 5 active browser sessions.

### 27.2 Responsiveness Guidelines (P95 Benchmarks)
- **Standard REST API reads/writes (PostgreSQL):** < 300 ms.
- **Blockchain transaction confirmation (Hardhat local automine):** < 1500 ms.
- **IPFS document pinning (Pinata REST):** < 3500 ms (subject to local internet bandwidth).
- **Asynchronous AI risk inference:** Background execution completes within 2 to 5 seconds following lifecycle action without holding HTTP response.
- **Client Initial Page Load:** < 2.0 seconds on local development server.

---

## 28. Technical Traceability

| PRD / FRD Ref | Functional Requirement | TRD Section | Technical Component | Technical Mechanism |
|---|---|---|---|---|
| FR-001..005 | Authentication & RBAC | §8 | Server / Auth | JWT HS256, bcrypt hashing, `require_role` dependency. |
| FR-006 | Wallet Connection | §6.4, §13 | Client / Wallet | EIP-1193 provider integration, Hardhat chainId verification. |
| FR-020..024 | Project Management | §9, §10 | Server / DB / API | `projects` table, REST endpoints, `ProjectCreated` event. |
| FR-040..044 | Milestone & Due Dates | §9, §18 | Server / Scheduler | `milestones` table, periodic APScheduler deadline job. |
| FR-050..055 | Fund Release Workflow | §9, §10, §13 | Server / Contracts | 5-state release lifecycle, hybrid signer, `FundRelease*` events. |
| FR-060..063 | Physical Progress | §9, §10 | Server / DB | `project_progress` table, monotonic check, off-chain input to AI. |
| FR-070..075 | Evidence & IPFS | §15 | Server / Pinata | `httpx` multipart upload, CID indexing, PDF/JPEG/PNG <= 15MB. |
| FR-080..083 | Blockchain Audit Trail | §11, §12, §14 | Contracts / Indexer | 10 Solidity events, `PublicFundTracker.sol`, DB indexer. |
| FR-090..099 | AI Risk & Anomaly | §16, §17 | Server / AI Module | Hybrid rules + Isolation Forest, async background task. |
| FR-100..106 | Auditor Investigation | §8, §10, §13 | Client / Server | EIP-1193 wallet sign for finding, off-platform referral status. |
| FR-110..112 | Admin Escalation | §9, §10 | Server / DB | `escalations` table, queue routing, audit linkage. |
| FR-120..122 | Notifications | §19 | Server / Client | In-app alerts in PostgreSQL, decoupled try/except creation. |
| FR-130..135 | Citizen Portal | §6.2, §10 | Client / Public API | Public unauthenticated route, exclusion of internal metadata. |
| FR-140..143 | Dashboards & Reports | §6.5, §10 | Client / Charts | Segregated physical % vs financial utilization %, metrics. |

---

## 29. Technical Decisions, Assumptions, and Deferred Items

### 29.1 Technical Decisions Resolved in This TRD

| Decision ID | Source Reference | Technical Decision | Resolution Summary |
|---|---|---|---|
| **TD-01** | PDQ-02 / FRD-DEF-01 | Scheduled Milestone Check Interval | Set to **60 minutes** baseline; configurable down to **1 minute** for live testing/viva demonstration via `.env`. |
| **TD-02** | PDQ-03 / FRD-DEF-02 | AI Execution Model | Asynchronous in-process execution via FastAPI `BackgroundTasks`. Fast HTTP response; AI failures do not abort lifecycle actions. |
| **TD-03** | PDQ-04 / FRD-DEF-03 | Blockchain Signing Model | **Hybrid Model:** Server relayer wallet signs admin/system events; browser wallet signs Contractor release requests and Auditor findings. |
| **TD-04** | PDQ-08 / FRD-DEF-04 | JWT Token Expiration & Revocation | Access token expiry set to **60 minutes**; stateless JWT validation; re-login required upon expiry. |
| **TD-05** | FRD-DEF-05 | Supported Document MIME & Size | MIME types restricted to **PDF, JPEG, PNG**. Max file size enforced at **15 MB**. |
| **TD-06** | FRD-DEF-06 | Database Schema & Constraints | 11 core tables specified with explicit constraints, foreign keys, indexes, and field types in §9. |
| **TD-07** | FRD-DEF-07 | Server REST API Contract | Structured JSON REST API defined with 35+ endpoints, standard HTTP verbs, and RFC 7807 error schemas in §10. |
| **TD-08** | FRD-DEF-08 | Smart Contract Interface & Events | 10 confirmed event signatures, state structs, and `PublicFundTracker.sol` function signatures defined in §11 & §12. |
| **TD-09** | FRD-DEF-09 | AI Feature Weights & Hyperparameters | 5 features, composite weighting formula ($0.30, 0.20, 0.20, 0.15, 0.15$), and Isolation Forest hyperparameters set in §16. |
| **TD-10** | FRD-DEF-10 | IPFS Gateway Resolution | Direct Pinata dedicated gateway URL with fallback to public gateway in §15. |

### 29.2 Technical Assumptions
- **T-ASS-01:** Local Hardhat automining is sufficient for live evaluation; block mining latency is treated as near-instantaneous (~0.5–1.0s).
- **T-ASS-02:** Local developer machine has open port access for `8000` (FastAPI), `5173` (Vite), `5432` (PostgreSQL), and `8545` (Hardhat).
- **T-ASS-03:** Single-officer assignment per project (`FRD-ASS-02`) is implemented as a 1:1 foreign key in `projects.assigned_officer_id`.
- **T-ASS-04:** Single active fund release request per milestone (`FRD-ASS-01`) is enforced by checking milestone active release status before insert.

### 29.3 Items Deferred to Downstream Artifacts
- **Deferred to System Architecture Document (`docs/04-SYSTEM-ARCHITECTURE.md`):** Detailed sequence diagrams, process thread models, network port topologies, and physical deployment specs.
- **Deferred to UI/UX Design (`docs/05-UI-UX-DESIGN.md`):** Complete screen wireframes, component component trees, typography hierarchies, design system color codes, and responsive layouts.
- **Deferred to Implementation Plan (`docs/06-IMPLEMENTATION-PLAN.md`):** Phase-by-phase development sprint tasks, file scaffolding sequence, and verification milestones.

---

*End of Document — TRD v1.0.0*

*This TRD is based on the approved Project Definition v0.3.0, PRD v1.1.0, and FRD v1.0.1. It is a draft pending review. No application implementation code or project scaffolding shall begin until this TRD is formally reviewed and approved.*
