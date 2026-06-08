---
name: setup-agentforge
description: Use when setting up AgentForge skills for a repo for the first time, or when skills appear to be missing context about the issue tracker, triage labels, or domain docs. Scaffolds the per-repo configuration that the engineering skills assume.
---

# Setup AgentForge Skills

Scaffold the per-repo configuration that the AgentForge skills assume:

- **Issue tracker** — where issues live
- **Triage labels** — the strings used for the five canonical triage roles
- **Domain docs** — where `CONTEXT.md` and ADRs live, and the consumer rules for reading them

This is a prompt-driven skill, not a deterministic script. Explore, present what you found, confirm with the user, then write.

## Process

### 0. Detect Harness

Check whether the agent is running inside oh-my-pi:

- Look for `.omp/` directory at the repo root
- Check for `omp` in the environment or tool list

If oh-my-pi is detected:
- After completing the normal setup (Sections 1-5), also:
  - Copy `.omp/rules/verification-gate.md`, `.omp/rules/tdd-cycle.md`, `.omp/rules/no-self-review.md`, `.omp/rules/domain-language.md` from the AgentForge installation to the project's `.omp/rules/`
  - Copy `.omp/commands/align.md`, `.omp/commands/plan.md`, `.omp/commands/build.md`, `.omp/commands/diagnose.md`, `.omp/commands/ship.md` from the AgentForge installation to the project's `.omp/commands/`
  - Generate `AGENTS.md` at the repo root using `omp/AGENTS.md` as a template, filling in the tech stack and conventions discovered during exploration
  - Tell the user: "Oh-my-pi integration activated: 4 TTSR guardrails, 5 slash commands, and AGENTS.md bridge configured."

If oh-my-pi is NOT detected:
- Skip the oh-my-pi steps
- Tell the user: "Oh-my-pi not detected. If you use oh-my-pi, see docs/oh-my-pi-integration.md for setup instructions."

### 1. Explore

Look at the current repo to understand its starting state:

- `git remote -v` and `.git/config` — which hosting provider?
- `CONTEXT.md` and `CONTEXT-MAP.md` at the repo root
- `docs/adr/` and any per-context `docs/adr/` directories
- `docs/agents/` — does prior setup output already exist?
- `.scratch/` — sign that a local-markdown issue tracker convention is already in use

### 2. Present findings and ask

Walk the user through three decisions **one at a time**. Each section starts with a short explainer (what it is, why skills need it, what changes if they pick differently).

**Section A — Issue tracker.** Where issues live for this repo. Skills read from and write to it.

Default posture: if a `git remote` points at GitHub, propose GitHub. If it points at GitLab, propose GitLab. Otherwise offer:

- **GitHub** — issues live in the repo's GitHub Issues (uses the `gh` CLI)
- **GitLab** — issues live in the repo's GitLab Issues (uses the `glab` CLI)
- **Local markdown** — issues live as files under `.scratch/<feature>/` in this repo
- **Other** (Jira, Linear, etc.) — ask the user to describe the workflow; record it as freeform prose

**Section B — Triage label vocabulary.** When the `triage` skill processes an issue, it moves it through a state machine using labels. If the repo already uses different label names, map them here.

The five canonical roles:

- `needs-triage` — maintainer needs to evaluate
- `needs-info` — waiting on reporter
- `ready-for-agent` — fully specified, ready for automated work
- `ready-for-human` — needs human implementation
- `wontfix` — will not be actioned

Default: each role's string equals its name. Ask if they want to override any.

**Section C — Domain docs.** Some skills read `CONTEXT.md` for domain language and `docs/adr/` for past decisions. Confirm the layout:

- **Single-context** — one `CONTEXT.md` + `docs/adr/` at the repo root. Most repos are this.
- **Multi-context** — `CONTEXT-MAP.md` at the root pointing to per-context `CONTEXT.md` files.

### 3. Confirm and edit

Show a draft of `docs/agents/issue-tracker.md`, `docs/agents/triage-labels.md`, `docs/agents/domain.md`. Let them edit before writing.

### 4. Write

Write the three docs files using the templates in this skill folder as a starting point:

- [github.md](issue-trackers/github.md) — GitHub issue tracker
- [gitlab.md](issue-trackers/gitlab.md) — GitLab issue tracker
- [local.md](issue-trackers/local.md) — local-markdown issue tracker
- [triage-labels.md](triage-labels.md) — label mapping
- [domain.md](domain.md) — domain doc consumer rules

For "other" issue trackers, write `docs/agents/issue-tracker.md` from scratch using the user's description.

### 5. Done

Tell the user the setup is complete and which skills will now read from these files. Mention they can edit `docs/agents/*.md` directly later — re-running this skill is only necessary to switch issue trackers or restart from scratch.

### 6. Oh-My-Pi Post-Setup (if detected)

After writing the three config files, also set up oh-my-pi integration:

1. **Copy TTSR rules** from AgentForge's `omp/rules/` to the project's `.omp/rules/`:
   - `verification-gate.md` — blocks completion claims without evidence
   - `tdd-cycle.md` — blocks implementation without a failing test
   - `no-self-review.md` — blocks self-review patterns
   - `domain-language.md` — catches generic terms that conflict with CONTEXT.md

2. **Copy commands** from AgentForge's `omp/commands/` to the project's `.omp/commands/`:
   - `/align` — grill-with-docs → to-prd pipeline
   - `/plan` — to-issues → triage pipeline
   - `/build` — subagent-driven-development execution
   - `/diagnose` — 6-phase debug discipline
   - `/ship` — verification gate → finish branch

3. **Generate AGENTS.md** at the repo root:
   - Use `omp/AGENTS.md` as the template
   - Fill in the tech stack discovered during exploration
   - Fill in issue tracker and triage labels from Sections A and B
   - Fill in domain doc layout from Section C
   - Add project-specific code conventions discovered during exploration

4. **Tell the user**:
   ```
   Oh-my-pi integration activated:
   - 4 TTSR guardrails active (verification-gate, tdd-cycle, no-self-review, domain-language)
   - 5 slash commands available (/align, /plan, /build, /diagnose, /ship)
   - AGENTS.md bridge configured at repo root

   Try: /align to start the full alignment pipeline.
   ```
