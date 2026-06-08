# AgentForge × Oh-My-Pi Integration Strategy

## The Multiplicative Insight

AgentForge provides **process** (workflows, discipline, domain-awareness). Oh-My-Pi provides **mechanism** (tools, stream rules, subagents, memory).

Most agent skill sets are process without mechanism — they *aspire* to TDD but cannot *enforce* it. Most harnesses are mechanism without process — they have powerful tools but no opinion on how to wield them.

**AgentForge + Oh-My-Pi closes this gap.** The combination is multiplicative, not additive.

---

## Integration Architecture

```
┌──────────────────────────────────────────────────┐
│                  Oh-My-Pi Harness                  │
│  ┌──────────┐ ┌────────┐ ┌──────┐ ┌───────────┐  │
│  │ TTSR     │ │ task   │ │ eval │ │ debug     │  │
│  │ (stream  │ │ (sub-  │ │ (py+ │ │ (DAP:     │  │
│  │  rules)  │ │ agents)│ │  js) │ │ lldb,dlv, │  │
│  └────┬─────┘ └───┬────┘ └──┬───┘ │ debugpy)  │  │
│       │           │         │     └─────┬──────┘  │
│       ▼           ▼         ▼           ▼          │
│  ┌──────────────────────────────────────────────┐ │
│  │           AgentForge Skills                   │ │
│  │  ┌─────────┐  ┌────────┐  ┌───────────────┐  │ │
│  │  │ ALIGN   │  │ PLAN   │  │ BUILD         │  │ │
│  │  │ grill-  │  │ to-    │  │ tdd + subagent│  │ │
│  │  │ with-   │  │ issues │  │ + parallel    │  │ │
│  │  │ docs    │  │ +triage│  │               │  │ │
│  │  └─────────┘  └────────┘  └───────────────┘  │ │
│  │  ┌─────────┐  ┌────────┐  ┌───────────────┐  │ │
│  │  │ DEBUG   │  │ REVIEW │  │ META          │  │ │
│  │  │ diagnose│  │ code-  │  │ caveman,      │  │ │
│  │  │         │  │ review │  │ handoff, etc  │  │ │
│  │  └─────────┘  └────────┘  └───────────────┘  │ │
│  └──────────────────────────────────────────────┘ │
│                                                    │
│  ┌──────────┐ ┌──────────┐ ┌───────────────────┐  │
│  │ Commands │ │ Rules    │ │ AGENTS.md         │  │
│  │ /.omp/   │ │ /.omp/   │ │ (bridge config)   │  │
│  │ commands │ │ rules    │ │                   │  │
│  └──────────┘ └──────────┘ └───────────────────┘  │
└──────────────────────────────────────────────────┘
```

---

## 1. TTSR Rules: From Aspiration to Enforcement

**This is the highest-leverage integration.**

AgentForge states principles as "non-negotiable" but has no enforcement mechanism. Oh-My-Pi's Time-Traveling Stream Rules (TTSR) can abort the model mid-token, inject a reminder, and retry — without consuming context on every turn.

### Rule: verification-gate

```yaml
---
description: Block completion claims without fresh verification evidence
condition: "(done|complete|fixed|passing|ready to|all tests pass|works now)"
scope: "always"
---
```

**Inject on match:**
> **STOP.** You are about to claim completion. verification-before-completion applies:
> 1. IDENTIFY the command that proves this claim
> 2. RUN it fresh — right now, in this turn
> 3. READ the full output
> 4. ONLY THEN make the claim with evidence
> "Should work" / "probably passes" is not verification. Run the command.

### Rule: tdd-cycle

```yaml
---
description: Enforce red-green-refactor cycle — no implementation without a failing test
condition: "(implement|write the code|build the feature)"
scope: "tool:write(**/*.test.*), tool:edit(**/*.test.*)"
---
```

**Inject on match:**
> **TDD CYCLE CHECK:** Before writing implementation, you MUST have a failing test.
> 1. Write the test for the behavior → watch it FAIL (RED)
> 2. THEN write minimal implementation → watch it PASS (GREEN)
> 3. ONLY THEN refactor
> Never write implementation without a failing test first.

### Rule: no-self-review

```yaml
---
description: Block subagent self-review — independent reviewer required
condition: "(self.review|I('ve| have) reviewed|my own review)"
scope: "always"
---
```

**Inject on match:**
> **NO SELF-REVIEW.** subagent-driven-development requires independent review:
> - Spec compliance review by a fresh subagent
> - Code quality review by a fresh subagent
> Your own review of your own code is not acceptable. Dispatch a reviewer.

