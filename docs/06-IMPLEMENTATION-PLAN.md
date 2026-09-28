# 06 — Implementation Plan

## 1. Document Control

| Field | Value |
| :--- | :--- |
| **Document Title** | Implementation Plan |
| **File** | `docs/06-IMPLEMENTATION-PLAN.md` |
| **Project** | AI Powered Decentralized Public Fund Tracking and Fraud Detection Platform |
| **Version** | 1.1.0 |
| **Status** | DRAFT — IMPLEMENTATION PLAN REVIEW |
| **Author** | AI Engineering Agent |
| **Date** | 2026-09-28 |
| **Source Baselines** | Project Definition v0.3.0 · PRD v1.1.0 · FRD v1.0.1 · TRD v1.0.1 · System Architecture v1.0.3 · UI/UX Design Spec v1.0.2 |

---

## 2. Purpose and Scope

This document is the final planning document before implementation begins. It provides a comprehensive, dependency-aware blueprint for coding the platform. Every phase, task, and decision herein is derived from the approved project baselines. The plan translates approved architecture and functional requirements into a concrete, ordered execution strategy.

**This document is documentation only.** No source files, directories, packages, or migrations are created by this document.

---

## 3. Source-of-Truth Documents

All implementation decisions derive from the following approved documents, treated as authoritative in this order:

1. `docs/00-PROJECT-DEFINITION.md` — v0.3.0 BASELINE APPROVED
2. `docs/01-PRD.md` — v1.1.0 APPROVED
3. `docs/02-FRD.md` — v1.0.1 APPROVED
4. `docs/03-TRD.md` — v1.0.1 APPROVED
5. `docs/04-SYSTEM-ARCHITECTURE.md` — v1.0.3 APPROVED/FROZEN
6. `docs/05-UI-UX-DESIGN-SPECIFICATION.md` — v1.0.2 APPROVED/FROZEN

**No upstream document is modified by this Implementation Plan.**

---

## 4. Implementation Terminology

All implementation work must use the following approved terminology consistently:

