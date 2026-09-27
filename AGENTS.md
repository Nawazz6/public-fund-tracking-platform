# AGENTS.md

## Current state

- Git repo on branch `main`. Project definition is complete and fully approved (v0.3.0).
- **Stack has been selected** (see `docs/00-PROJECT-DEFINITION.md` §19 and §4 for the full baseline). No application code, scaffolding, or manifests exist yet. Do NOT invent run commands.
- **No toolchain commands yet**: no `install`, `dev`, `test`, `lint`, or `typecheck` commands have been verified. Do not claim any command works until it has been verified against actual config files.

## Selected stack (design-phase decisions — not yet implemented)

| Layer | Technology |
|---|---|
| **Client** | React + Vite + Tailwind CSS + shadcn/ui (dark mode in scope) |
| **Server** | Python + FastAPI (single process) |
| Database | PostgreSQL |
| Smart contracts | Solidity + Hardhat (local Hardhat network primary; dual-layer: events + minimal state) |
| IPFS | Pinata API |
| AI/ML | scikit-learn (Isolation Forest) + rule-based hybrid, within Server process |
| Auth | JWT-based application auth; wallet connection separate |

## Monorepo structure (planned, not yet created)

```
frontend/     Client (React + Vite)
backend/      Server (Python + FastAPI)
contracts/    Solidity smart contracts (Hardhat)
ml/           AI/ML training scripts and model artifacts
docs/         Project documentation
data/         Synthetic/demo data
```

> Directory names `frontend/` and `backend/` are the planned repository directory names and are NOT changed by the Client/Server terminology update.

## Terminology standard

- React/Vite application = **Client** (never "front-end client", "frontend", or "frontend server")
- Python/FastAPI application = **Server** (never "backend server", "backend", or "frontend server")
- Directory names `frontend/` and `backend/` are unaffected by this correction

## For future sessions

- When scaffolding is introduced and manifests exist, update this file with the exact verified commands (`install`, `dev`, `test`, `lint`, `typecheck`) in the order they must run.
- Trust executable config (manifests, scripts, CI) over anything written here; delete stale claims rather than leaving them.
- The canonical project design documents are in `docs/`. Read them before making architectural decisions.
- Project Definition v0.3.0 is approved. All Open Questions (OQ-A through OQ-E) are resolved. The next step is the PRD.
- Do NOT write application code, scaffold directories, or install dependencies until the PRD is reviewed and approved and an Implementation Plan has been confirmed.