### Rule: domain-language

```yaml
---
description: Enforce CONTEXT.md glossary terms when writing domain code
condition: "(user|customer|account|order|payment|product|cart)"
scope: "tool:write(**/*.php), tool:write(**/*.ts), tool:edit(**/*.php), tool:edit(**/*.ts)"
---
```

**Inject on match:**
> **DOMAIN CHECK:** You used a term that may conflict with the project glossary.
> Read `CONTEXT.md` before writing domain code. Use the canonical terms defined there.
> If you mean something different from the glossary, state it explicitly.

### Deployment

Place these in `.omp/rules/` in your project:

```
.omp/rules/
├── verification-gate.md
├── tdd-cycle.md
├── no-self-review.md
└── domain-language.md
```

Oh-my-pi loads them automatically. They survive compaction. They fire mid-token — the model never even finishes the sentence before the rule injects.

---

## 2. Commands: One-Slash Workflow Entry

Oh-my-pi's `.omp/commands/` turn multi-step AgentForge workflows into single slash commands. Each command orchestrates skills in sequence.

### `/align` — Grill + PRD

```markdown
# Align Command

Run the full alignment pipeline:
1. Invoke `skill://grill-with-docs` — interview user, resolve every ambiguous decision, update CONTEXT.md, offer ADRs
2. When grill-with-docs completes, invoke `skill://to-prd` — synthesize into a PRD, publish to issue tracker
```

### `/plan` — Issues + Triage

```markdown
# Plan Command

Break a PRD into executable issues:
1. Read the PRD from the issue tracker (check `docs/agents/issue-tracker.md` for which tracker)
2. Invoke `skill://to-issues` — decompose into vertical-slice issues
3. Invoke `skill://triage` — classify and label all new issues
```

### `/build` — Subagent-Driven Development

```markdown
# Build Command

Execute issues via subagent-driven development:
1. Read open `ready-for-agent` issues from the tracker
2. For each issue, invoke `skill://subagent-driven-development`
3. Use oh-my-pi's `task` tool for subagent dispatch — it provides schema-validated, IRC-coordinated subagents
4. After all issues complete, invoke `skill://finishing-a-development-branch`
```

### `/diagnose` — Feedback Loop First

```markdown
# Diagnose Command

Debug using the diagnose skill:
1. Invoke `skill://diagnose` — follow the 6-phase discipline
2. Phase 1 (build feedback loop): prefer oh-my-pi's `eval` kernel for constructing test harnesses
3. Phase 4 (instrument): prefer oh-my-pi's `debug` tool (DAP) over print statements
4. Phase 6 (cleanup): use `skill://handoff` if handing to improve-codebase-architecture
```

### `/review` — Code Review

Already exists in oh-my-pi as `review-prs` — AgentForge's `requesting-code-review` + `receiving-code-review` complement it for non-PR code review scenarios.

### `/ship` — Finish Branch

```markdown
# Ship Command

Complete development:
1. Invoke `skill://verification-before-completion` — hard gate: no claims without evidence
2. Invoke `skill://finishing-a-development-branch` — structured merge/PR/discard
```

---

## 3. task Tool: Schema-Validated Subagent Orchestration

AgentForge's subagent-driven-development and dispatching-parallel-agents describe subagent patterns abstractly. Oh-my-pi's `task` tool provides the concrete implementation with capabilities AgentForge cannot achieve alone:

| AgentForge Concept | Oh-My-Pi `task` Equivalent |
|---|---|
| "Dispatch implementer subagent" | `task` with structured assignment, schema-validated return |
| "Provide full task text and context" | `context` field + `local://` URIs for large payloads |
| "Dispatch spec compliance reviewer" | Fresh `task` with reviewer prompt, `agent://<impl>/` for reading implementation output |
| "Fan out independent issues" | `task` batch with parallel subagents |
| "Subagents must not inherit session context" | `task` subagents start with clean context by default |
| "Handle implementer questions" | IRC coordination between subagents + parent |
| "Two-stage review" | Sequential `task` calls: spec reviewer, then quality reviewer |

### Subagent Prompt Templates → task Assignments

AgentForge's `implementer-prompt.md` and `spec-reviewer-prompt.md` become `task` assignments with oh-my-pi-specific instructions:

