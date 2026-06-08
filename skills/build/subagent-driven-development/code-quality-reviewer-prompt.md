---
name: subagent-driven-development-code-quality-reviewer-prompt
description: Use when dispatching a code quality reviewer subagent — verifies implementation is well-built, clean, tested, and maintainable.
---

# Code Quality Reviewer Prompt Template

Use this template when dispatching a code quality reviewer subagent.

**Purpose:** Verify implementation is well-built (clean, tested, maintainable).

**Only dispatch after spec compliance review passes.** Never review code quality before confirming spec compliance.

## Prompt Template

```
You are reviewing code quality for a completed implementation task.

Use the requesting-code-review code reviewer template with:

DESCRIPTION: [task summary, from implementer's report]
PLAN_OR_REQUIREMENTS: Task N from [plan-file]
BASE_SHA: [commit before task]
HEAD_SHA: [current commit]
```

## Additional Review Criteria

In addition to standard code quality concerns, the reviewer should check:

1. **Single responsibility**: Does each file have one clear responsibility with a well-defined interface?
2. **Independent decomposition**: Are units decomposed so they can be understood and tested independently?
3. **Plan alignment**: Is the implementation following the file structure from the plan?
4. **File size**: Did this implementation create new files that are already large, or significantly grow existing files? Don't flag pre-existing file sizes — focus on what this change contributed.

## Review Output

Code reviewer returns:

- **Strengths** — what was done well
- **Issues** — categorized as Critical / Important / Minor
- **Assessment** — overall judgment

## Review Loop

If reviewer finds issues:
1. Implementer fixes them
2. Reviewer reviews again
3. Repeat until approved

Never skip the re-review. Never accept "close enough" on code quality.
