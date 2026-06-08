# Build Command

Execute `ready-for-agent` issues via subagent-driven development.

## Arguments

- `$ARGUMENTS` — optional: specific issue numbers to build, or "all" for all ready issues

## Steps

### 1. Gather Issues

Read open `ready-for-agent` issues from the tracker. If `$ARGUMENTS` specifies particular issues, use those.

### 2. For Each Issue: Subagent-Driven Development

Invoke `skill://subagent-driven-development`. For each task:
1. Dispatch implementer subagent using oh-my-pi's `task` tool
2. Implementer writes failing test → implements → self-reviews
3. Dispatch spec compliance reviewer (fresh subagent)
4. Fix gaps, re-review until compliant
5. Dispatch code quality reviewer (fresh subagent)
6. Fix issues, re-review until approved
7. Mark task complete

### 3. Final Review

After all issues complete:
- Dispatch a final code reviewer across the entire implementation
- Invoke `skill://finishing-a-development-branch`

## Rules

- **MUST** use oh-my-pi's `task` tool for subagent dispatch
- **MUST NOT** skip either review stage
- **MUST NOT** dispatch multiple implementation subagents in parallel (conflicts)
- **MUST** use `skill://using-git-worktrees` for workspace isolation
- **MUST** use `skill://tdd` for each implementer subagent