**Implementer task assignment:**
```
# Target: src/modules/ordering/OrderCancellation.php, tests/Unit/OrderCancellationTest.php
# Change:
1. Read the spec at agent://planner/final-output (the to-issues output)
2. Write a failing test for the behavior described in the issue
3. Implement the minimal code to pass
4. Run ONLY the test file you modified
5. Commit with conventional commit format
# Rules:
- Use `lsp references` before modifying any exported symbol
- Match existing patterns in the codebase — no new conventions
- Use `read` (not cat/grep) for file exploration
- NEVER run project-wide test suites
- NEVER format — caller handles formatting
```

**Spec reviewer task assignment:**
```
# Target: verify implementation at agent://implementer/ matches spec at agent://planner/
# Check:
1. Every acceptance criterion in the spec is covered by a test
2. No behavior beyond the spec was added
3. All test assertions are semantic (not implementation-detail)
4. Edge cases from the spec are handled
# Return: structured verdict — PASS or FAIL with specific gaps
```

---

## 4. AGENTS.md: The Bridge Configuration

Oh-my-pi reads `AGENTS.md` natively (and 7 other formats). The `setup-agentforge` skill should generate an oh-my-pi-aware `AGENTS.md` that:

1. Points to AgentForge skills via `skill://` URIs
2. Activates TTSR rules
3. Configures oh-my-pi-specific tool preferences
4. Maps project-specific commands

### Template

```markdown
# Project Rules

## AgentForge Skills

This project uses [AgentForge](https://github.com/yuanfanzz/agentforge) skills.
All skills are available via `skill://` URIs.

**Before any creative work:** `skill://grill-with-docs`
**Before writing implementation:** `skill://tdd`
**When debugging:** `skill://diagnose`
**Before claiming completion:** `skill://verification-before-completion`

## Oh-My-Pi Configuration

### Active Rules
- `.omp/rules/verification-gate.md`
- `.omp/rules/tdd-cycle.md`
- `.omp/rules/no-self-review.md`
- `.omp/rules/domain-language.md`

### Tool Preferences
- **MUST** use `lsp references` before modifying any exported symbol
- **MUST** use `lsp rename` for cross-file renames — never text-based
- **MUST** use `ast_grep` for structural code search — never grep for syntax
- **MUST** use `read` with line selectors — never cat/head/tail
- Prefer `debug` tool (DAP) over print statements when diagnosing

### Issue Tracker
Issues live in GitHub Issues. Use `issue://<N>` to read, `gh issue` CLI to write.

### Domain Docs
- `CONTEXT.md` — project glossary (read before writing domain code)
- `docs/adr/` — architecture decision records (check before changing design)

### Tech Stack
- Backend: Laravel PHP (`php artisan test`, `phpstan analyse`)
- Frontend: Next.js TypeScript (`bun test`, `bun check`)
```

---

## 5. Hindsight Memory: Persistent Domain Context

Oh-my-pi's `retain`/`recall` memory system persists across sessions. AgentForge's domain artifacts (CONTEXT.md, ADRs) should be retained so every session starts with domain awareness.

After `grill-with-docs` resolves new terms or creates ADRs:

```
retain: "Domain term 'Cancellation' defined as: full or partial reversal of an Order before fulfillment. Distinct from 'Refund' (post-fulfillment). See CONTEXT.md."
retain: "ADR-0003: Chose optimistic locking for Order state transitions over pessimistic. Rationale: read-heavy workload, conflict rate <0.1%. See docs/adr/0003-order-locking.md."
```

On next session start, the agent recalls these automatically — no need to re-read CONTEXT.md from scratch.

---

## 6. Eval Kernel: Feedback Loop Construction

AgentForge's `diagnose` Phase 1 (build a feedback loop) benefits enormously from oh-my-pi's `eval`:

- **Python kernel**: construct test harnesses, query databases, parse logs, run statistical analysis on flaky tests
- **JavaScript kernel**: drive HTTP clients, manipulate DOM snapshots, process JSON APIs
- **Tool loopback**: call `tool.read()`, `tool.search()`, `tool.task()` from inside eval cells — no context-switching

```
# diagnose Phase 1 example using eval
eval cell 1 (py):  "Load the failing request payload, construct a minimal reproduction"
eval cell 2 (py):  "Loop 100x against dev server, capture failure rate"
eval cell 3 (py):  "Bisect: which parameter combination triggers the bug?"
```

This replaces "build a throwaway script, run it, read output, adjust" with interactive exploration.

---

## 7. debug Tool: Real Debugging, Not Print Statements

AgentForge's `diagnose` Phase 4 (instrument) says "one breakpoint beats ten logs." Oh-my-pi's `debug` tool makes this real:

| diagnose Phase | Oh-My-Pi `debug` Equivalent |
|---|---|
| "One breakpoint beats ten logs" | `debug set_breakpoint` + `debug continue` + `debug variables` |
| "Tag every debug log with [DEBUG-xxx]" | Not needed — breakpoints leave no cleanup |
| PHP debugging | `debug` with Xdebug or `debug adapter: "php"` |
| Next.js/Node debugging | `debug` with `adapter: "node"` or Chrome DevTools Protocol |
| "Perf regressions: measure first, fix second" | `debug evaluate` for timing expressions, `debug stack_trace` for hotspots |

---

## 8. Internal URIs: Seamless Cross-Artifact Navigation

AgentForge skills reference each other and artifacts. Oh-my-pi's URI schemes make these navigable:

| AgentForge Reference | Oh-My-Pi URI |
|---|---|
| "Use the project's CONTEXT.md" | `read CONTEXT.md` (auto-resolved) |
| "Respect ADRs in the area" | `read docs/adr/` (directory listing) |
| "Publish PRD to issue tracker" | `issue://` read/write |
| "Read the implementer's output" | `agent://implementer/final-output` |
| "Check the spec" | `agent://planner/findings.issues.0` |
| "Reference skill X" | `skill://tdd`, `skill://diagnose` |