| Term | Meaning |
| :--- | :--- |
| **Client** | React + Vite SPA (`frontend/` directory) |
| **Server** | Python + FastAPI single-process app (`backend/` directory) |
| **PostgreSQL** | Operational/queryable application database |
| **Blockchain** | Tamper-evident audit and lifecycle anchoring layer (Hardhat) |
| **Smart Contract** | Solidity contract deployed to local Hardhat network |
| **Server Relayer** | Server-side blockchain transaction sender (Account #0) |
| **Browser Wallet** | MetaMask — used only for designated Contractor/Auditor operations |
| **IPFS / Pinata** | Off-chain evidence/document storage |
| **AI/ML** | In-process Python component within Server |
| **Indexer** | In-process blockchain event indexer within Server |
| **Scheduler** | APScheduler AsyncIOScheduler within Server |
| **Notification Service** | In-process PostgreSQL-backed notification writer within Server |
| **JWT** | Application authentication/session token (separate from wallet auth) |

> **FORBIDDEN terms:** "frontend server", "backend server", "front-end client", "backend" — never use these when describing architectural components.

---

## 5. Implementation Principles

1. **Dependency-first sequencing:** Design-system foundations, routing, application shells, reusable components, placeholder pages, and static layouts may be implemented before APIs. Workflow UI should connect to real Server APIs once the corresponding API contract exists. Avoid disconnected fake workflow logic that diverges from the real domain model. Never build an API before its database schema is migrated.
2. **Hybrid integrity:** PostgreSQL is authoritative for operational state. Blockchain is the immutable audit layer. They serve distinct purposes and must not duplicate each other's role.
3. **Human-in-the-loop AI:** AI flags anomalies; humans (Auditors) decide. The implementation must never create code pathways that auto-block funds or label fraud.
4. **Non-durable async AI:** `BackgroundTasks` is acceptable and expected. AI failure must not roll back the originating lifecycle operation. Do not introduce Redis/Celery/Kafka.
5. **Fail-safe blockchain operations:** Use the staged DB-transition pattern for all Server-relayed operations. Failure after staging must trigger a clean rollback.
6. **Local demonstrability:** The entire stack must run on a developer laptop without cloud dependencies, except Pinata (external API key).
7. **Proportional engineering:** Academic prototype scope. Prioritize correctness, demonstrability, and clean code over enterprise-scale optimisation.
8. **Flexibility where appropriate:** Implementation choices not frozen by upstream documents remain flexible. Label them accordingly.

---

## 6. Approved Technology Baseline

All of the following are **APPROVED / FROZEN** unless explicitly noted otherwise:

| Layer | Technology | Status |
| :--- | :--- | :--- |
| Client framework | React + Vite | FROZEN |
| Client styling | Tailwind CSS | FROZEN |
| Client components | shadcn/ui | FROZEN |
| Server framework | Python + FastAPI (single process) | FROZEN |
| Database | PostgreSQL 15+ | FROZEN |
| Blockchain runtime | Hardhat (local, Chain ID 31337) | FROZEN |
| Smart contract language | Solidity ^0.8.20 | FROZEN |
| Smart contract libraries | OpenZeppelin 5.x (`Ownable`, `ReentrancyGuard`) | FROZEN |
| IPFS provider | Pinata API | FROZEN |
| AI/ML | scikit-learn (Isolation Forest) + rule-based hybrid | FROZEN |
| Auth | JWT HS256, 256-bit secret, 60-min expiry | FROZEN |
| Browser wallet | MetaMask | FROZEN |
| Scheduler | APScheduler `AsyncIOScheduler` in FastAPI lifespan | FROZEN |
| Async AI execution | FastAPI `BackgroundTasks` (non-durable) | FROZEN |
| Contract testing | Hardhat / Mocha / Chai | FROZEN |
| Server testing | pytest + `httpx.AsyncClient` | FROZEN |
| Client testing | Vitest + React Testing Library | FROZEN |
| Icon library | `lucide-react` | IMPLEMENTATION CHOICE |
| Charting library | `recharts` or `chart.js` | IMPLEMENTATION CHOICE (evaluate during Phase 16) |
| Table library | `@tanstack/react-table` | IMPLEMENTATION CHOICE |
| Form/validation | `react-hook-form` + `zod` | IMPLEMENTATION CHOICE |
| Animation | `framer-motion` | IMPLEMENTATION CHOICE |
| Data fetching | `react-query` or RTK Query | IMPLEMENTATION CHOICE |

---

## 7. Target Repository Structure

```text
/ (monorepo root)
├── .env.example                  (Root env template)
├── AGENTS.md                     (Agent rules)
├── docs/                         (All project documentation)
├── frontend/                     (Client — React + Vite)
│   ├── public/
│   ├── src/
│   │   ├── assets/               (Static images, fonts)
│   │   ├── components/           (Reusable UI components)
│   │   │   ├── ui/               (shadcn primitive overrides)
│   │   │   ├── layout/           (Shell, Sidebar, Nav)
│   │   │   ├── charts/           (Data visualizations)
│   │   │   └── shared/           (Badges, Cards, Timelines, etc.)
│   │   ├── pages/                (Role-based page views)
│   │   │   ├── auth/
│   │   │   ├── admin/
│   │   │   ├── gov-admin/
│   │   │   ├── officer/
│   │   │   ├── contractor/
│   │   │   ├── auditor/
│   │   │   └── public/           (Citizen portal)
│   │   ├── hooks/                (Custom React hooks)
│   │   ├── services/             (API client layer — Axios/Fetch)
│   │   ├── store/                (Client-side state)
│   │   ├── lib/                  (Utilities, formatters)
│   │   └── wallet/               (Ethers.js / MetaMask integration)
│   ├── vite.config.ts
│   ├── tailwind.config.ts
│   └── .env.example
│
├── backend/                      (Server — FastAPI)
│   ├── app/
│   │   ├── main.py               (FastAPI app + lifespan)
│   │   ├── core/
│   │   │   ├── config.py         (Settings / env vars)
│   │   │   ├── security.py       (JWT logic, password hashing)
│   │   │   └── dependencies.py   (Auth dependencies)
│   │   ├── api/
│   │   │   └── v1/
│   │   │       ├── auth.py
│   │   │       ├── admin.py
│   │   │       ├── projects.py
│   │   │       ├── milestones.py
│   │   │       ├── progress.py
│   │   │       ├── funds.py
│   │   │       ├── documents.py
│   │   │       ├── ai_risk.py
│   │   │       ├── audit.py
│   │   │       ├── notifications.py
│   │   │       └── public.py
│   │   ├── models/               (SQLAlchemy ORM models)
│   │   ├── schemas/              (Pydantic request/response schemas)
│   │   ├── services/             (Business logic layer)
│   │   │   ├── project_service.py
│   │   │   ├── fund_service.py
│   │   │   ├── audit_service.py
│   │   │   └── ...
│   │   ├── db/
│   │   │   ├── session.py        (Async engine, session factory)
│   │   │   └── migrations/       (Alembic)
│   │   ├── blockchain/
│   │   │   ├── relayer.py        (Server Relayer — Account #0)
│   │   │   ├── indexer.py        (In-process event indexer)
│   │   │   └── contract.py       (ABI loader, Web3 contract instance)
│   │   ├── ipfs/
│   │   │   └── pinata.py         (Pinata upload/CID service)
│   │   ├── ai/
│   │   │   ├── inference.py      (Inference entry point)
│   │   │   ├── features.py       (Feature engineering)
│   │   │   ├── rules.py          (Rule-based signals)
│   │   │   └── model_loader.py   (Load fitted Isolation Forest)
│   │   ├── tasks/
│   │   │   ├── scheduler.py      (APScheduler AsyncIOScheduler)
│   │   │   └── ai_tasks.py       (BackgroundTasks wrappers)
│   │   └── notifications/
│   │       └── service.py
│   ├── tests/
│   ├── alembic.ini
│   ├── requirements.txt
│   └── .env.example
│
├── contracts/                    (Hardhat project)
│   ├── contracts/
│   │   └── FundTracker.sol
│   ├── scripts/
│   │   └── deploy.js
│   ├── test/
│   │   └── FundTracker.test.js
│   ├── hardhat.config.js
│   └── package.json
│
├── ml/                           (AI/ML training scripts)
│   ├── data/                     (Synthetic training data)
│   ├── features/                 (Feature engineering scripts)
│   ├── train_isolation_forest.py
│   ├── evaluate.py
│   └── models/                   (Exported model files — isolation_forest.joblib)
│
└── data/                         (Seed / demo datasets)
    ├── seed_users.sql
    ├── seed_projects.sql
    └── seed_demo_scenario.py
```

> **Note:** Internal subdirectory structure is a **PROPOSAL**. Exact layout may be adjusted during implementation without violating the architecture.

---

## 8. Environment and Toolchain Prerequisites

The following tools must be installed on the development machine before any phase begins. Exact patch versions are **IMPLEMENTATION CHOICES** unless noted.

| Prerequisite | Notes |
| :--- | :--- |
| **Node.js** | v18 LTS or v20 LTS (IMPLEMENTATION CHOICE) |
| **Python** | v3.10 or v3.11 (IMPLEMENTATION CHOICE) |
| **PostgreSQL** | v15+ (FROZEN — per TRD) |
| **MetaMask** | Browser extension, configured to `localhost:8545`, Chain ID 31337 |
| **Pinata Account** | Free tier sufficient; Pinata JWT and dedicated gateway URL required (`PINATA_JWT`, `PINATA_GATEWAY_URL`, `IPFS_GATEWAY_URL`) |
| **Git** | Version control |
| **npm / pnpm** | Package manager for Client and Hardhat (IMPLEMENTATION CHOICE) |
| **pip + venv** | Python virtual environment (REQUIRED) |

---

## 9. Dependency and Package Strategy

> **IMPORTANT:** No packages are installed during this planning task.

**Strategy:**
- **Client:** Use `npm` (or `pnpm`) with `package-lock.json` (or `pnpm-lock.yaml`) committed to pin exact versions after initial install.
- **Server:** Use `pip` with `requirements.txt` pinned after initial install. Python virtual environment (`venv`) is mandatory.
- **Contracts:** Use `npm` within `contracts/` directory for Hardhat toolchain.
- **Candidate UI Libraries (IMPLEMENTATION CHOICES — evaluate, do not install all):**
  - `lucide-react` — Icon set (already included with shadcn/ui)
  - `recharts` or `chart.js` — Data visualization
  - `@tanstack/react-table` — Headless data table
  - `react-hook-form` + `zod` — Form management and validation
  - `framer-motion` — UI transitions (evaluate necessity)
  - `@tanstack/react-query` or RTK Query — Server state management

**Version strategy:** Pin major versions; allow compatible minor/patch updates. Lock files are the authority during build.

---

## 10. Configuration and Environment Variables

Real secrets must never be committed. Each package directory will have a `.env.example` file.

**Client (`frontend/.env`):**

| Variable | Purpose |
| :--- | :--- |
| `VITE_API_BASE_URL` | Base URL for Server REST API (e.g., `http://localhost:8000`) |
| `VITE_BLOCKCHAIN_RPC_URL` | Local Hardhat node RPC (e.g., `http://127.0.0.1:8545`) |
| `VITE_CHAIN_ID` | `31337` (FROZEN) |
| `VITE_CONTRACT_ADDRESS` | Address of deployed `FundTracker.sol` |

**Server (`backend/.env`):**

| Variable | Purpose |
| :--- | :--- |
| `DATABASE_URL` | Async PostgreSQL connection string |
| `JWT_SECRET` | 256-bit random secret (NEVER commit real value) |
| `JWT_EXPIRY_MINUTES` | `60` (FROZEN) |
| `BLOCKCHAIN_RPC_URL` | Local Hardhat RPC URL |
| `RELAYER_PRIVATE_KEY` | Hardhat Account #0 private key (NEVER commit real value) |
| `CONTRACT_ADDRESS` | Deployed contract address |
| `CHAIN_ID` | `31337` |
| `PINATA_JWT` | Pinata API JWT Bearer token |
| `PINATA_GATEWAY_URL` | Dedicated Pinata gateway URL for evidence retrieval |
| `IPFS_GATEWAY_URL` | Public IPFS gateway for fallback / retrieval |
| `DEADLINE_CHECK_INTERVAL_MINUTES` | `60` default, `1` for demo (FROZEN — per TRD) |
| `INDEXER_POLL_INTERVAL_SECONDS` | `15` default, `2` for demo (IMPLEMENTATION CHOICE) |
| `AI_RISK_THRESHOLD` | IMPLEMENTATION CHOICE — calibrated during Phase 13 |
| `ENVIRONMENT` | `development` / `test` |

---

## 11. Implementation Dependency Graph

The following shows the implementation dependency order. No phase may begin before its prerequisites are complete.

```text
Phase 0 (Environment / Repository Setup)
├── Phase 1 (Client Foundation)
│   └── Phase 4 (Auth Client) → Role-specific UI dashboards
│
├── Phase 2 (Server Foundation)
│   └── Phase 3 (DB Schema)
│       └── Phase 4 (Auth & RBAC)
│           └── Phase 8 (Project Management)
│               └── Phase 9 (Milestone & Physical Progress)
│                   └── evidence-capable IPFS prerequisite (Phase 11)
│                       └── Phase 10 (Fund Release Lifecycle)
│
└── Phase 5 (Smart Contract)
    └── Phase 6 (Server Relayer + Browser Wallet Integration)
        └── Phase 7 (Blockchain Indexer)
            └── (Integrated into lifecycle phases 8–14)

Phase 12 (Deadline Scheduler + Notifications) — Depends on milestone/project state model
Phase 13 (AI/ML) — Depends on project, milestone, fund release, progress, and invoice data structures
Phase 14 (Auditor Workflow) — Depends on AI flags, Gov Admin escalation, and blockchain finding flow
Phase 15 (Citizen Portal) — Depends on public project/query APIs
Phase 16 (UI Polish / Integration) — Runs as core screens become available
Phase 17 (Testing) — Runs throughout; formalized here
Phase 18 (Seed Data + Demo) — After required MVP workflows exist
Phase 19 (Hardening / Finalization) — Final
```

---

## 12. Phase 0 — Repository and Environment Readiness

**Objective:** Set up the monorepo structure and verify all tools are installed.

**Why this phase exists:** No implementation can begin without a clean workspace, working toolchain, and verified environment.

**Prerequisites:** None.

**Tasks:**
1. Create root monorepo directory structure: `frontend/`, `backend/`, `contracts/`, `ml/`, `data/`, `docs/`.
2. Initialize `frontend/` with Vite + React + TypeScript template.
3. Initialize `backend/` with Python virtual environment and bare FastAPI `main.py`.
4. Initialize `contracts/` with Hardhat project (`hardhat.config.js`, `contracts/`, `test/`, `scripts/`).
5. Create root `.gitignore` covering `node_modules/`, `__pycache__/`, `.env`, `*.joblib`, etc.
6. Create `.env.example` files for `frontend/` and `backend/`.
7. Verify: `node --version`, `python --version`, `psql --version`, `npx hardhat --version`.

**Database impact:** Not applicable.
**API impact:** Not applicable.
**Client impact:** Empty Vite project shell created.
**Blockchain impact:** Hardhat project scaffolded; no contract yet.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:** Manual verification of toolchain installation.

**Exit criteria:**
- `npm run dev` in `frontend/` serves a blank page.
- `python -m uvicorn app.main:app --reload` in `backend/` starts without error.
- `npx hardhat compile` in `contracts/` succeeds (no contracts yet, but toolchain works).
- PostgreSQL server is reachable.

**Definition of done:** All tools verified. Skeleton directories committed.

---

## 13. Phase 1 — Client Foundation

**Objective:** Establish the React/Vite Client application shell, routing, theme, and design system foundation.

**Why this phase exists:** All UI phases depend on a working layout shell, routing system, and design tokens.

**Prerequisites:** Phase 0 complete.

**Components/files expected:**
- `frontend/src/App.tsx` — Root router
- `frontend/src/components/layout/AppShell.tsx` — Authenticated layout
- `frontend/src/components/layout/Sidebar.tsx` — Role-based navigation
- `frontend/src/components/layout/TopNav.tsx` — Breadcrumbs, theme toggle, notifications bell
- `frontend/tailwind.config.ts` — Design tokens configured
- `frontend/src/store/authStore.ts` — Auth context/state (user, role, JWT)
- `frontend/src/lib/api.ts` — Base Axios/Fetch client with JWT header injection

**Tasks:**
1. Install and configure Tailwind CSS and shadcn/ui. Configure design tokens (colors, typography, spacing, radius) per UI/UX Spec Sections 7 and 8.
2. Set up React Router with route groups: `/auth/*`, `/admin/*`, `/gov/*`, `/officer/*`, `/contractor/*`, `/auditor/*`, `/public/*`.
3. Implement protected route wrapper that checks JWT presence and redirects to `/auth/login` on absence.
4. Implement role-based route guard that redirects to the appropriate dashboard based on the JWT's role claim.
5. Build `AppShell` with collapsible sidebar (role-aware navigation items) and top navigation.
6. Implement Dark/Light/System theme toggle using a CSS custom-property approach (Tailwind `darkMode: 'class'`).
7. Implement skeleton loader, empty state, and error boundary components.
8. Implement the base API client with automatic JWT header attachment and 401 handling (redirect to login).
9. Create placeholder pages for all major routes (AUTH-01 through PUB-03) to validate routing.

**Database impact:** Not applicable.
**API impact:** Not applicable — uses placeholder/mock data.
**Blockchain impact:** Not applicable.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:** Vitest routing tests; manually verify sidebar renders correct items for each simulated role.

**Exit criteria:**
- Theme toggle switches dark/light correctly.
- Protected routes redirect unauthenticated users to login.
- Role guard redirects authenticated users to the correct dashboard.
- Sidebar renders role-appropriate navigation.

**Definition of done:** Client shell navigable for all 6 roles using mock auth state.

---

## 14. Phase 2 — Server Foundation

**Objective:** Establish the FastAPI Server core: app factory, CORS, exception handling, health check, and async database connection.

**Why this phase exists:** All Server APIs and services depend on the base FastAPI application and database connectivity.

**Prerequisites:** Phase 0 complete. PostgreSQL server running.

**Components/files expected:**
- `backend/app/main.py` — FastAPI app with lifespan handler
- `backend/app/core/config.py` — Pydantic `Settings` reading from environment
- `backend/app/core/exceptions.py` — Global exception handlers (RFC 7807 Problem Details)
- `backend/app/db/session.py` — Async SQLAlchemy engine and session factory
- `backend/app/api/v1/__init__.py` — Router registry

**Tasks:**
1. Create FastAPI application with `lifespan` context manager (for Scheduler and Indexer startup/shutdown in later phases).
2. Configure CORS (`CORSMiddleware`) to allow Client origin.
3. Implement global exception handlers returning RFC 7807 `application/problem+json` format for `HTTPException`, `ValidationError`, and generic `Exception`.
4. Create `GET /health` endpoint returning `{"status": "ok", "db": "connected"}`.
5. Implement async SQLAlchemy engine (`asyncpg` driver) and session dependency.
6. Configure Pydantic `Settings` model loading from `.env` for all required variables.
7. Implement structured logging setup (Python `logging` module).

**Database impact:** Database connection verified; no schema yet.
**API impact:** `/health` endpoint live.
**Client impact:** Not applicable.
**Blockchain impact:** Not applicable.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:** `pytest` test for `/health` returning 200.

**Exit criteria:**
- Server starts without error.
- `/health` returns `{"status": "ok"}` when DB is reachable.
- A DB disconnect returns a meaningful error, not an unhandled exception.

**Definition of done:** Stable Server foundation; RFC 7807 error format confirmed.

---

## 15. Phase 3 — PostgreSQL Schema and Migrations

**Objective:** Create the full operational database schema using Alembic migrations, implementing the data model defined in TRD Section 9.

**Why this phase exists:** All business logic depends on a correctly migrated schema with proper constraints, indexes, and foreign keys.

**Prerequisites:** Phase 2 complete. PostgreSQL reachable. TRD Section 9 (PostgreSQL Technical Requirements) used as the specification.

**Implementation sequence (migration order):**

| Migration | Tables Created | Notes |
| :--- | :--- | :--- |
| `001_users` | `users` | UUID PK, role enum, password hash, wallet address, status, timestamps |
| `002_projects` | `projects` | UUID PK, on_chain_id (bigint), budget fields, status enum, timestamps |
| `003_milestones` | `milestones` | UUID PK, on_chain_id, FK → projects, deadline, status, timestamps |
| `004_physical_progress` | `physical_progress` | UUID PK, FK → milestones, FK → users (officer), percent, notes, timestamps |
| `005_fund_releases` | `fund_releases` | UUID PK, on_chain_id, FK → milestones, FK → contractor, amount, status enum, timestamps |
| `006_documents` | `documents` | UUID PK, FK → fund_releases OR audit_findings (nullable), CID, filename, mime_type, file_size, uploaded_by, timestamps |
| `007_ai_flags` | `ai_flags` | UUID PK, on_chain_id (bigint), FK → projects, risk_score, factors (JSONB), status, timestamps |
| `008_escalations` | `escalations` | UUID PK, FK → projects, FK → escalated_by (Gov Admin), reason, status, timestamps |
| `009_audit_findings` | `audit_findings` | UUID PK, on_chain_id (bigint), FK → ai_flags (nullable), FK → escalations (nullable), narrative, outcome, report_cid, timestamps |
| `010_blockchain_index` | `blockchain_index` | UUID PK, event_name, tx_hash, block_number, log_index (unique), on_chain_id_ref, processed_at |
| `011_notifications` | `notifications` | UUID PK, FK → users, category, message, read, deep_link, timestamps |

**Key schema rules:**
- All application PKs are UUID (`gen_random_uuid()`).
- All on-chain entity references use a separate `on_chain_id BIGINT` column (never cast UUID).
- `status` fields use PostgreSQL `ENUM` or `VARCHAR` with `CHECK` constraints per TRD.
- `fund_releases.status` states: `Pending` → `Verified` → `Approved` | `Rejected`.
- `projects.status` states: `Draft` → `Active` → `Completed`.
- `ai_flags.status` states: `Open` → `Under Review` → `Reviewed — No Action` | `Escalated Externally` (per TRD Section 9.2).
- Foreign key constraints enforce referential integrity.
- Indexes on: `users.email` (unique), `fund_releases.milestone_id + status`, `ai_flags.project_id`, `blockchain_index.(tx_hash, log_index)` (unique — prevents duplicate indexing).

**Database impact:** Full schema migrated. `alembic upgrade head` applies all migrations cleanly.
**API impact:** Not applicable yet.
**Client impact:** Not applicable.
**Blockchain impact:** `on_chain_id` columns prepared; not populated until Phase 6+.
**IPFS impact:** `documents.cid` column exists; not used until Phase 11.
**AI impact:** `ai_flags` table exists; not populated until Phase 13.

**Testing requirements:**
- Run `alembic upgrade head` on a fresh database.
- Run `alembic downgrade base` and `alembic upgrade head` again (verify reversibility).
- Write a pytest fixture that creates an isolated test database using the same migrations.

**Exit criteria:**
- `alembic upgrade head` completes with zero errors on a fresh DB.
- All tables, constraints, indexes, and enums exist as specified.
- Test DB fixture works.

**Definition of done:** Migrations idempotent. Test database usable. Schema consistent with TRD.

---

## 16. Phase 4 — Authentication and RBAC

**Objective:** Implement JWT-based authentication, password hashing, role-based authorization, and the live DB-status check.

**Why this phase exists:** Authentication gates every subsequent API. RBAC determines which actors can perform which operations.

**Prerequisites:** Phase 3 complete (users table exists).

**Components/files expected:**
- `backend/app/core/security.py` — `create_access_token()`, `verify_token()`, `hash_password()`, `verify_password()`
- `backend/app/core/dependencies.py` — `get_current_user()` dependency
- `backend/app/api/v1/auth.py` — `POST /api/v1/auth/login`, `GET /api/v1/auth/me`
- `backend/app/services/user_service.py` — User CRUD, role management

**Tasks:**
1. Implement `hash_password()` using `bcrypt`.
2. Implement JWT creation: HS256, 256-bit secret from env, 60-minute expiry (FROZEN).
3. Implement `get_current_user()` FastAPI dependency:
   - Extract and validate JWT from `Authorization: Bearer` header.
   - Decode user ID from token payload.
   - **Query PostgreSQL for current user** — get live `status` and `role` fields.
   - Reject with `403` if user is `inactive`.
   - Attach live user object to request state.
4. Implement `require_role(*roles)` dependency factory for endpoint-level RBAC.
5. Implement `POST /api/v1/auth/login`:
   - Validate credentials against DB.
   - Return `access_token`, `token_type`, `expires_in`, `role`.
6. Implement `GET /api/v1/auth/me` — returns current user details (no password hash).
7. Implement Platform Admin endpoints for user management: list users, create user, update role, toggle active status.
8. Seed the first Platform Admin user (via migration or seed script) so the system is bootstrappable.

**Auth rules (FROZEN):**
- **No JWT blacklist.** Role changes and deactivation take effect immediately on the *next request* because the Server always reads live DB status.
- Wallet addresses are stored on user profiles but are separate from JWT authentication.

**Database impact:** `users` table read and written.
**API impact:** `/auth/login`, `/auth/me`, `/admin/users` endpoints live.
**Client impact:** Client login page (AUTH-01) can now authenticate. JWT stored in memory or `sessionStorage`.
**Blockchain impact:** Not applicable.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:**
- pytest: Valid login returns JWT; invalid credentials return 401.
- pytest: Deactivated user receives 403 on protected endpoint even with valid JWT.
- pytest: Role mismatch returns 403.
- pytest: `require_role('government_admin')` allows Gov Admin; blocks Contractor.

**Exit criteria:**
- Login flow works end-to-end.
- Live DB role/status check confirmed in tests.
- Platform Admin user management working.
- Client can log in, receive JWT, and access role-appropriate dashboard.

**Definition of done:** Authentication and RBAC verified by tests. Client Login page (AUTH-01) functional.

---

## 17. Phase 5 — Smart Contract and Hardhat

**Objective:** Write, test, and locally deploy the Solidity `FundTracker` smart contract.

**Why this phase exists:** All blockchain integration phases depend on a verified, deployable contract.

**Prerequisites:** Phase 0 complete. Node.js and Hardhat installed.

**Components/files expected:**
- `contracts/contracts/FundTracker.sol`
- `contracts/scripts/deploy.js`
- `contracts/test/FundTracker.test.js`
- `contracts/hardhat.config.js`

**Smart contract design (per TRD Sections 11–12):**

Implement the `PublicFundTracker` contract exactly according to TRD Sections 11–13. This phase creates the Solidity source, tests all approved functions and events, deploys locally, and verifies ABI compatibility with the Server and Client.

*Core Storage (per TRD Section 12.1 — minimal, not duplicating PostgreSQL):*
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

*Ten Confirmed Events (FROZEN — per TRD Section 11.3 — DO NOT alter signatures):*
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
    uint256 indexed findingId,
    uint256 indexed flagId,
    address indexed auditorWallet,
    uint8 findingOutcome, // 0 = NoAction, 1 = EscalatedExternal
    string reportCid,
    uint256 timestamp
);
// Note: flagId = associated AI flag's on_chain_id. flagId = 0 when finding
// originates from a Government Admin escalation without an AI flag.

