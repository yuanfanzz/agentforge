---
name: requesting-code-review
description: Use when completing tasks, implementing major features, or before merging to verify work meets requirements
---

# Requesting Code Review

Dispatch a code reviewer to catch issues before they cascade. The reviewer gets precisely crafted context — never your session history. This keeps the reviewer focused on the work product and preserves your context for continued work.

**Core principle:** Review early, review often.

## When to Request Review

**Mandatory:**
- After each task in subagent-driven development
- After completing a major feature
- Before merge to main

**Optional:**
- When stuck (fresh perspective)
- Before refactoring (baseline check)
- After fixing a complex bug

## How to Request

**1. Get git SHAs:**
```bash
BASE_SHA=$(git rev-parse HEAD~1)  # or origin/main
HEAD_SHA=$(git rev-parse HEAD)
```

**2. Dispatch the reviewer** using the template at `code-reviewer.md`. Fill the placeholders:
- `{DESCRIPTION}` — what you built
- `{PLAN_OR_REQUIREMENTS}` — what it should do
- `{BASE_SHA}` — starting commit
- `{HEAD_SHA}` — ending commit

**3. Act on feedback:**
- Fix Critical issues immediately
- Fix Important issues before proceeding
- Note Minor issues for later
- Push back if reviewer is wrong (with reasoning)

## Integration with Workflows

**Subagent-driven development:** Review after EACH task. Catch issues before they compound.

**Executing plans:** Review after each task or at natural checkpoints.

**Ad-hoc development:** Review before merge or when stuck.

## Red Flags

- Skip review because "it's simple"
- Ignore Critical issues
- Proceed with unfixed Important issues
- Argue with valid technical feedback

If the reviewer is wrong: push back with technical reasoning, show code/tests that prove correctness, or request clarification.

See template at: requesting-code-review/code-reviewer.md