---

## 9. Structural Summaries: Faster Domain Exploration

Oh-my-pi's `read` returns structural summaries (signatures, bodies elided) by default. AgentForge skills that explore codebases (`grill-with-docs`, `zoom-out`, `diagnose`, `improve-codebase-architecture`) benefit from this:

- **grill-with-docs Phase "Cross-Reference with Code"**: read a module → get all function signatures → spot mismatches with glossary terms without reading implementation
- **zoom-out**: read `src/` directory → get the structural skeleton of the entire codebase in one call
- **diagnose Phase 3 (hypothesize)**: read the affected module → see all entry points → generate hypotheses about which path is failing

---

## 10. Installation Path

### Option A: Direct Clone (Recommended)

```bash
# Clone AgentForge into oh-my-pi's skills directory
git clone https://github.com/yuanfanzz/agentforge.git .omp/skills/agentforge

# Or symlink if you keep AgentForge elsewhere
ln -s /path/to/agentforge/skills .omp/skills/agentforge
```

Oh-my-pi discovers skills by walking `.omp/skills/` for `SKILL.md` files. The nested directory structure (`skills/align/grill-with-docs/SKILL.md`) works — oh-my-pi finds them and registers `skill://grill-with-docs`, `skill://tdd`, etc.

### Option B: Flat Layout

```bash
# Flatten into .omp/skills/ directly
cp -r agentforge/skills/*/* .omp/skills/
```

This loses the category grouping but may be simpler for `skill://` resolution.

### Option C: Symlink Categories (Clean)

```bash
for cat in align plan build debug review architecture meta; do
  ln -s /path/to/agentforge/skills/$cat .omp/skills/agentforge-$cat
done
```

Then reference as `skill://agentforge-align/grill-with-docs`, etc.

---

## Priority: What To Do First

1. **Install AgentForge skills** (Option A) — immediate process improvement
2. **Deploy TTSR rules** — turns aspirations into guardrails. Start with `verification-gate` and `tdd-cycle`
3. **Run `/setup-agentforge`** — generates `docs/agents/` config for your project
4. **Write the project AGENTS.md** — bridge that connects AgentForge skills, oh-my-pi tools, TTSR rules, and project conventions
5. **Add commands** — `/align`, `/plan`, `/build`, `/diagnose`, `/ship`
6. **Train hindsight memory** — retain CONTEXT.md terms and ADR decisions after each `grill-with-docs` session

---

## What You Get

| Without Integration | With Integration |
|---|---|
| AgentForge says "verify before claiming" | TTSR aborts the stream when model claims "done" without evidence |
| AgentForge says "red-green-refactor" | TTSR injects TDD reminder when model writes implementation without a test |
| AgentForge says "dispatch subagents" | `task` tool provides schema-validated, IRC-coordinated subagents |
| AgentForge says "build a feedback loop" | `eval` kernel provides interactive Python/JS for loop construction |
| AgentForge says "one breakpoint beats ten logs" | `debug` tool provides actual DAP breakpoints across PHP/Node/Python |
| AgentForge says "read CONTEXT.md" | `read` returns glossary instantly; `retain` persists it across sessions |
| AgentForge says "use domain language" | TTSR catches generic terms and injects glossary reminder |
| AgentForge says "independent review" | TTSR catches self-review patterns and blocks them |
