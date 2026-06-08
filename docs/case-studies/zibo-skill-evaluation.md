# Case Study: Zibo — Skill Evaluation for a Production Next.js Platform

> How to choose which skills to install, which to skip, and which ideas to borrow without installing anything.

## Project Profile

**Zibo** is a Next.js 16 content monetization platform (React 19, Prisma, Auth.js, MariaDB) self-hosted on a VPS with PM2. It has:

- Strict TypeScript, Zod validation, Repository pattern, Server Actions
- Vitest test infrastructure with "Agentic TDD"
- Complex domain: VIP tiers, 5 paywall types, card-key system, captcha (3 modes), real-time messaging
- `AGENTS.md` with detailed conventions and architecture docs
- **Private repo, no GitHub Issues workflow** — critical constraint

## Evaluation Framework

Each Matt Pocock skill assessed on 4 axes:

| Axis | Question |
|------|----------|
| **Fit** | Does Zibo's architecture/tech/workflow benefit? |
| **Token cost** | Does it save more tokens than it loads? |
| **Overlap** | Does AGENTS.md already cover this ground? |
| **Maintenance** | Ongoing cost to use vs benefit? |

## Results

### Tier 1 — MUST HAVE (high impact, low token cost)

| Skill | Verdict | Reasoning |
|-------|---------|-----------|
| `caveman` | ✅ Install | ~75% token reduction per response. AGENTS.md already terse. Zero maintenance. |
| `zoom-out` | ✅ Install | Prevents architecture-blind fixes in interleaved subsystems (auth→paywall→admin→captcha). Non-invocative, near-zero overhead. |
| `diagnose` | ✅ Install | Complex production stack (PM2/MariaDB/Docker/captcha modes). "Feedback loop first" discipline saves hours. Leverages existing Vitest harness. |
| `tdd` | ✅ Install | Systematizes existing "Agentic TDD". Behavior-vs-implementation + seam mocking guidance directly improves test quality. |

### Tier 2 — CONDITIONAL (valuable but need adaptation)

| Skill | Verdict | Reasoning |
|-------|---------|-----------|
| `grill-with-docs` | ⚠️ Partial install | Terminology + ADR parts are gold. Issue tracker integration is dead weight for a private repo. Mitigation: install but skip issue tracker probing; manually create `CONTEXT.md` first. |
| `grill-me` | ⚠️ Lighter alt | Pure design interrogation without doc maintenance. Use when grill-with-docs feels heavy. |
| `improve-codebase-architecture` | ⚠️ Periodic | Deep module framework fits Zibo's layered architecture. But heavy (5 sub-files + HTML report). Run weekly/biweekly, not always-on. |
| `handoff` | ⚠️ Multi-session only | Valuable when sessions split across different work. Zero value for single-session workflows. |

### Tier 3 — SKIP (token waste for Zibo)

| Skill | Why Skip |
|-------|----------|
| `to-prd`, `to-issues`, `triage`, `triage-issue`, `github-triage`, `setup-matt-pocock-skills`, `prd-to-plan`, `prd-to-issues`, `write-a-prd` | Entire issue-tracker pipeline assumes GitHub Issues + PM workflow. Zibo is private VPS, no issue tracker. |
| `prototype` | Production app, not greenfield. |
| `review` | In-progress, incomplete. No PR workflow. |
| `design-an-interface`, `qa`, `request-refactor-plan`, `ubiquitous-language` | Deprecated by upstream. |
| `teach`, `scaffold-exercises` | Educational, not engineering. |
| `edit-article`, `writing-fragments`, `writing-shape`, `writing-beats` | Content writing, not code. |
| `obsidian-vault` | Personal PKM, not project tooling. |
| `migrate-to-shoehorn` | Library-specific, not used. |
| `git-guardrails-claude-code` | Claude Code specific, not OMP-compatible. |
| `setup-pre-commit` | Trivial to hand-write. |
| `write-a-skill` | Meta-skill; only needed if creating custom skills. |

## Ideas Worth Borrowing Without Installation

These deliver 80% of the value without loading any skill:

### 1. Domain Glossary (`CONTEXT.md`)

Single highest-ROI non-code improvement for Zibo:

```markdown
# Domain Glossary

## Paywall
- **PayType**: FREE | LOGIN_ONLY | VIP_ONLY | PAID_ONLY | POINTS_PAY
- **computePayable()**: Determines final price considering VIP level
- **SVIP**: Full exemption from all paywalls
- **VIP**: 50% discount on paid content

## Assets (distinct, non-interchangeable)
- **balance**: Monetary balance (topped via card-key redeem)
- **credits**: Points earned via tasks (sign-in, posting, profile completion)
- **兽粮**: Display name for balance in UI

## Card Keys
- **Registration invite card**: Face value 20/50, used at registration for initial balance
- **Top-up card**: Face value 50/100, redeemable for balance

## Captcha
- **CAPTCHA_TYPE**: none | svg-captcha | turnstile
- **verifyTurnstileAction**: Returns false when keys not configured (security fix)
```

### 2. ADR Directory

Candidates for `docs/adr/`:
- Why in-memory settings cache with 60s TTL (not DB-per-request)
- Why Pusher/Soketi over native WebSocket
- Why JWT strategy over database sessions
- WordPress URL compatibility rewrite strategy

### 3. "Feedback Loop First" in AGENTS.md

> Before fixing any bug, construct a reproducible failure (Vitest test / curl / browser automation). Do not modify code without a pass/fail signal.

### 4. "Test Behavior, Not Implementation"

Already in AGENTS.md spirit, but strengthen:
> Good test: exercises public API, survives refactors. Bad test: mocks internal collaborators, queries DB directly.

## Final Installation for Zibo

```
# OMP global install (hidden, manual trigger)
~/.omp/skills/
├── grill-with-docs/       # /skill:grill-with-docs
├── improve-codebase-architecture/  # /skill:improve-codebase-architecture
└── handoff/               # /skill:handoff
```

Plus `caveman`, `zoom-out`, `diagnose`, `tdd` installed directly as always-on skills.

## Key Insight

**Matt Pocock skills are not "install everything."** The real discipline is:

1. **Shared language** — CONTEXT.md + ADR reduces communication tokens
2. **Feedback loops** — TDD + diagnose prevents blind code-writing
3. **Deep design** — Deep module thinking prevents code rot

Zibo was already on this path (layered architecture, TDD, AGENTS.md). The skills strengthen the discipline rather than introducing a new paradigm. Installing 4 core skills + manually adding domain docs beats blindly installing all 35.
