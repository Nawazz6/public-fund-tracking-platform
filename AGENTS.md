# AGENTS.md

## Current state

- Git repo on branch `main`. Project definition phase is complete.
- **Stack has been selected** (see `docs/00-PROJECT-DEFINITION.md` §19 and §4 for the full baseline). No application code, scaffolding, or manifests exist yet. Do NOT invent run commands.
- **No toolchain commands yet**: no `install`, `dev`, `test`, `lint`, or `typecheck` commands have been verified. Do not claim any command works until it has been verified against actual config files.

## Selected stack (design-phase decisions — not yet implemented)

| Layer | Technology |
|---|---|
| Front-end client | React + Vite + Tailwind CSS + shadcn/ui |
| Backend server | Python + FastAPI |
| Database | PostgreSQL |
| Smart contracts | Solidity + Hardhat (local Hardhat network primary) |
| IPFS | Pinata API |
| AI/ML | scikit-learn (Isolation Forest) + rule-based hybrid, within backend server |
| Auth | JWT-based application auth; wallet connection separate |

## Monorepo structure (planned, not yet created)

```
frontend/     React/Vite front-end client
backend/      Python/FastAPI backend server
contracts/    Solidity smart contracts (Hardhat)
ml/           AI/ML training scripts and model artifacts
docs/         Project documentation
data/         Synthetic/demo data
```

## Terminology standard

- React/Vite application = **front-end client** (never "frontend server")
- Python/FastAPI application = **backend server**

## For future sessions

- When scaffolding is introduced and manifests exist, update this file with the exact verified commands (`install`, `dev`, `test`, `lint`, `typecheck`) in the order they must run.
- Trust executable config (manifests, scripts, CI) over anything written here; delete stale claims rather than leaving them.
- The canonical project design documents are in `docs/`. Read them before making architectural decisions.
- The next step is the PRD. Do NOT write application code until the PRD is reviewed and approved.
