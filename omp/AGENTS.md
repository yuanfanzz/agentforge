# Project Rules

## AgentForge Skills

This project uses [AgentForge](https://github.com/yuanfanzz/agentforge) skills.
Skills are available via `skill://` URIs in oh-my-pi.

### Workflow Skills

| When | Invoke |
|------|--------|
| Before any creative work | `skill://grill-with-docs` |
| Turning decisions into a spec | `skill://to-prd` |
| Breaking a spec into issues | `skill://to-issues` |
| Managing issue state | `skill://triage` |
| Implementing features | `skill://tdd` |
| Multi-issue execution | `skill://subagent-driven-development` |
| Parallel independent work | `skill://dispatching-parallel-agents` |
| Debugging | `skill://diagnose` |
| Code review | `skill://requesting-code-review` |
| Receiving review feedback | `skill://receiving-code-review` |
| Before claiming completion | `skill://verification-before-completion` |
| Finishing a branch | `skill://finishing-a-development-branch` |
| Architecture improvements | `skill://improve-codebase-architecture` |
| Exploring unfamiliar code | `skill://zoom-out` |
| Token-efficient communication | `skill://caveman` |
| Handing off to another agent | `skill://handoff` |
| Isolated workspaces | `skill://using-git-worktrees` |
| Creating new skills | `skill://writing-skills` |
| First-time setup | `skill://setup-agentforge` |

## Oh-My-Pi Configuration

### Active TTSR Rules

These rules fire mid-stream to enforce discipline:

- `.omp/rules/verification-gate.md` — Blocks completion claims without fresh evidence
- `.omp/rules/tdd-cycle.md` — Blocks implementation without a failing test
- `.omp/rules/no-self-review.md` — Blocks self-review patterns
- `.omp/rules/domain-language.md` — Catches generic terms that conflict with CONTEXT.md

### Tool Preferences

- **MUST** use `lsp references` before modifying any exported symbol
- **MUST** use `lsp rename` for cross-file renames — never text-based rename
- **MUST** use `ast_grep` for structural code search — never grep for syntax
- **MUST** use `read` with line selectors — never cat/head/tail
- **MUST** use `search` for content search — never grep/rg in bash
- **MUST** use `find` for file globbing — never ls/fd/find in bash
- Prefer `debug` tool (DAP) over print statements when diagnosing
- Prefer `eval` kernel for constructing test harnesses and data exploration
- Prefer `task` tool for subagent dispatch — schema-validated, IRC-coordinated

### Issue Tracker

<!-- Configured by setup-agentforge -->
Issues live in GitHub Issues.
- Read: `issue://<N>` or `read issue://`
- Write: `gh issue` CLI

### Triage Labels

<!-- Configured by setup-agentforge -->
- `needs-triage` — maintainer needs to evaluate
- `needs-info` — waiting on reporter
- `ready-for-agent` — fully specified, ready for automated work
- `ready-for-human` — needs human implementation
- `wontfix` — will not be actioned

### Domain Docs

<!-- Configured by setup-agentforge -->
- `CONTEXT.md` — project glossary (read before writing domain code)
- `docs/adr/` — architecture decision records (check before changing design)
- `docs/agents/` — AgentForge configuration (issue tracker, labels, domain layout)

## Tech Stack

<!-- Replace with your actual stack -->
- Backend: Laravel PHP
  - Test: `php artisan test`
  - Static analysis: `phpstan analyse`
  - Format: `vendor/bin/pint`
- Frontend: Next.js TypeScript
  - Test: `bun test` (or `npm test`)
  - Type check: `bun check` (or `tsc --noEmit`)
  - Format: `bun fmt` (or `prettier --write .`)

## Code Conventions

<!-- Add project-specific conventions here -->

### PHP (Laravel)
- Controllers: thin — delegate to Actions/Services
- Models: Eloquent, no business logic in models
- Actions: single-purpose invocable classes
- Tests: prefer feature tests over unit tests for business logic

### TypeScript (Next.js)
- Server Components by default, Client Components only when needed
- Server Actions for mutations
- No `any` unless absolutely necessary
- Prefer `type` over `interface` for component props
