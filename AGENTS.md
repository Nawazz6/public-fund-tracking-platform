# AGENTS.md

## Current state

- Fresh git repo on branch `main` with **no commits and no files** — this file is likely the first tracked content.
- **No toolchain chosen yet**: no package manager, framework, build/test/lint commands, or CI exist. Do not assume Node/Python/etc. or invent commands like `npm test`.
- Project intent (per directory name): a public fund tracking platform. Details not yet specified.

## For future sessions

- When a stack is introduced (scaffolding, manifests, config), update this file with the exact verified commands (`install`, `dev`, `test`, `lint`, `typecheck`) in the order they must run.
- Trust executable config (manifests, scripts, CI) over anything written here; delete stale claims rather than leaving them.