event ProjectCompleted(
    uint256 indexed projectId,
    uint256 finalDisbursedAmount,
    uint256 timestamp
);
```

*Ten Approved Functions (FROZEN — per TRD Section 12.2):*
1. `createProject(uint256 projectId, uint256 totalBudget, string calldata name) external onlyOwner`
2. `allocateBudget(uint256 projectId, uint256 amount) external onlyOwner`
3. `defineMilestone(uint256 projectId, uint256 milestoneId, uint256 budgetPortion, bytes32 dueDateHash) external onlyOwner`
4. `requestFundRelease(uint256 projectId, uint256 milestoneId, uint256 requestedAmount, string calldata evidenceCids) external nonReentrant`
5. `recordOfficerVerification(uint256 projectId, uint256 milestoneId, uint8 recommendationOutcome, string calldata reportCid, address officer) external onlyOwner`
6. `approveFundRelease(uint256 projectId, uint256 milestoneId, uint256 approvedAmount) external onlyOwner nonReentrant`
7. `rejectFundRelease(uint256 projectId, uint256 milestoneId, bytes32 rejectionReasonHash) external onlyOwner`
8. `recordAIAnomaly(uint256 projectId, uint256 flagId, uint8 riskScore, string calldata anomalyType) external onlyOwner`
9. `recordAuditFinding(uint256 projectId, uint256 findingId, uint256 flagId, uint8 findingOutcome, string calldata reportCid) external nonReentrant`
10. `completeProject(uint256 projectId) external onlyOwner`

*Access control (per TRD Section 12.2):*
- Server Relayer (Account #0) = contract `owner` (via OpenZeppelin `Ownable`). Constructor: `Ownable(initialOwner)` per OpenZeppelin 5.x requirement.
- `onlyOwner` modifier on all Server-relayed functions (1–3, 5–8, 10).
- Contractor/Auditor wallet-signed functions (`requestFundRelease`, `recordAuditFinding`): open to any caller; Server verifies wallet ownership independently from the receipt.
- `ReentrancyGuard` on state-changing functions.

*Local Hardhat accounts (FROZEN):*
- Account #0: Server Relayer / Deployer
- Account #1: Contractor demo wallet
- Account #2: Auditor demo wallet
- Chain ID: 31337

**Implementation sequence:**
1. Create contract skeleton with inheritance and constructor.
2. Define all approved state variables.
3. Implement all 10 approved functions with correct signatures.
4. Implement all 10 approved events with correct signatures.
5. Implement access control (`onlyOwner`, `nonReentrant`).
6. Implement input validation (project/milestone existence checks).
7. Write `deploy.js` deploying to `localhost:8545`, logging contract address.
8. Write Mocha/Chai tests:
   - Event emission for all 10 events with correct parameters.
   - `onlyOwner` reverts on non-owner calls.
   - `requestFundRelease` callable by Contractor (Account #1).
   - `recordAuditFinding` callable by Auditor (Account #2).
   - State validation (nonexistent project, duplicate).
9. Verify exported ABI is compatible with Server (web3.py) and Client (ethers.js).

**Database impact:** Not applicable.
**API impact:** Not applicable.
**Client impact:** Not applicable.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:**
- `npx hardhat test` must pass all contract tests before Phase 6 begins.

**Phase Gate:**
> ⛔ **Do not proceed to Phase 6 until:** Contract compiles, all Hardhat tests pass, deploy script runs successfully on `localhost:8545`, and the deployed contract address is captured.

**Exit criteria:**
- `npx hardhat test` passes 100%.
- `npx hardhat run scripts/deploy.js --network localhost` succeeds.
- Contract address available for environment configuration.

**Definition of done:** Contract deployed locally. All events verified in tests. Address captured in `.env`.

---

## 18. Phase 6 — Blockchain Service, Relayer and Wallet Integration

**Objective:** Implement the Server-side blockchain relayer and the Client-side MetaMask integration for the two designated browser-wallet workflows.

**Why this phase exists:** Fund release requests (Contractor) and audit findings (Auditor) use browser-wallet signing. All administrative operations use the Server Relayer. Both paths must exist before the fund lifecycle can be implemented.

**Prerequisites:** Phase 5 complete (contract deployed, address in `.env`). Phase 2 complete (Server foundation).

**Components/files expected:**
- `backend/app/blockchain/contract.py` — Loads ABI, creates Web3 contract instance
- `backend/app/blockchain/relayer.py` — `BlockchainRelayer` singleton; signs and broadcasts via Account #0
- `backend/app/blockchain/wallet_verifier.py` — Verifies browser-wallet transaction receipts
- `frontend/src/wallet/WalletContext.tsx` — Ethers.js provider, MetaMask connection state
- `frontend/src/wallet/useWallet.ts` — Hook for connect, sign, get address

**Server Relayer implementation (APPROVED flow — FROZEN):**
```
Validate request
→ Stage PostgreSQL transition (set status to intermediate)
→ Call contract function via relayer (Account #0)
→ Await transaction receipt (confirmation)
→ On SUCCESS: finalize PostgreSQL state, store tx_hash + block_number
→ On FAILURE: rollback PostgreSQL to pre-transaction state, log error, return HTTP 500
```

**Browser Wallet verification (APPROVED flow — FROZEN):**
```
Client: Actor fills form → submits → Client constructs transaction → MetaMask prompts
→ Actor signs → Transaction broadcasts to Hardhat
→ Client receives receipt (tx_hash, block_number)
→ Client sends POST to Server: { tx_hash, context_data }
→ Server: Fetch transaction receipt from Hardhat
→ Server: Verify method called, parameters, registered wallet address match the actor
→ On VALID: finalize PostgreSQL state
→ On INVALID: reject with 400/403
```

**Wallet assignment (FROZEN):**
- Account #0: Server Relayer / Deployer (private key in Server `.env`)
- Account #1: Contractor demo wallet
- Account #2: Auditor demo wallet

**Client Wallet Integration:**
- `WalletContext` wraps the application for Contractor and Auditor routes.
- Shows "Connect Wallet" button if MetaMask is not connected.
- Displays truncated connected address in the top navigation.
- Handles MetaMask rejection gracefully with error toast.
- Shows "Waiting for MetaMask confirmation" modal during pending signing.

**Database impact:** On lifecycle finalization, the Server stores `tx_hash` and `block_number` on the relevant entity (e.g., `fund_releases.tx_hash`). The **Indexer (Phase 7) is the sole authoritative writer of `blockchain_index` event rows** — Phase 6 does NOT write to `blockchain_index` independently.
**API impact:** No new public endpoints yet; relayer/verifier are internal services called by later API phases.
**Client impact:** Wallet connection UI in Contractor and Auditor layouts.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:**
- pytest: Mock Web3 provider; test that relayer broadcasts a transaction and finalizes DB state on success.
- pytest: Relayer failure (simulated) triggers PostgreSQL rollback.
- pytest: Wallet verifier rejects a mismatched wallet address.
- Client test: MetaMask rejection shows error toast.

**Phase Gate:**
> ⛔ **Do not proceed to Phase 8 until:** Server Relayer can broadcast a transaction and receive a receipt. Browser wallet verifier can validate a receipt. Both failure paths tested.

**Exit criteria:**
- Server Relayer functional (tested with mock Hardhat).
- Wallet verifier rejects invalid receipts.
- Client connects to MetaMask and displays address.

**Definition of done:** Both blockchain transaction paths are operational and tested.

---

## 19. Phase 7 — Blockchain Event Indexer

**Objective:** Implement the in-process Indexer that polls Hardhat, decodes events, and synchronizes applicable state into PostgreSQL.

**Why this phase exists:** The audit trail displayed to users requires mapping on-chain events back to PostgreSQL records. The Indexer enables the Client to display blockchain-backed event timelines without querying the chain directly.

**Prerequisites:** Phase 5 (contract deployed), Phase 3 (schema — `blockchain_index` table exists).

**Components/files expected:**
- `backend/app/blockchain/indexer.py` — Polling loop + event decoder + DB writer (runs as an async background task in FastAPI lifespan, completely separate from APScheduler)

**Indexer design:**
1. **Startup:** On Server lifespan startup, Indexer reads `MAX(block_number)` from `blockchain_index` table as the `last_processed_block`.
2. **Polling loop:** Every N seconds (configurable via env; default 15s for dev, 2s for demo; IMPLEMENTATION CHOICE, separate from Deadline Scheduler), call `contract.events.EventName().get_logs(fromBlock=last_processed_block+1, toBlock='latest')`.

> **Architectural Separation:** The Indexer is strictly an asynchronous polling loop for blockchain events that writes decoded event history to `blockchain_index`. It is NOT an APScheduler job and does NOT handle milestone deadlines.
3. **Event processing:** For each log:
   - Extract `event_name`, `tx_hash`, `block_number`, `log_index`, `on_chain_id` from args.
   - Check `blockchain_index` for `(tx_hash, log_index)` uniqueness — skip if already processed (idempotency).
   - Resolve `on_chain_id` → PostgreSQL UUID via lookup in the relevant table.
   - Write a row to `blockchain_index`.
   - Update any applicable PostgreSQL fields (e.g., `tx_hash` on `fund_releases`).
4. **Restart safety:** Because `last_processed_block` comes from the DB, restarting the Server always resumes from the correct block.
5. **Unknown events:** Log a warning; do not crash.
6. **Malformed events:** Log an error; skip that event; continue polling.

**Database impact:** `blockchain_index` table written. `on_chain_id` fields updated on relevant tables.
**API impact:** `GET /api/v1/projects/{id}/audit-trail` endpoint can now be implemented (reads `blockchain_index`).
**Client impact:** Blockchain audit timeline (AUDIT-01) becomes populatable.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Testing requirements:**
- pytest: Emit a `ProjectCreated` event on Hardhat; confirm Indexer writes correct row to `blockchain_index`.
- pytest: Duplicate events are not double-processed.
- pytest: Indexer resumes correctly after simulated restart.

**Exit criteria:**
- Indexer starts on Server startup.
- Events emitted on Hardhat appear in `blockchain_index` within the polling interval.
- Duplicate protection verified in tests.

**Definition of done:** Indexer operational and restartable. Duplicate protection tested.

---

## 20. Phase 8 — Project Management

**Objective:** Implement the full Government Admin project management workflow: project creation, budget allocation, and project status management.

**Why this phase exists:** Projects are the root entity. All milestones, funds, AI flags, and audit findings are scoped to projects. This is the first end-to-end integrated feature.

**Prerequisites:** Phase 4 (Auth), Phase 6 (Relayer), Phase 7 (Indexer).

**Blockchain events triggered:**
- `ProjectCreated` — emitted via Server Relayer when project is created.
- `BudgetAllocated` — emitted via Server Relayer when budget is allocated/updated.
- `ProjectCompleted` — emitted via Server Relayer when Gov Admin marks project complete.

**Server-Relayed flow for Project Creation:**
```
Gov Admin → POST /api/v1/projects
→ Server validates request
→ Server: INSERT project (status=Draft, on_chain_id=NULL)
→ Server Relayer: call createProject(projectId, totalBudget, name)
  emits: ProjectCreated(projectId, totalBudget, name, timestamp)
→ Await receipt
→ On SUCCESS: UPDATE project SET on_chain_id=projectId, status='Draft', tx_hash=...
  (Note: Project starts and remains in Draft status until Officer is assigned and budget is allocated)
→ On FAILURE: DELETE staged project row (or mark failed), return 500
→ Indexer (async): observes ProjectCreated event, writes blockchain_index row
```

**Project Activation Flow (Draft → Active):**
```
1. Gov Admin assigns Department Officer / Engineer:
   PATCH /api/v1/projects/{id}/assign-officer
   → Server updates project.assigned_officer_id in PostgreSQL
2. Gov Admin allocates initial budget:
   POST /api/v1/projects/{id}/budget
   → Server Relayer: call allocateBudget(projectId, amount)
     emits: BudgetAllocated(projectId, amount, authority, timestamp)
3. Project Activation:
   → Once both Officer is assigned and budget is allocated, Server transitions project status from Draft to Active.
   → Active projects can now have milestones defined by the assigned Department Officer / Engineer.
```

**Server-Relayed flow for Budget Allocation:**
```
Gov Admin → POST /api/v1/projects/{id}/budget
→ Server Relayer: call allocateBudget(projectId, amount)
  emits: BudgetAllocated(projectId, amount, authority, timestamp)
→ Indexer (async): observes BudgetAllocated event, writes blockchain_index row
```

**Server-Relayed flow for Project Completion:**
```
Gov Admin → POST /api/v1/projects/{id}/complete
→ Server validates approved completion conditions:
  1. Project is in Active status.
  2. No Active milestones remain (all milestones are Completed or Missed/Overdue).
     (Note: Missed/Overdue milestones do NOT by themselves block project completion).
  3. No unresolved fund release requests remain (all requests are Approved or Rejected).
→ Server Relayer: call completeProject(projectId)
  emits: ProjectCompleted(projectId, finalDisbursedAmount, timestamp)
→ On SUCCESS: UPDATE project SET status='Completed', tx_hash=...
→ Indexer (async): observes ProjectCompleted event, writes blockchain_index row
```

**API endpoints:**
- `POST /api/v1/projects` — Create project (Gov Admin only)
- `GET /api/v1/projects` — List projects (role-filtered)
- `GET /api/v1/projects/{id}` — Project detail
- `PATCH /api/v1/projects/{id}` — Update editable fields (Gov Admin)
- `POST /api/v1/projects/{id}/complete` — Mark project complete (Gov Admin)
- `GET /api/v1/projects/{id}/audit-trail` — Blockchain event timeline (reads `blockchain_index`)

**Client screens implemented:**
- PROJ-01: Project List (paginated, filterable data table)
- PROJ-02: Project Detail (tabbed — Overview, Milestones, Finances, Audit Trail)
- PROJ-03: Project Creation form (multi-step: Details → Budget)

**Database impact:** `projects` table written. `on_chain_id` populated after blockchain confirmation.
**API impact:** Project CRUD endpoints live.
**IPFS impact:** Not applicable.
**AI impact:** Not applicable.

**Financial utilization formula (REQUIRED):**
```
financial_utilization = SUM(approved fund releases for project) / project.budget × 100
```
This is a computed value derived from `fund_releases` records. Never a manually stored field.

**Testing requirements:**
- pytest: Project creation triggers relayer, commits on_chain_id, returns project detail.
- pytest: Relayer failure rolls back project row.
- pytest: Non-Gov-Admin receives 403.
- Client test: Project form validates required fields.

**Exit criteria:**
- Gov Admin can create a project end-to-end (DB + blockchain anchored).
- `ProjectCreated` event appears in `blockchain_index` after creation.
- Project List and Detail screens functional.

**Definition of done:** Complete project management workflow verified. Blockchain anchoring confirmed.

---

## 21. Phase 9 — Milestones and Physical Progress

**Objective:** Implement milestone lifecycle (definition, physical progress tracking, deadline handling, and explicit Officer completion) and physical progress recording as a distinct process from financial utilization.

**Why this phase exists:** Milestones and physical progress are the core of the fraud detection proposition — the mismatch between financial utilization and physical progress is the primary AI signal.

**Prerequisites:** Phase 8 complete.

**Key design rules:**
- **Milestone ownership:** Milestones are defined exclusively by the assigned Department Officer / Engineer for an Active project. Government Admin does NOT define milestones.
- **Physical progress:** Authoritative physical progress is recorded and updated exclusively by the Department Officer / Engineer. Contractors do NOT own the authoritative physical-progress record.
- **Progress separation:** Physical progress is a completely separate operational record from financial utilization.
- **Financial utilization formula:** Derived strictly from approved fund releases: `SUM(approved fund releases) / project.budget × 100` (never manually entered).
- **Project-level physical progress:** Derived and aggregated from the project's milestone physical progress records (e.g., budget-weighted sum or average of milestone physical progress records), reflecting overall project completion rather than single milestone progress.
- **Milestone completion:** Explicit Officer completion action by the assigned Department Officer / Engineer (operational state transition in PostgreSQL to `Completed`; no blockchain event).
- **Missed/Overdue transition:** Handled deterministically by the Deadline Scheduler (Phase 12), not by AI or manual input.

**Blockchain events triggered:**
- `MilestoneDefined` — emitted via Server Relayer on milestone creation.
*(Note: `OfficerVerified` belongs strictly to fund-release verification in Phase 10; milestone completion itself is an application-level operational state transition in PostgreSQL and does not emit a blockchain event).*

**Milestone Definition flow:**
```
Officer → POST /api/v1/projects/{id}/milestones
→ Server: validate Officer role, verify project is Active
→ Server: INSERT milestone in PostgreSQL (status=Active)
→ Server Relayer: call defineMilestone(projectId, milestoneId, budgetPortion, dueDateHash)
  emits: MilestoneDefined(projectId, milestoneId, budgetPortion, dueDateHash, timestamp)
→ Await receipt
→ On SUCCESS: UPDATE milestone SET on_chain_id=milestoneId, tx_hash=...
→ Indexer (async): observes MilestoneDefined event, writes blockchain_index row
→ Server: triggers mandatory AI analysis for the project after MilestoneDefined
```

**Milestone Completion flow:**
```
Officer → POST /api/v1/milestones/{id}/complete
→ Server: validate Officer role, verify milestone belongs to project assigned to Officer
→ Server: validate milestone is in Active or Missed/Overdue state
→ Server: UPDATE milestone SET status='Completed' in PostgreSQL
→ Server: return updated milestone details
*(Operational PostgreSQL transition only; no blockchain transaction or event)*
```

**API endpoints:**
- `POST /api/v1/projects/{id}/milestones` — Define milestone (Department Officer / Engineer only)
- `GET /api/v1/projects/{id}/milestones` — List milestones for project
- `GET /api/v1/milestones/{id}` — Milestone detail
- `POST /api/v1/milestones/{id}/progress` — Record physical progress update (Department Officer / Engineer only)
- `GET /api/v1/milestones/{id}/progress` — Physical progress history
- `POST /api/v1/milestones/{id}/complete` — Complete milestone (Department Officer / Engineer only; operational state transition)

**Client screens:**
- MILE-01: Milestone Management view (sub-view of PROJ-02 or standalone)

**Database impact:** `milestones` and `physical_progress` tables written.
**API impact:** Milestone definition, progress, and completion endpoints live.
**IPFS impact:** Not applicable (evidence upload is Phase 11).
**AI impact:** Not applicable yet, but `physical_progress.percent` is a critical AI feature.

**Testing requirements:**
- pytest: Milestone creation emits `MilestoneDefined` and stores `on_chain_id`.
- pytest: Officer-only milestone completion transitions milestone status to `Completed` in PostgreSQL.
- pytest: Non-Officer cannot define or complete milestones.
- pytest: Physical progress percent is recorded by Officer and accessible for AI feature computation.

**Exit criteria:**
- Milestones can be defined by the assigned Department Officer / Engineer.
- Physical progress can be recorded by the Officer independently from financial utilization.
- Milestones can be explicitly completed by the Officer.
- Missed/Overdue transitions are handled deterministically by the Deadline Scheduler.
- `OfficerVerified` belongs strictly to fund-release verification in Phase 10.

**Definition of done:** Full milestone lifecycle (definition, physical progress tracking, deadline handling, explicit Officer completion) verified.

---

## 22. Phase 10 — Fund Release Lifecycle

**Objective:** Implement the complete fund release lifecycle: Contractor browser-wallet request → Officer fund-release verification → Government Admin approval/rejection → PostgreSQL state machine with blockchain anchoring.

**Why this phase exists:** The fund release is the most critical business flow and the primary trigger for AI analysis.

**Prerequisites:** Phase 9 complete (milestones exist). Phase 6 complete (both blockchain paths). Phase 11 IPFS evidence-upload capability (evidence-dependent fund requests require document upload to exist and yield CIDs before the fund request flow can be completed).

**State machine (FROZEN):**
```
(new) → Pending (Contractor submits, FundReleaseRequested emitted)
→ Verified (Officer verifies fund release, OfficerVerified emitted)
→ Approved (Gov Admin approves, FundReleaseApproved emitted) [terminal]
OR
→ Rejected (Gov Admin rejects, FundReleaseRejected emitted) [terminal, re-submittable]
```

**Rules (FROZEN):**
- **Partial releases supported:** Multiple approved partial releases are supported against a single milestone until the cumulative approved amount reaches the applicable milestone budget portion.
- **Single active request:** Only one unresolved fund release request may be in the active processing path for a milestone at a time (`Pending` or `Verified`).
- **Rejected records retained:** Rejected requests are permanently retained in history and never deleted.
- **Resubmission:** A Contractor may create a new fund release request after a rejection.
- **AI triggers:** AI analysis is triggered asynchronously after `FundReleaseApproved` and after `OfficerVerified` (Phase 13).

**Contractor Browser-Wallet flow (FROZEN):**
```
1. Contractor opens Fund Release Request form on Client.
2. Contractor fills amount, selects milestone, uploads evidence (Phase 11 — CIDs in hand).
3. Client constructs requestFundRelease(projectId, milestoneId, requestedAmount, evidenceCids) call.
   emits: FundReleaseRequested(projectId, milestoneId, requestedAmount, evidenceCids, contractor, timestamp)
4. Client calls MetaMask → Contractor signs → Transaction broadcasts to Hardhat.
5. Client receives receipt (tx_hash, block_number) → submits { tx_hash, context_data } to Server.
6. Server verifies via Web3.py:
   - receipt.status == 1 (success)
   - method called = requestFundRelease with matching projectId/milestoneId
   - msg.sender matches Contractor's registered wallet address
7. On VALID: Server creates fund_releases DB row (status=Pending), stores tx_hash.
8. Server creates notification for Officer.
9. Indexer (async): observes FundReleaseRequested event, writes blockchain_index row.
```

**Officer Fund-Release Verification (Server-Relayed flow):**
```
1. Officer reviews fund release request details and attached evidence CIDs in Client.
2. Officer submits verification recommendation: POST /api/v1/funds/{id}/verify (Officer role required).
3. Server: validate fund release is in Pending status, verify Officer is assigned to project.
4. Server: stage DB — set status to intermediate.
5. Server Relayer: call recordOfficerVerification(projectId, milestoneId, recommendationOutcome, reportCid, officer)
   emits: OfficerVerified(projectId, milestoneId, recommendationOutcome, reportCid, officer, timestamp)
6. Await receipt.
7. On SUCCESS: finalize fund_releases status=Verified, store tx_hash.
8. On FAILURE: rollback to Pending status, return HTTP 500.
9. Server: create notification for Government Admin (category=FUND_VERIFIED).
10. Server: trigger AI BackgroundTask for the project after OfficerVerified.
11. Indexer (async): observes OfficerVerified event, writes blockchain_index row.
```

**Government Admin Approval (Server-Relayed flow):**
```
1. Gov Admin clicks Approve in Fund Release Approval dialog.
2. Client sends POST /api/v1/funds/{id}/approve.
3. Server: validate state is Verified, authorize Gov Admin role.
4. Server: stage DB — set status to intermediate.
5. Server Relayer: call approveFundRelease(projectId, milestoneId, approvedAmount)
   emits: FundReleaseApproved(projectId, milestoneId, approvedAmount, authority, timestamp)
6. Await receipt.
7. On SUCCESS: finalize status=Approved, store tx_hash, trigger AI BackgroundTask.
8. On FAILURE: rollback to Verified state, return 500.
9. Server: create notifications for Contractor and relevant parties.
10. Indexer (async): observes FundReleaseApproved event, writes blockchain_index row.
```

**Government Admin Rejection (Server-Relayed flow):**
```
1. Gov Admin clicks Reject.
2. Server Relayer: call rejectFundRelease(projectId, milestoneId, rejectionReasonHash)
   emits: FundReleaseRejected(projectId, milestoneId, rejectionReasonHash, authority, timestamp)
3. On SUCCESS: finalize status=Rejected, store tx_hash, store rejection_reason in PostgreSQL.
4. Indexer (async): observes FundReleaseRejected event, writes blockchain_index row.
```

**API endpoints:**
- `POST /api/v1/funds/request` — Contractor submits (with tx_hash verification)
- `GET /api/v1/funds` — List (role-filtered)
- `GET /api/v1/funds/{id}` — Fund release detail
- `POST /api/v1/funds/{id}/verify` — Officer verifies fund release (Server Relayed calls recordOfficerVerification)
- `POST /api/v1/funds/{id}/approve` — Gov Admin approves (Server Relayed)
- `POST /api/v1/funds/{id}/reject` — Gov Admin rejects (Server Relayed)

**Client screens:**
- FUND-01: Fund Release Request form (Contractor, includes wallet trigger and MetaMask modal)
- FUND-02: Fund Release Approval dialog/modal (Gov Admin)
- FUND-03: Fund Release History (tab on PROJ-02)

**Database impact:** `fund_releases` table written. State machine transitions enforced at DB level (status column).
**IPFS impact:** Evidence CIDs attached during request (Phase 11 implements upload; this phase stores the CID reference).
**AI impact:** `BackgroundTasks` trigger registered here; AI analysis deferred to Phase 13.
**Blockchain impact:** `FundReleaseRequested`, `OfficerVerified`, `FundReleaseApproved`, `FundReleaseRejected` events.

**Testing requirements:**
- pytest: Contractor submission with valid wallet receipt creates Pending record.
- pytest: Submission with invalid wallet receipt is rejected.
- pytest: Duplicate active request for same milestone is rejected.
- pytest: Officer fund-release verification calls recordOfficerVerification and emits OfficerVerified.
- pytest: Non-Officer cannot call fund-release verify endpoint.
- pytest: Approval flow completes DB state + blockchain confirmation.
- pytest: Approval relayer failure rolls back to Verified state.
- pytest: Rejection retains history; new request allowed.

**Phase Gate:**
> ⛔ **Do not proceed to Phase 13 (AI) until:** Full fund lifecycle tested including both blockchain paths, failure rollbacks, duplicate prevention, and re-submission after rejection.

**Exit criteria:**
- Full fund release lifecycle works end-to-end.
- All blockchain events confirmed in `blockchain_index`.
- Contractor wallet verification enforced.
- Gov Admin approval/rejection with rollback tested.

**Definition of done:** Fund lifecycle operational, tested, and blockchain-anchored.

---

## 23. Phase 11 — IPFS and Evidence Management

**Objective:** Implement document/evidence upload via Pinata, CID generation, PostgreSQL metadata storage, and evidence display in the Client.

**Why this phase exists:** Fund release requests require evidence. Audit reports reference IPFS CIDs. IPFS CIDs are anchored in blockchain events.

**Prerequisites:** Phase 3 (documents table), Phase 4 (auth — know who uploads).

**Document constraints (FROZEN — per TRD Section 15.2):**
- Allowed MIME types: `application/pdf`, `image/jpeg`, `image/png` only.
- Maximum file size: **15 MB** (15,728,640 bytes) per file. Files exceeding this limit are rejected with HTTP 413.
- These are not implementation choices — they are frozen upstream requirements.

**Server IPFS service design:**
```
Client → POST /api/v1/documents/upload (multipart/form-data)
→ Server: validate file (MIME type, size)
→ Server: upload to Pinata API → receive CID
→ Server: INSERT document record (cid, filename, mime_type, file_size, uploaded_by)
→ Server: return { document_id, cid, filename }
```

**Evidence association:** Documents are linked to `fund_releases` or `audit_findings` via foreign key. For `FundReleaseRequested`, the Contractor passes `evidenceCids` (string of CID references) directly in the transaction call. For `OfficerVerified` and `AuditFindingRecorded`, the `reportCid` is passed as a string parameter. No hashed CID variants (`ipfsCidHash`) are used.

**API endpoints:**
- `POST /api/v1/documents/upload` — Upload file, returns CID
- `GET /api/v1/documents/{id}` — Document metadata
- `GET /api/v1/projects/{id}/documents` — All documents for a project

**Client component:**
- `EvidenceCard` — Displays filename, CID (truncated with copy button), file type icon, and download link (via IPFS gateway).
- File upload zone in Fund Release Request form and Audit Finding form.

**Security note:** Documents are NOT encrypted in this academic version. The UI must display a small note: "Evidence is publicly accessible via IPFS."

**Database impact:** `documents` table written.
**API impact:** Document upload/retrieval endpoints live.
**Client impact:** `EvidenceCard` component; upload zone in forms.
**Blockchain impact:** CIDs are included as string parameters: `evidenceCids` in `FundReleaseRequested`, `reportCid` in `OfficerVerified` and `AuditFindingRecorded`. No hashed CID fields.
**AI impact:** Not applicable.

**Testing requirements:**
- pytest: Upload valid PDF/JPEG/PNG file → returns CID.
- pytest: Upload file exceeding 15 MB → returns HTTP 413.
- pytest: Upload disallowed MIME type (e.g., WebP, GIF, MP4) → returns 400.
- pytest: Mock Pinata failure → returns 500 with meaningful error.

**Exit criteria:**
- File upload works end-to-end with real Pinata API.
- CID stored in DB and displayed in Client.
- Invalid uploads rejected.

**Definition of done:** Evidence management operational. CIDs displayable in Client. IPFS gateway link functional.

---

## 24. Phase 12 — Notifications and Deadline Scheduler

**Objective:** Implement in-app notification creation/delivery and the APScheduler-based milestone deadline detection.

**Why this phase exists:** Notifications alert actors to required actions. Deadline detection is a distinct deterministic Server behavior (not AI).

**Prerequisites:** Phase 9 (milestones), Phase 3 (notifications table).

**Notification design (FROZEN — in-app only, no email/SMS):**

| Event | Notification Target | Category |
| :--- | :--- | :--- |
| Fund release submitted | Officer | `FUND_SUBMITTED` |
| Officer verified | Gov Admin | `FUND_VERIFIED` |
| Fund release approved | Contractor | `FUND_APPROVED` |
| Fund release rejected | Contractor | `FUND_REJECTED` |
| AI flag created | Auditor | `AI_FLAG` |
| Escalation created | Auditor | `ESCALATION` |
| Milestone overdue | Officer, Gov Admin | `MILESTONE_OVERDUE` |

**Notification service rules:**
- Notification creation must NOT break the primary lifecycle operation. Wrap in try/except; log failure.
- Notifications are read from PostgreSQL via Client polling (acceptable per approved design).
- `read` field updated when user marks notification read.

**API endpoints:**
- `GET /api/v1/notifications` — Current user's notifications (paginated)
- `PATCH /api/v1/notifications/{id}/read` — Mark as read
- `PATCH /api/v1/notifications/read-all` — Mark all as read

**Scheduler design (FROZEN baseline, implementation details are IMPLEMENTATION CHOICES):**
```
APScheduler AsyncIOScheduler registered in FastAPI lifespan (completely separate from the blockchain Indexer):
→ Job interval: DEADLINE_CHECK_INTERVAL_MINUTES (default 60 minutes, configured via env to 1 minute for viva demo)
→ On each tick:
   SELECT milestones WHERE status='Active' AND deadline < NOW()
   FOR EACH eligible milestone:
     UPDATE milestone SET status='Missed/Overdue' (idempotent — skip if already Overdue)
     INSERT notification for Officer and Gov Admin (if not already notified — check idempotency flag)
     Log action
→ Graceful shutdown: APScheduler.shutdown() in lifespan cleanup
```

**Idempotency for scheduler:** Duplicate overdue notifications must be prevented on repeated scheduler ticks. The persistence mechanism for notification idempotency must remain consistent with the approved TRD schema. A `notified_overdue_at` timestamp field on `milestones` is one viable approach but is an **IMPLEMENTATION DETAIL — requires schema verification before migration**. If introduced, it must be documented as an implementation-level addition, not a TRD schema element.

**Use of Overdue state as AI feature:** `milestones.status == 'Missed/Overdue'` is made available as an input feature to the AI module (Phase 13) for project delay risk detection. Overdue/Missed status is a deterministic Server decision, not an AI decision.

**Database impact:** `notifications` table written. `milestones.status` updated to `Missed/Overdue` (per TRD Section 9.2). Idempotency mechanism to be decided during implementation (see Deferred Decisions).
**API impact:** Notification endpoints live.
**Client impact:** `NOTIF-01` notification center dropdown/drawer. Unread indicator on bell icon.
**Blockchain impact:** Not applicable (deadline detection is purely deterministic Server logic).
**AI impact:** `Overdue` milestone status is a feature input for delay risk detection.

**Testing requirements:**
- pytest: Scheduler correctly identifies past-deadline milestones and transitions to Overdue.
- pytest: Duplicate Overdue notifications not created on repeated scheduler ticks.
- pytest: Notification creation failure does not propagate to the primary operation.
- pytest: Notification read/read-all endpoints update DB correctly.

**Exit criteria:**
- Scheduler starts on Server startup and runs on configured interval.
- Overdue milestones detected and notified correctly.
- In-app notifications appear in Client.

**Definition of done:** Scheduler operational. Notifications displayable. Overdue detection idempotent.

---

## 25. Phase 13 — AI/ML Risk and Anomaly Detection

**Objective:** Implement the hybrid AI/ML risk and anomaly detection subsystem, integrating it into the Server as a BackgroundTask triggered by approved lifecycle events.

**Why this phase exists:** AI-based risk flagging is a core technical differentiator of the project. It must be presented correctly as a human-in-the-loop system.

**Prerequisites:** Phase 10 (fund releases — primary AI trigger), Phase 9 (physical progress — primary AI feature), Phase 12 (Scheduler — delay risk feature). Phase 3 (`ai_flags` table).

> **IMPORTANT:** Exact AI hyperparameters, final feature set, calibration thresholds, and numerical risk score bands are **NOT FROZEN** by upstream documents. These are **IMPLEMENTATION CHOICES** to be determined and documented during this phase.

### 25.1 AI/ML Implementation Roadmap

**A. Data Preparation:**
- Generate synthetic tabular datasets in `ml/data/` representing plausible-but-fabricated government project records.
- Include scenarios: normal projects, mismatched utilization, delayed milestones, near-duplicate invoices, spending anomalies.
- Clearly document academic limitation: synthetic data does not prove real-world fraud detection performance.

**B. Feature Engineering (`ml/features/` and `backend/app/ai/features.py`):**

| Feature | Source | Notes |
| :--- | :--- | :--- |
| `financial_utilization_pct` | `SUM(approved fund releases) / budget * 100` | From PostgreSQL |
| `physical_progress_pct` | Project-level physical progress derived/aggregated from milestone progress records | From PostgreSQL |
| `util_vs_progress_gap` | `financial_utilization_pct - physical_progress_pct` | Computed |
| `budget_overrun_trajectory` | Projected final cost vs budget | Computed |
| `days_since_last_progress` | Days since last physical progress update | Computed |
| `milestone_overdue_count` | Count of Overdue milestones in project | From PostgreSQL |
| `days_overdue` | Max days past deadline among overdue milestones | Computed |
| `invoice_similarity_score` | Near-duplicate invoice detection (TF-IDF or hash comparison) | Computed |
| `spending_velocity` | Rate of fund release requests per time window | Computed |

**C. Rule-Based Signals:**
1. **Utilization vs Progress Mismatch (AI-CAP-01):** `util_vs_progress_gap > THRESHOLD_1` → Risk signal. Threshold is an IMPLEMENTATION CHOICE calibrated during Phase 13.
2. **Budget Overrun Trajectory (AI-CAP-02):** Projected to exceed budget → Risk signal.
3. **Project Delay Risk (AI-CAP-03):** `milestone_overdue_count > 0` OR `days_since_last_progress > THRESHOLD_2` → Delay risk signal. Overdue status is determined by the Scheduler, not AI.
4. **Duplicate Invoice (AI-CAP-04):** `invoice_similarity_score > THRESHOLD_3` → Duplication risk signal.
5. **Unusual Spending Pattern Detection (AI-CAP-05):** Spending velocity or disbursement claim size anomalous compared to project baseline → Spending pattern risk signal (driven by Isolation Forest anomaly score and velocity thresholds).

Each rule contributes a sub-score (0–100). Thresholds are **IMPLEMENTATION CHOICES** calibrated during this phase.

**D. Isolation Forest:**
- Train on synthetic dataset (`ml/train_isolation_forest.py`).
- Features: numeric features from the feature set above.
- Export trained model to `ml/models/isolation_forest.joblib` using `joblib`.
- Output: anomaly score per inference (scaled to 0–100).

**E. Model Fitting:**
- `n_estimators`, `contamination` parameter, and `max_samples` are **IMPLEMENTATION CHOICES**.
- Select values during calibration that produce a reasonable anomaly rate on the synthetic dataset.
- Document chosen values in `ml/` README.

**F. Risk Score Aggregation:**
```
final_risk_score = weighted_average(
    rule_score,         # from deterministic signals
    isolation_score,    # from Isolation Forest
)
```
Weights are **IMPLEMENTATION CHOICES** calibrated for balanced sensitivity.

**G. Contributing Factors:**
- Each rule that fires adds a human-readable factor string to a `factors` list.
- Example: `["High financial utilization vs. low physical progress", "Milestone overdue by 12 days"]`
- Stored as JSONB in `ai_flags.factors`.

**H. Threshold and Flagging:**
- If `final_risk_score >= AI_RISK_THRESHOLD` (from env) → create AI flag.
- AI_RISK_THRESHOLD is an **IMPLEMENTATION CHOICE** (calibrated during this phase; suggested starting point for calibration: 60 — not frozen).

**I. Inference Integration in Server:**
```python
# backend/app/ai/inference.py
async def run_ai_analysis(project_id: UUID, db: AsyncSession):
    try:
        features = await build_features(project_id, db)
        rule_score, rule_factors = evaluate_rules(features)
        isolation_score = isolation_forest.score(features)
        final_score = aggregate(rule_score, isolation_score)
        factors = rule_factors
        if final_score >= settings.AI_RISK_THRESHOLD:
            flag = await create_ai_flag(project_id, final_score, factors, db)
            # anomaly_type: primary anomaly capability code (e.g. "AI-CAP-01")
            await relayer.record_anomaly(project_id, flag.on_chain_id, final_score, anomaly_type)
            # calls: recordAIAnomaly(projectId, flagId, riskScore, anomalyType)
            # emits: AIAnomalyRecorded(projectId, flagId, riskScore, anomalyType, timestamp)
            await notification_service.create(auditors, "AI_FLAG", ...)
    except Exception as e:
        logger.error(f"AI analysis failed for project {project_id}: {e}")
        # Do NOT raise — AI failure must not propagate to the originating lifecycle operation
```

**J. BackgroundTasks Triggers (FROZEN):**
- After `FundReleaseApproved` → trigger AI analysis for the related project.
- After `OfficerVerified` (fund-release verification) → trigger AI analysis for the related project.
- After `MilestoneDefined` → trigger mandatory AI analysis for the related project.

**K. Blockchain Recording (FROZEN — per TRD Section 11.3 and 16.3):**
- If AI flag is created → Server Relayer calls `recordAIAnomaly(projectId, flagId, riskScore, anomalyType)`.
- Emits: `AIAnomalyRecorded(projectId, flagId, riskScore, anomalyType, timestamp)`.
- `anomalyType` is a string identifying the primary anomaly category (e.g. `"AI-CAP-01"`).
- **AI contributing factors are stored in PostgreSQL (`ai_flags.contributing_factors` JSONB) and shown in the Auditor UI.**
- The blockchain event does NOT store the full AI explanation payload — only the `anomalyType` identifier string is recorded on-chain per the approved TRD signature.
- The Indexer observes the `AIAnomalyRecorded` event and writes the blockchain_index row.

**L. Persistence:**
- AI flag row inserted into `ai_flags` (UUID, on_chain_id, project_id, risk_score, anomaly_types JSONB, contributing_factors JSONB, status=`Open` per TRD Section 9.2).

**M. Evaluation:**
- Use precision/recall on synthetic labeled dataset as academic validation metrics.
- Clearly state: "These metrics are indicative only, computed on synthetic data."

**N. Error Handling:**
- All AI errors are caught, logged, and swallowed. The originating lifecycle operation is never rolled back due to AI failure.

**O. Testing:**
- pytest: `build_features()` returns expected feature values for a known project.
- pytest: Rule evaluator returns correct signals for known scenarios.
- pytest: `run_ai_analysis()` creates an `ai_flags` row when threshold exceeded.
- pytest: `run_ai_analysis()` exception does not propagate.
- pytest: `AIAnomalyRecorded` emitted when flag created.

**P. Demo Data:**
- At least 3 projects in the seed dataset should have AI flags.
- At least 1 should have a clearly explained contributing factor for viva demonstration.

**Phase Gate:**
> ⛔ **Do not proceed to Phase 14 until:** AI analysis creates flags correctly, flags persist in DB, `AIAnomalyRecorded` anchored on blockchain, AI failure is swallowed without breaking fund lifecycle, test suite passes.

**Database impact:** `ai_flags` table populated.
**API impact:** `GET /api/v1/ai-risk/flags` and `GET /api/v1/ai-risk/flags/{id}` endpoints implemented.
**Client impact:** `RISK-01` AI Risk Queue, `RISK-02` Anomaly Investigation view with explainable factors.
**IPFS impact:** Not applicable.

**Exit criteria:**
- Approving a fund release asynchronously creates an AI flag (when threshold exceeded).
- Risk score and factors stored in DB.
- `AIAnomalyRecorded` event in `blockchain_index`.
- AI failure does not affect fund lifecycle.

**Definition of done:** Hybrid AI system operational. Risk flags correctly classified. Factors explainable in the UI.

---

## 26. Phase 14 — Auditor Investigation and Government Admin Escalation

**Objective:** Implement the Auditor investigation workflow (reviewing AI flags and escalations, recording findings via MetaMask) and the Government Admin formal escalation mechanism.

**Why this phase exists:** The human-in-the-loop component of the AI pipeline. No AI finding is complete without an Auditor's investigation and formal conclusion.

**Prerequisites:** Phase 13 (AI flags exist). Phase 6 (browser wallet — Auditor uses MetaMask). Phase 4 (auth — Auditor role).

**Government Admin Escalation flow:**
```
Gov Admin → POST /api/v1/escalations (with non-empty reason)
→ Server: validate Gov Admin role, validate reason non-empty
→ Server: INSERT escalation record (FK → project, FK → gov_admin, status=Open, reason)
→ Server: create notification for Auditor (category=ESCALATION)
→ Server: return escalation detail
```

**Auditor Investigation flow:**
```
Auditor → views DASH-AUD (queue of AI flags + escalations)
→ Auditor → selects a case → views RISK-02 (Investigation detail)
   Shows: AI factors, fund release history, physical progress, IPFS evidence links, blockchain audit trail
→ Auditor → fills Audit Finding form (narrative, outcome, uploads report to IPFS if applicable)
→ Client: constructs recordAuditFinding(projectId, findingId, flagId, findingOutcome, reportCid) call
  emits: AuditFindingRecorded(projectId, findingId, flagId, auditorWallet, findingOutcome, reportCid, timestamp)
→ MetaMask → Auditor signs → broadcasts to Hardhat
→ Client: receives receipt (tx_hash) → sends { tx_hash, context_data } to Server
→ Server: verify via Web3.py:
  - receipt.status == 1 (already mined — exactly ONE transaction)
  - method = recordAuditFinding with matching findingId, flagId
  - tx.from matches Auditor's registered wallet address
→ On VALID: Server INSERT audit_findings row (stores tx_hash, report_ipfs_cid)
→ Server: UPDATE ai_flag status to `Under Review` or `Reviewed — No Action` (per FRD outcome)
→ Server: create notification for Gov Admin
→ Indexer (async): observes AuditFindingRecorded event, writes blockchain_index row

> **IMPORTANT:** `AuditFindingRecorded` is emitted exactly ONCE by the Auditor's browser-wallet transaction.
> There is NO second Server Relayer transaction for audit findings.
> The Server verifies the already-mined transaction; it does not submit an additional blockchain confirmation.
```

**AuditFindingRecorded on-chain parameters (FROZEN):**
- `findingId` = Audit finding's on-chain ID (monotonically assigned).
- `flagId` = AI flag's on_chain_id if the finding originates from an AI flag.
- `flagId = 0` if the finding originates from a Gov Admin escalation without an AI flag.

**API endpoints:**
- `POST /api/v1/escalations` — Gov Admin creates escalation (with reason)
- `GET /api/v1/escalations` — Auditor lists escalations assigned
- `GET /api/v1/ai-risk/flags` — Auditor lists AI flags
- `GET /api/v1/ai-risk/flags/{id}` — Flag detail with investigation context
- `POST /api/v1/audit/findings` — Auditor submits finding (with tx_hash verification)
- `GET /api/v1/audit/findings/{id}` — Finding detail

**Client screens:**
- DASH-AUD: Auditor dashboard with queue of flags + escalations.
- RISK-01: AI Anomaly Investigation view.
- RISK-02: Escalation view.
- AUDIT-01: Finding submission form (includes MetaMask trigger).

**Client UI rules (from UI/UX Spec):**
- AI flags displayed with Violet AI identity indicator + severity indicator (separate).
- Never display fraud or guilt conclusions (e.g. "Fraud Confirmed", "Guilty", "Corrupt"). Use "Risk Flag", "Anomaly Detected", "Investigation Required".
- Investigation view shows: risk score ring, contributing factors list, timeline of events, evidence links.

**Database impact:** `audit_findings` and `escalations` tables written. `ai_flags.status` updated.
**Blockchain impact:** `AuditFindingRecorded` event emitted.
**IPFS impact:** Audit report (if any) uploaded to IPFS; CID stored in `audit_findings.report_cid`.

**Testing requirements:**
- pytest: Escalation creation requires non-empty reason; empty reason returns 400.
- pytest: Audit finding submission with valid Auditor wallet receipt creates finding and updates flag status.
- pytest: `flagId = 0` when finding originates from escalation without AI flag.
- pytest: Non-Auditor cannot submit a finding.
- pytest: `AuditFindingRecorded` appears in `blockchain_index`.

**Exit criteria:**
- Gov Admin can escalate a project to Auditor.
- Auditor can view queue, investigate, and submit finding via MetaMask.
- Finding anchored on blockchain via exactly one Auditor browser-wallet transaction.
- AI flag status updated to `Under Review` or `Reviewed — No Action` (per FRD outcome).

**Definition of done:** Complete human-in-the-loop flow operational and blockchain-anchored.

---

## 27. Phase 15 — Citizen / Public Portal

**Objective:** Implement the unauthenticated, read-only Citizen portal exposing approved public information about projects and audit trails.

**Why this phase exists:** Public transparency is a core stated purpose of the platform. Citizen access validates the end-to-end data pipeline.

**Prerequisites:** Phase 8 (projects), Phase 10 (fund releases), Phase 14 (audit findings).

**Public API design:** Public endpoints do NOT require JWT. They must be implemented as a distinct router with no auth dependency.

**Data exposure rules (FROZEN):**

| Exposed to Citizen | NOT Exposed to Citizen |
| :--- | :--- |
| Project name, description, status | User account details |
| Allocated budget | Internal AI contributing factors |
| Approved financial utilization (aggregate) | Restricted IPFS evidence links |
| Physical progress (aggregate percent) | Internal workflow data |
| Public milestone names and statuses | Contractor wallet addresses |
| Public fund event timeline | Gov Admin escalation reasons |
| Published audit finding outcomes | Raw AI risk scores |

**API endpoints:**
- `GET /api/v1/public/projects` — List public projects (paginated, searchable)
- `GET /api/v1/public/projects/{id}` — Public project detail
- `GET /api/v1/public/projects/{id}/timeline` — Public fund event timeline (from `blockchain_index`)
- `GET /api/v1/public/projects/{id}/audit-summary` — Published audit finding outcomes only

**Client screens:**
- PUB-01: Citizen Landing Page (search bar, global stats)
- PUB-02: Citizen Project Detail (budget bar, physical progress, milestone list)
- PUB-03: Citizen Public Audit Timeline (vertical timeline of anchored events)

**Client layout:** No sidebar. Top navigation only. No authentication links. Fully accessible.

**Database impact:** Read-only queries on existing tables. Add `is_public` flag to `audit_findings` if only published findings should be visible (IMPLEMENTATION CHOICE).
**API impact:** Separate public router, no auth middleware.
**Blockchain impact:** Timeline reads from `blockchain_index`.
**IPFS impact:** Not applicable (no new uploads; only publicly safe CIDs shown if applicable).
**AI impact:** Not applicable.

**Testing requirements:**
- pytest: Public endpoints return 200 without JWT.
- pytest: Public project detail does NOT include user emails, wallet addresses, or AI factors.
- pytest: Audit timeline returns only approved events.

**Exit criteria:**
- Unauthenticated Citizen can browse projects, view budget/progress, and see fund event timeline.
- Restricted data confirmed absent from public responses.

**Definition of done:** Citizen portal functional. Data boundary verified by tests.

---

## 28. Phase 16 — Dashboards, Visualization, and UI Polish

**Objective:** Implement role-specific dashboards, data visualizations, dark mode refinement, responsive adjustments, and overall UI polish per the UI/UX Specification.

**Why this phase exists:** This is what makes the project impressive during viva. Core workflows must exist (Phases 8–15) before polish is applied.

**Prerequisites:** Phases 8–15 complete (core data exists). Phase 1 (Client Foundation).

**Implementation areas (in priority order):**

| Priority | Area | Screen IDs |
| :--- | :--- | :--- |
| Demo-Critical | Government Admin Dashboard | DASH-GA |
| Demo-Critical | Project Detail + Audit Timeline | PROJ-02, AUDIT-01 |
| Demo-Critical | AI Risk Investigation | RISK-01, RISK-02 |
| Demo-Critical | Fund Lifecycle Approval Dialog | FUND-02 |
| Demo-Critical | Citizen Project View | PUB-01, PUB-02, PUB-03 |
| Demo-Critical | Login | AUTH-01 |
| High | Contractor Dashboard + Wallet UI | DASH-CON, FUND-01 |
| High | Auditor Dashboard | DASH-AUD |
| High | Officer Dashboard | DASH-OFF |
| Normal | Platform Admin Dashboard | ADMIN-01, ADMIN-02 |

**Data visualizations (IMPLEMENTATION CHOICES for library):**
- **Financial vs Physical Progress:** Dual horizontal bar chart (side-by-side). Blue = Financial %, Green = Physical %.
- **Budget Utilization:** Donut chart or stacked progress bar.
- **Risk Score:** Circular progress ring (0–100). Color semantics: neutral for Low, warning for Medium, danger for High (thresholds are IMPLEMENTATION CHOICES pending AI calibration).
- **Fund Event Timeline:** Vertical stepper with icons.

**UI Polish checklist:**
- [ ] Skeleton loaders for all data tables and dashboard cards.
- [ ] Empty states with friendly messages for empty queues/lists.
- [ ] Toast notifications for success/error/blockchain confirmation.
- [ ] MetaMask "Waiting for confirmation" non-dismissible modal.
- [ ] Confirmation dialogs for destructive actions (approve, reject, finding).
- [ ] All forms validate inline with red error text.
- [ ] Dark mode verified on all screens.
- [ ] Responsive layout verified at ≥768px (tablet) and ≥1024px (desktop).
- [ ] AI outputs labeled with Violet AI indicator (AI identity) and separate severity indicator.
- [ ] Blockchain hashes displayed in monospace with copy button.
- [ ] IPFS CIDs displayed truncated with copy button and gateway link.

**Database impact:** Not applicable.
**API impact:** Dashboard-specific aggregate endpoints may be needed (e.g., `GET /api/v1/gov/dashboard` returning budget summary).

**Testing requirements:**
- Client tests: Skeleton loader shown during data fetch.
- Client tests: Empty state shown when list is empty.
- Client tests: Dark mode CSS variables applied correctly.
- Manual viva review: All demo-critical screens reviewed against UI/UX Spec.

**Exit criteria:**
- All 6 role dashboards functional and visually polished.
- Dark mode working on all screens.
- Data visualizations rendering with real data.
- Demo-critical screens pass internal visual review.

**Definition of done:** Application is visually impressive, role-specific, and ready for demonstration.

---

## 29. Phase 17 — Testing and Integration

**Objective:** Formalize the test suite, fill gaps in critical path coverage, and run integration tests across the full lifecycle.

**Why this phase exists:** Ensures implementation correctness and regression safety before the final demo.

**Prerequisites:** Phases 0–16 substantially complete.

### Testing Layers

**A. Contract Tests (Hardhat/Mocha/Chai):**
- Event emission for all 10 approved events with correct approved parameter signatures.
- `onlyOwner` access control: non-owner calls to `createProject`, `allocateBudget`, `defineMilestone`, `recordOfficerVerification`, `approveFundRelease`, `rejectFundRelease`, `recordAIAnomaly`, `completeProject` revert.
- `requestFundRelease` callable by Contractor (Account #1) directly.
- `recordAuditFinding` callable by Auditor (Account #2) directly.
- State validation (nonexistent project, duplicate ID checks).

**B. Server Unit + Integration Tests (pytest + httpx.AsyncClient):**
- Authentication: valid login, invalid credentials, deactivated user, role mismatch.
- RBAC: every role-restricted endpoint tested with wrong role.
- State machines: fund release transitions (invalid transitions rejected).
- Relayer: success path and failure/rollback path.
- Wallet verifier: valid receipt accepted, mismatched wallet rejected, wrong method rejected, wrong parameters rejected.
- Indexer: events indexed idempotently, duplicates skipped, restart resumes from watermark.
- Indexer: lifecycle services do NOT independently write blockchain_index rows (Indexer is sole writer).
- IPFS: upload valid PDF/JPEG/PNG succeeds, file >15 MB rejected (HTTP 413), invalid MIME rejected.
- Notifications: created on events, failure swallowed without breaking lifecycle.
- Scheduler: Missed/Overdue detection idempotent on repeated ticks, does not affect AI status.
- AI: feature building, rule signals, threshold flagging, failure isolation.
- AI `AIAnomalyRecorded`: uses `anomalyType` string parameter per approved TRD event signature.
- Escalation: non-empty reason enforced.
- Audit finding: `flagId = 0` for escalation-originated findings.
- Audit finding: exactly one browser-wallet transaction, Server only verifies (no second Relayer tx).

**C. AI Tests:**
- `build_features()` returns correct values for known dataset.
- Rule signals produce correct binary outputs.
- Isolation Forest scores within expected range.
- Risk score aggregation is deterministic for same inputs.
- Exception in inference does not propagate.

**D. Client Tests (Vitest + RTL):**
- Auth flow: login form submits, JWT stored, redirect occurs.
- Protected routes: unauthenticated redirect.
- Role guard: wrong-role redirect.
- Fund Release Request form: validation errors shown, MetaMask modal shown.
- Error state: API error displays error message.
- Empty state: empty list shows empty state component.
- Dark mode: theme toggle persists.

**E. Integration Tests:**
- Full fund lifecycle: Contractor request → Officer verification → Gov Admin approval → AI flag → Auditor finding.
- Blockchain confirmation: events appear in `blockchain_index` after lifecycle steps.
- IPFS integration: CID stored and retrievable.
- Citizen portal: unauthenticated access to correct data, restricted data absent.

**F. End-to-End:**
- Complete demo scenario executed programmatically (Phase 18 seed data + manual walkthrough).

**Testing philosophy:** Prioritize critical business workflows and failure paths. Do not aim for artificial 100% coverage.

**Exit criteria:**
- Contract tests: 100% pass.
- Server tests: Critical path tests pass. No uncaught exceptions in happy path.
- AI tests: Feature building and inference deterministic.
- Client tests: Auth flows and state transitions pass.
- Integration: Full fund lifecycle integration test passes.

---

## 30. Phase 18 — Seed Data and End-to-End Demo

**Objective:** Create a comprehensive seed dataset and validate the complete demonstration scenario.

**Why this phase exists:** An empty database cannot demonstrate anything. Seed data makes dashboards meaningful and the viva impressive.

**Prerequisites:** Phases 1–17 complete.

**Seed dataset scenarios:**

| Scenario | Purpose |
| :--- | :--- |
| 1. Normal healthy project | Baseline — shows successful lifecycle |
| 2. High financial utilization, low physical progress | Primary AI anomaly demo case |
| 3. Delayed/missed milestone | Scheduler and delay risk demo |
| 4. Rejected fund release + resubmission | Demonstrates rejection history retention |
| 5. AI anomaly flagged, Auditor investigating | Shows open investigation queue |
| 6. Gov Admin escalation (no AI flag) | Demonstrates escalation with `flagId=0` |
| 7. Completed Auditor finding | Shows resolved investigation |
| 8. Completed project | Shows full lifecycle terminal state |
| 9. Citizen-visible project | Validates public portal |
| 10. Multiple projects (data volume) | Makes dashboards statistically meaningful |

**Implementation:**
- `data/seed_demo_scenario.py` — Python script that uses the Server's services layer (not direct SQL) to create seed data in the correct application state.
- Alternatively, SQL seed files for rapid reset.
- **Include at least 10 projects, 30 milestones, 50 fund releases** to make charts meaningful.
- At least 3 AI flags with different risk scores and contributing factors.

**End-to-end demo validation:**
1. Run seed script → confirm all data present.
2. Login as Gov Admin → verify dashboard shows portfolio stats.
3. Open anomalous project → verify AI risk badge visible.
4. Log in as Contractor → connect MetaMask → submit fund request → sign.
5. Log in as Officer → verify fund release request.
6. Log in as Gov Admin → approve fund release.
7. Verify AI flag created asynchronously.
8. Log in as Auditor → open flag → review factors → submit finding via MetaMask.
9. Log in as Gov Admin → escalate a project.
10. Open Citizen portal → verify public project visible with timeline.

**Exit criteria:**
- Seed script runs successfully on a fresh database.
- Complete 23-step demo scenario executed successfully without errors.

**Definition of done:** Complete demo scenario reproducible in under 15 minutes from a fresh database.

---

## 31. Phase 19 — Final Hardening and Viva Preparation

**Objective:** Final cleanup, documentation alignment, and demonstration readiness.

**Tasks:**
1. Remove all `print()` debug statements and excessive debug logging.
2. Verify all `.env.example` files are accurate and complete.
3. Verify `README.md` in each directory has accurate setup and run instructions.
4. Run full test suite; fix any failures.
5. Cross-check `docs/06-IMPLEMENTATION-PLAN.md` against actual implementation — update any discrepancies.
6. Verify documentation chain (docs/00–06) is internally consistent with what was built.
7. Prepare a 10–15 minute demo walkthrough script for viva.
8. Verify dark mode on all demo-critical screens.
9. Verify MetaMask flows work on the demo machine.
10. Verify Pinata API key is valid and quota is sufficient.
11. Test seed script on a fresh database.

---

## 32. API Implementation Roadmap

APIs are developed in dependency order across phases. Full endpoint list:

**Authentication:**
- `POST /api/v1/auth/login`
- `GET /api/v1/auth/me`

**Platform Admin:**
- `GET /api/v1/admin/users`
- `POST /api/v1/admin/users`
- `PATCH /api/v1/admin/users/{id}`
- `PATCH /api/v1/admin/users/{id}/status`

**Projects:**
- `POST /api/v1/projects`
- `GET /api/v1/projects`
- `GET /api/v1/projects/{id}`
- `PATCH /api/v1/projects/{id}`
- `POST /api/v1/projects/{id}/complete`
- `GET /api/v1/projects/{id}/audit-trail`

**Milestones:**
- `POST /api/v1/projects/{id}/milestones`
- `GET /api/v1/projects/{id}/milestones`
- `GET /api/v1/milestones/{id}`
- `POST /api/v1/milestones/{id}/progress`
- `GET /api/v1/milestones/{id}/progress`
- `POST /api/v1/milestones/{id}/complete`

**Fund Releases:**
- `POST /api/v1/funds/request`
- `GET /api/v1/funds`
- `GET /api/v1/funds/{id}`
- `POST /api/v1/funds/{id}/verify`
- `POST /api/v1/funds/{id}/approve`
- `POST /api/v1/funds/{id}/reject`

**Documents:**
- `POST /api/v1/documents/upload`
- `GET /api/v1/documents/{id}`
- `GET /api/v1/projects/{id}/documents`

**AI / Risk:**
- `GET /api/v1/ai-risk/flags`
- `GET /api/v1/ai-risk/flags/{id}`

**Audit:**
- `POST /api/v1/escalations`
- `GET /api/v1/escalations`
- `GET /api/v1/escalations/{id}`
- `POST /api/v1/audit/findings`
- `GET /api/v1/audit/findings/{id}`

**Notifications:**
- `GET /api/v1/notifications`
- `PATCH /api/v1/notifications/{id}/read`
- `PATCH /api/v1/notifications/read-all`

**Dashboards:**
- `GET /api/v1/gov/dashboard`
- `GET /api/v1/auditor/dashboard`

**Public (no auth):**
- `GET /api/v1/public/projects`
- `GET /api/v1/public/projects/{id}`
- `GET /api/v1/public/projects/{id}/timeline`
- `GET /api/v1/public/projects/{id}/audit-summary`

**API layering pattern:**
```
Route (FastAPI router)
→ Pydantic schema validation (request)
→ Auth/RBAC dependency
→ Service layer (business logic)
→ DB layer (SQLAlchemy async)
→ Blockchain/IPFS/AI integration (as needed)
→ Pydantic schema serialization (response)
```

**All errors:** RFC 7807 `application/problem+json` format.

---

## 33. Database Implementation Roadmap

Schema creation order (must match migration dependency order):

```text
001_users → 002_projects → 003_milestones → 004_physical_progress
→ 005_fund_releases → 006_documents → 007_ai_flags
→ 008_escalations → 009_audit_findings
→ 010_blockchain_index → 011_notifications
```

Each migration is reversible (`downgrade`). Test both directions before proceeding.

---

## 34. Smart Contract Implementation Roadmap

```text
1. Define all events (10 approved events — FROZEN)
2. Define state variables (ID counters and existence mappings)
3. Implement Ownable inheritance and constructor
4. Implement ReentrancyGuard
5. Implement server-relayed functions (onlyOwner)
6. Implement browser-wallet functions (Contractor, Auditor)
7. Write Mocha/Chai tests (all events, access control, state)
8. Write deploy.js
9. Test and confirm on local Hardhat
```

---

## 35. AI/ML Implementation Roadmap

```text
A. Generate synthetic dataset (ml/data/)
B. Implement feature engineering (ml/features/ + backend/app/ai/features.py)
C. Implement rule-based signals (backend/app/ai/rules.py)
D. Train Isolation Forest (ml/train_isolation_forest.py)
E. Export model artifact (ml/models/isolation_forest.joblib)
F. Implement inference entry point (backend/app/ai/inference.py)
G. Implement risk score aggregation
H. Implement contributing factor generation
I. Calibrate threshold (AI_RISK_THRESHOLD — IMPLEMENTATION CHOICE)
J. Evaluate on synthetic test split
K. Integrate with FastAPI BackgroundTasks
L. Implement AIAnomalyRecorded relayer call
M. Implement AI flag persistence
N. Implement exception isolation (no propagation)
O. Write AI tests
P. Verify with seed demo data
```

---

## 36. Client Implementation Roadmap

Implemented in this sequence across phases:

```text
A. Design system + theme (Phase 1)
B. Routing + auth shell (Phase 1)
C. Login page, JWT storage (Phase 4)
D. Role-based navigation (Phase 1+4)
E. Wallet connection context (Phase 6)
F. Project List + Detail (Phase 8)
G. Milestone Management (Phase 9)
H. Fund Release Request + Approval (Phase 10)
I. IPFS Evidence display (Phase 11)
J. Notifications (Phase 12)
K. AI Risk Queue + Investigation (Phase 13)
L. Auditor Finding form (Phase 14)
M. Citizen Portal (Phase 15)
N. Dashboards + Charts (Phase 16)
O. Dark mode + Responsive polish (Phase 16)
P. Empty/Loading/Error states (Phase 16)
```

---

## 37. Server Implementation Roadmap

```text
A. FastAPI app factory + CORS + exception handlers (Phase 2)
B. Config + logging (Phase 2)
C. SQLAlchemy async engine + session (Phase 2)
D. Alembic migrations (Phase 3)
E. JWT + RBAC (Phase 4)
F. User management service (Phase 4)
G. Web3 contract loader + Relayer (Phase 6)
H. Wallet verifier (Phase 6)
I. Indexer background task (Phase 7)
J. Project service + router (Phase 8)
K. Milestone + Progress service + router (Phase 9)
L. Fund Release service + router (Phase 10)
M. IPFS / Pinata service (Phase 11)
N. Notification service (Phase 12)
O. Scheduler (APScheduler) (Phase 12)
P. AI inference task (Phase 13)
Q. Escalation + Audit Finding service + router (Phase 14)
R. Public API router (Phase 15)
S. Dashboard aggregate endpoints (Phase 16)
```

---

## 38. Testing Strategy and Gates

**Phase Gates (must pass before proceeding):**

| Gate | Condition |
| :--- | :--- |
| **Phase 5 → Phase 6** | `npx hardhat test` passes 100%. Contract deploys. |
| **Phase 6 → Phase 8** | Relayer success + failure tested. Wallet verifier tested. |
| **Phase 8 → Phase 9** | Project lifecycle with blockchain anchoring tested. |
| **Phase 10 → Phase 13** | Full fund lifecycle tested including both blockchain paths and rollbacks. |
| **Phase 13 → Phase 14** | AI creates flags. Failure isolated. `AIAnomalyRecorded` confirmed. |
| **Phase 15 → Phase 16** | All 6 actor workflows functional. Citizen portal verified. |
| **Phase 16 → Phase 17** | Demo-critical screens reviewed and passing visual standard. |
| **Phase 18 → Phase 19** | Seed script runs. Demo scenario completable in full. |

---

## 39. Local Development and Run Order

The following startup sequence must be followed (exact commands are IMPLEMENTATION-TIME decisions — verify against actual manifests):

```
Step 1: Start PostgreSQL
  → (docker-compose up -d db) OR (pg_ctl start)

Step 2: Run migrations
  → cd backend && alembic upgrade head

Step 3: Start Hardhat node
  → cd contracts && npx hardhat node

Step 4: Deploy Smart Contract
  → cd contracts && npx hardhat run scripts/deploy.js --network localhost
  → Copy CONTRACT_ADDRESS to backend/.env and frontend/.env

Step 5: Start Server
  → cd backend && python -m uvicorn app.main:app --reload

Step 6: Start Client
  → cd frontend && npm run dev

Step 7: Configure MetaMask
  → Add Localhost:8545 network, Chain ID 31337
  → Import Account #1 (Contractor) and Account #2 (Auditor) from Hardhat mnemonics

Step 8: Configure Pinata
  → Ensure PINATA_JWT, PINATA_GATEWAY_URL, and IPFS_GATEWAY_URL are set in backend/.env

Step 9: Run seed data
  → cd data && python seed_demo_scenario.py

Step 10: Verify
  → Login, navigate dashboard, verify MetaMask connects
```

---

## 40. Demo Scenario

The authoritative 23-step viva demonstration sequence:

| Step | Actor | Action | Technology Demonstrated |
| :--- | :--- | :--- | :--- |
| 1 | Gov Admin | Login to Platform | JWT auth (HS256, live DB check), RBAC |
| 2 | Gov Admin | Create Project | Server Relayer (Account #0) → `ProjectCreated` event anchored |
| 3 | Gov Admin | Verify Initial Status | PostgreSQL state: Project starts in `Draft` status |
| 4 | Gov Admin | Assign Responsible Officer | PostgreSQL update: Department Officer / Engineer assigned |
| 5 | Gov Admin | Allocate Project Budget | Server Relayer → `BudgetAllocated` event anchored |
| 6 | (System) | Project Activation | Project transitions from `Draft` to `Active` (Officer assigned + budget allocated) |
| 7 | Officer | Login & Define Milestone | JWT auth; Officer-only milestone creation for Active project |
| 8 | (System) | Anchor MilestoneDefined | Server Relayer → `MilestoneDefined` event anchored |
| 9 | (System) | Trigger Mandatory AI Analysis | Event trigger after `MilestoneDefined` runs in-process |
| 10 | Contractor | Login & Upload Invoice Evidence | Pinata IPFS upload (PDF/PNG, ≤15 MB) → IPFS CID generated |
| 11 | Contractor | Submit Fund Release Request | MetaMask (Account #1) signs transaction → `FundReleaseRequested` |
| 12 | (System) | Server Verifies Wallet Transaction | Web3 receipt verification: `receipt.status == 1`, method, parameters, wallet |
| 13 | Officer | Verify Fund Release Request | Server Relayer → `OfficerVerified` event anchored |
| 14 | Gov Admin | Approve Fund Release | Server Relayer → `FundReleaseApproved` event anchored |
| 15 | (System) | AI Risk Analysis on Fund Release | BackgroundTasks, hybrid rule engine + Isolation Forest risk scoring (0–100) |
| 16 | Officer | Record Physical Progress Update | Physical progress recorded independently from financial utilization |
| 17 | (System) | Deadline Scheduler Inspection | APScheduler inspects milestone deadlines → transitions Missed/Overdue |
| 18 | Auditor | Login & Review AI Risk Queue | AI flags with explainable contributing factors displayed in Auditor UI |
| 19 | Gov Admin | Escalate Suspicious Case | Formal administrative escalation flow to Auditor (`flagId = 0` if no prior flag) |
| 20 | Auditor | Submit Formal Audit Finding | MetaMask (Account #2) signs transaction → `AuditFindingRecorded` |
| 21 | (System) | Anchor AuditFindingRecorded | Exactly one browser-wallet transaction; Server verifies receipt and updates DB |
| 22 | Citizen | View Public Portal | Unauthenticated read-only access: project details, budget, audit trail |
| 23 | Gov Admin | Mark Project Complete | Server Relayer → `ProjectCompleted` (verified: Active, no Active milestones, no unresolved requests) |

---

## 41. Security and Observability Checklist

**Security:**
- [ ] Passwords hashed with `bcrypt`
- [ ] JWT secret is 256-bit, loaded from environment (never hardcoded)
- [ ] Every protected endpoint calls `get_current_user()` which checks live DB status
- [ ] Role permissions enforced via `require_role()` dependency
- [ ] Browser-wallet receipts verified by Server (wallet address, method, parameters)
- [ ] Server Relayer private key loaded from environment (never committed)
- [ ] Pinata credentials loaded from environment (never committed)
- [ ] File upload: MIME type validated (PDF, JPEG, PNG only) and size validated (≤15 MB) before Pinata call
- [ ] Public citizen endpoints return only approved public fields
- [ ] No private key or secret logged anywhere

**Logging (structured, using Python `logging`):**
- [ ] Authentication failures (login invalid credentials, JWT invalid)
- [ ] Authorization failures (role mismatch, inactive user)
- [ ] Blockchain transaction attempts (relayer calls with on-chain function name)
- [ ] Blockchain transaction hashes on success
- [ ] Blockchain submission failures with error details
- [ ] Indexer: blocks processed, events decoded, duplicate skipped
- [ ] IPFS upload success (CID) and failures
- [ ] AI analysis: start, completion, flag created, exception caught
- [ ] Scheduler: tick start, milestones transitioned, notifications created
- [ ] Notification creation failures
- [ ] Major lifecycle transitions (project created, fund approved, finding recorded)
- [ ] NOT logged: passwords, JWT secrets, private keys, Pinata credentials

---

## 42. Implementation Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Fallback |
| :--- | :--- | :--- | :--- | :--- |
| **Blockchain Tx Failure (relayer)** | High | Medium | Staged DB transitions; explicit rollback on failure; retry guidance | Return HTTP 500; user retries action |
| **MetaMask Rejection by User** | Medium | High | Client shows clear "Waiting for MetaMask" modal; handles rejection gracefully | Show error toast; user can retry |
| **Wallet/Account Mismatch** | High | Low | Server verifies `msg.sender` from receipt against registered wallet | Return HTTP 400; Auditor uses correct account |
| **Hardhat Restart (chain reset)** | High | Medium | Document that Hardhat restart resets chain; contract must be redeployed; seed data must be re-run | Startup documentation; explicit warning in README |
| **Indexer Duplication** | High | Low | `(tx_hash, log_index)` unique constraint; idempotent processing | Constraint rejects duplicate insert; indexer logs and continues |
| **Indexer Restart Gap** | Medium | Low | `last_processed_block` persisted in DB; resumes from correct block | Safe restart design |
| **IPFS Upload Failure (Pinata)** | Medium | Low | Server catches exception, returns HTTP 500 with meaningful message | User retries upload; Pinata quota monitored |
| **Pinata Credential Issues** | Medium | Low | Validate Pinata credentials at Server startup | Log error; document re-configuration |
| **AI False Positives** | Low | High (synthetic) | Academic framing: "Risk Flag requiring investigation," never "fraud" | Threshold tuned during calibration; Auditor is the final decision-maker |
| **Synthetic Data Limitations** | Low | Certain | Clearly documented academic limitation in code and report | Accepted; does not block demonstration |
| **Scheduler Duplication** | Medium | Low | `notified_overdue_at` idempotency column | Prevents duplicate notifications |
| **DB Migration Errors** | High | Low | Always test `upgrade` + `downgrade` before merging; test DB fixture | Manual rollback to previous revision |
| **JWT Role-Change Edge Case** | Medium | Low | Live DB check on every request; no blacklist needed | Deactivation effective immediately on next request |
| **UI/API Contract Mismatch** | Medium | Medium | Pydantic schemas on Server; TypeScript types on Client; keep in sync | Schema-first API design; update Client on Server change |
| **Demo Data Inconsistencies** | Medium | Medium | Validate seed script on fresh DB before viva | Re-run seed script |
| **Environment Config Errors** | High | Medium | `.env.example` files comprehensive; startup validation logging | Clear error messages on Server startup |

---

## 43. MVP vs Stretch Scope

### MVP (Required — must be complete before viva)

- [ ] All 6 actor roles functional
- [ ] Project creation and management
- [ ] Milestone lifecycle and physical progress
- [ ] Fund release lifecycle (full flow: request → verify → approve/reject)
- [ ] Server Relayer for all administrative operations
- [ ] MetaMask wallet integration for Contractor fund request
- [ ] MetaMask wallet integration for Auditor audit finding
- [ ] Blockchain event indexer
- [ ] All 10 approved blockchain events
- [ ] IPFS evidence upload and display
- [ ] AI/ML risk flagging (hybrid rule-based + Isolation Forest)
- [ ] AI contributing factors (explainability)
- [ ] Auditor investigation workflow
- [ ] Government Admin escalation
- [ ] Deadline Scheduler (Overdue detection)
- [ ] In-app notifications
- [ ] Citizen public portal
- [ ] Dark mode
- [ ] Seed/demo dataset
- [ ] Critical test coverage

### Stretch (Only if MVP is complete and time permits)

- [ ] Sepolia testnet deployment
- [ ] XGBoost comparison / AI model extension
- [ ] AI risk score history chart
- [ ] PDF preview / export for evidence
- [ ] Physical progress history chart
- [ ] Public audit summary enhancements
- [ ] Profile / account management page (UI/UX Spec labels this as a proposal)

**Stretch features must not block MVP completion.**

---

## 44. Project Definition of Done

The implementation is considered **MVP-complete** when all of the following are verified:

**Infrastructure:**
- [ ] Client runs (`npm run dev`)
- [ ] Server runs (`uvicorn`)
- [ ] PostgreSQL runs and is migrated
- [ ] Hardhat node runs
- [ ] Smart contract deployed on Hardhat

**Blockchain:**
- [ ] All 10 blockchain events emittable
- [ ] Server Relayer operational (Account #0)
- [ ] Contractor MetaMask flow operational (Account #1)
- [ ] Auditor MetaMask flow operational (Account #2)
- [ ] Blockchain Indexer operational
- [ ] `blockchain_index` populated from events

**Application workflows:**
- [ ] Project lifecycle (create → complete)
- [ ] Milestone lifecycle (define → progress → complete → overdue)
- [ ] Physical progress recording (separate from financial utilization)
- [ ] Fund lifecycle (request → verify → approve/reject → re-submit)
- [ ] IPFS evidence upload and CID display
- [ ] Notification creation and display
- [ ] Deadline scheduler (Overdue detection idempotent)

**AI:**
- [ ] AI analysis triggered by fund approval
- [ ] AI flags persisted in DB
- [ ] `AIAnomalyRecorded` anchored on blockchain
- [ ] Contributing factors displayed in Auditor UI
- [ ] AI failure does not affect fund lifecycle

**Auditor:**
- [ ] Gov Admin escalation workflow
- [ ] Auditor investigation view with AI factors
- [ ] Audit finding submission via MetaMask
- [ ] `AuditFindingRecorded` anchored on blockchain
- [ ] `flagId = 0` for escalation-originated findings

**Citizen:**
- [ ] Public portal accessible without JWT
- [ ] Restricted data absent from public responses

**UI/UX:**
- [ ] All 6 role dashboards functional
- [ ] Dark mode working
- [ ] Loading/error/empty states present
- [ ] Demo-critical screens visually polished

**Quality:**
- [ ] Critical path tests pass (contract, server, client, integration)
- [ ] Seed/demo dataset runs on fresh DB
- [ ] End-to-end demo completable in < 15 minutes

---

## 45. Traceability Matrix

| Implementation Area | Source Document | Reference |
| :--- | :--- | :--- |
| React + Vite + Tailwind + shadcn/ui | Project Definition | Section 4 (Baseline) |
| Python + FastAPI single process | Project Definition | Section 4 (Baseline) |
| PostgreSQL 15+ | Project Definition | Section 4 (Baseline) |
| Hardhat local, Chain ID 31337 | Project Definition | Section 4 / TRD Sec 11 |
| Solidity ^0.8.20, OpenZeppelin 5.x | TRD | Section 11, 12 |
| 10 approved blockchain events | System Architecture | Section 11 |
| Dual-layer blockchain (events + minimal state) | System Architecture | Section 11 |
| Server Relayer (Account #0) | System Architecture | Section 18.2 / TRD Sec 13 |
| Browser Wallet — Contractor | System Architecture | Section 18.3 / TRD Sec 13 |
| Browser Wallet — Auditor | System Architecture | Section 18.4 |
| UUID → on_chain_id mapping | System Architecture | Section 10 |
| `flagId = 0` for escalation findings | System Architecture | Section 10 |
| In-process Indexer | System Architecture | Section 12 |
| FastAPI BackgroundTasks (non-durable) | System Architecture | Section 14 / TRD Sec 17 |
| APScheduler — Overdue detection | System Architecture | Section 17 / TRD Sec 18 |
| JWT HS256, 60-min, live DB check | TRD | Section 8 |
| No JWT blacklist | TRD | Section 8 |
| AI: 5 capabilities | System Architecture | Section 14 |
| Hybrid AI (rules + Isolation Forest) | Project Definition | Section 4 / TRD Sec 16 |
| AI does not prove fraud | System Architecture | Section 14 |
| Pinata IPFS | Project Definition | Section 4 / TRD Sec 15 |
| Documents unencrypted | TRD | Section 15 |
| In-app notifications only | TRD | Section 19 |
| 6 actor roles | PRD | Actor Profiles |
| Platform Admin ≠ Government Admin | PRD / FRD | Actor Definitions |
| Financial utilization formula | FRD | Fund Release section |
| Physical progress separate from financial | FRD | Milestone / Progress section |
| Citizen portal restrictions | PRD / FRD | Citizen Actor / Public Scope |
| Dark mode | Project Definition | CR-29 |
| Academic prototype scope | Project Definition | Section 13 |
| Role-specific dashboards | UI/UX Spec | Sections 12–17 |
| AI presented as risk signals only | UI/UX Spec | Section 22 |
| AI origin (Violet) ≠ severity semantics | UI/UX Spec | Sections 7.1, 35 |

---

## 46. Deferred Implementation Decisions

The following decisions are intentionally deferred to the implementation phase and must be documented when resolved:

| Decision | Status | Notes |
| :--- | :--- | :--- |
| Exact charting library | DEFERRED | Evaluate `recharts` vs `chart.js` during Phase 16 |
| Exact data fetching library | DEFERRED | Evaluate `react-query` vs RTK Query |
| Exact animation library | DEFERRED | Evaluate `framer-motion` necessity |
| AI risk threshold value | DEFERRED | Calibrated empirically during Phase 13; default starting point ~65 per TRD Sec 16.3 |
| Isolation Forest hyperparameters (`n_estimators`, `contamination`, `max_samples`) | DEFERRED | Tuned using synthetic dataset during Phase 13 |
| AI feature weights and score blending | DEFERRED | Calibrated for balanced sensitivity during Phase 13 |
| Exact indexer polling interval | DEFERRED | Default 15s for dev, 2s for demo; not frozen upstream |
| Profile/Account management page | DEFERRED | UI/UX Proposal only; implement only if MVP is complete and time permits |
| Exact PostgreSQL index strategies | DEFERRED | Beyond PK/unique indexes specified in TRD; tune during Phase 3 |
| Python package manager (pip vs Poetry) | DEFERRED | Either acceptable; document chosen approach |
| Scheduler idempotency mechanism | DEFERRED | `notified_overdue_at` field or equivalent; verify against TRD schema before adding |

> **Not deferred (already frozen upstream):**
> - File MIME types: `application/pdf`, `image/jpeg`, `image/png` only (TRD Sec 15.2)
> - Maximum file size: 15 MB (TRD Sec 15.2)
> - Blockchain event signatures: per TRD Sec 11.3 (all 10 events)
> - Smart contract function signatures: per TRD Sec 12.2 (all 10 functions)
> - JWT algorithm, expiry, live DB check: per TRD Sec 8
> - Auditor signing model: browser-wallet only, one transaction (TRD Sec 13.2)

---

## 47. Change History

| Version | Date | Author | Summary |
| :--- | :--- | :--- | :--- |
| 1.0.0 | 2026-09-28 | AI Engineering Agent | Initial complete Implementation Plan derived from approved project baselines (Project Definition v0.3.0, PRD v1.1.0, FRD v1.0.1, TRD v1.0.1, System Architecture v1.0.3, UI/UX Design Spec v1.0.2). Covers 20 implementation phases, full API roadmap, database migration sequence, smart contract design, AI/ML implementation roadmap, risk register, traceability matrix, and project-level Definition of Done. |
| 1.1.0 | 2026-09-28 | AI Engineering Agent | Final targeted reconciliation with approved project baselines. Corrections: (1) replaced all stale blockchain event signatures with TRD-authoritative definitions (removed nameHash, descHash, ipfsCidHash, factorsHash, narrativeHash, outcomeHash, reportCidHash, authorizedBy); (2) corrected smart-contract function call references in phase flows to match approved TRD signatures; (3) replaced factorsHash with anomalyType in AIAnomalyRecorded recording; (4) fixed IPFS constraints to PDF/JPEG/PNG and 15 MB (removed WebP and 10 MB); (5) corrected Auditor finding flow to single browser-wallet transaction, removed stale "optional Server Relayer" wording; (6) clarified Indexer as sole authoritative blockchain_index writer; (7) aligned ai_flags status terminology with TRD (Open/Under Review/Reviewed — No Action/Escalated Externally); (8) demoted notified_overdue_at to implementation detail; (9) softened UI/API sequencing principle to allow design-system work before APIs; (10) removed already-frozen items (MIME types, file size) from Deferred Decisions; (11) corrected FundReleaseApproved/Rejected call parameters in fund lifecycle flow. |

---

*(End of Implementation Plan)*
