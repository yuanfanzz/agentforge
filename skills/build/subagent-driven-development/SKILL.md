---
name: subagent-driven-development
description: Use when executing implementation plans with independent tasks in the current session
---

# Subagent-Driven Development

Execute a plan by dispatching a fresh subagent per task, with two-stage review after each: spec compliance review first, then code quality review.

**Why subagents:** You delegate tasks to specialized agents with isolated context. By precisely crafting their instructions and context, you ensure they stay focused and succeed at their task. They should never inherit your session's context or history — you construct exactly what they need. This also preserves your own context for coordination work.

**Core principle:** Fresh subagent per task + two-stage review (spec then quality) = high quality, fast iteration.

**Continuous execution:** Do not pause to check in with your human partner between tasks. Execute all tasks from the plan without stopping. The only reasons to stop are: a BLOCKED status you cannot resolve, ambiguity that genuinely prevents progress, or all tasks complete. "Should I continue?" prompts and progress summaries waste their time — they asked you to execute the plan, so execute it.

## When to Use

Use when you have:
1. An implementation plan with multiple tasks
2. Tasks that are mostly independent
3. A desire to stay in the current session (no parallel session handoff)

**vs. executing-plans (parallel session):**
- Same session (no context switch)
- Fresh subagent per task (no context pollution)
- Two-stage review after each task: spec compliance first, then code quality
- Faster iteration (no human-in-loop between tasks)

Don't use when tasks are tightly coupled or require a parallel session workflow.

## The Process

Per-task cycle:

1. **Dispatch implementer subagent** using [implementer-prompt.md](implementer-prompt.md). Provide full task text and context upfront — never make the subagent read files to discover requirements.
2. **Handle questions.** If the implementer asks questions before or during work, answer them completely. Don't rush them into implementation.
3. **Implementer completes** — they implement, test, commit, and self-review.
4. **Dispatch spec compliance reviewer** using [spec-reviewer-prompt.md](spec-reviewer-prompt.md). This reviewer verifies the implementation matches the specification (nothing missing, nothing extra).
5. **Fix spec gaps.** If the spec reviewer finds issues, the implementer fixes them and the reviewer re-reviews. Repeat until spec-compliant.
6. **Dispatch code quality reviewer** using [code-quality-reviewer-prompt.md](code-quality-reviewer-prompt.md). Only after spec compliance is confirmed.
7. **Fix quality issues.** If the code quality reviewer finds issues, the implementer fixes them and the reviewer re-reviews. Repeat until approved.
8. **Mark task complete.** Proceed to next task.

After all tasks:
- Dispatch a final code reviewer subagent across the entire implementation
- Use finishing-a-development-branch to merge, PR, or clean up

## Model Selection

Use the least powerful model that can handle each role to conserve cost and increase speed.

- **Mechanical implementation tasks** (isolated functions, clear specs, 1-2 files): use a fast, cheap model. Most implementation tasks are mechanical when the plan is well-specified.
- **Integration and judgment tasks** (multi-file coordination, pattern matching, debugging): use a standard model.
- **Architecture, design, and review tasks**: use the most capable available model.

**Task complexity signals:**
- Touches 1-2 files with a complete spec → cheap model
- Touches multiple files with integration concerns → standard model
- Requires design judgment or broad codebase understanding → most capable model

## Handling Implementer Status

Implementer subagents report one of four statuses. Handle each appropriately:

**DONE:** Proceed to spec compliance review.

**DONE_WITH_CONCERNS:** The implementer completed the work but flagged doubts. Read the concerns before proceeding. If the concerns are about correctness or scope, address them before review. If they're observations (e.g., "this file is getting large"), note them and proceed to review.

**NEEDS_CONTEXT:** The implementer needs information that wasn't provided. Provide the missing context and re-dispatch.

**BLOCKED:** The implementer cannot complete the task. Assess the blocker:
1. If it's a context problem, provide more context and re-dispatch with the same model
2. If the task requires more reasoning, re-dispatch with a more capable model
3. If the task is too large, break it into smaller pieces
4. If the plan itself is wrong, escalate

**Never** ignore an escalation or force the same model to retry without changes. If the implementer said it's stuck, something needs to change.

## Prompt Templates

- [implementer-prompt.md](implementer-prompt.md) — Dispatch implementer subagent
- [spec-reviewer-prompt.md](spec-reviewer-prompt.md) — Dispatch spec compliance reviewer subagent
- [code-quality-reviewer-prompt.md](code-quality-reviewer-prompt.md) — Dispatch code quality reviewer subagent

## Red Flags

**Never:**
- Start implementation on main/master branch without explicit consent
- Skip reviews (spec compliance OR code quality)
- Proceed with unfixed issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Make subagent read plan file (provide full text instead)
- Skip scene-setting context (subagent needs to understand where task fits)
- Ignore subagent questions (answer before letting them proceed)
- Accept "close enough" on spec compliance
- Skip review loops (reviewer found issues = implementer fixes = review again)
- Let implementer self-review replace actual review (both are needed)
- Start code quality review before spec compliance passes
- Move to next task while either review has open issues

**If subagent asks questions:**
- Answer clearly and completely
- Provide additional context if needed

**If reviewer finds issues:**
- Implementer (same subagent) fixes them
- Reviewer reviews again
- Repeat until approved

**If subagent fails task:**
- Dispatch fix subagent with specific instructions
- Don't try to fix manually (context pollution)

## Integration

**Required workflow skills:**
- using-git-worktrees — Ensures isolated workspace
- writing-plans — Creates the plan this skill executes
- requesting-code-review — Code review template for reviewer subagents
- finishing-a-development-branch — Complete development after all tasks

**Subagents should use:**
- tdd — Subagents follow TDD for each task

**Alternative workflow:**
- executing-plans — Use for parallel session instead of same-session execution
