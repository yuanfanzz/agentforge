---
name: using-git-worktrees
description: Use when starting feature work that needs isolation from current workspace or before executing implementation plans — ensures an isolated workspace exists via native tools or git worktree fallback.
---

# Using Git Worktrees

Ensure work happens in an isolated workspace. Prefer your platform's native worktree tools. Fall back to manual git worktrees only when no native tool is available.

**Core principle:** Detect existing isolation first. Then use native tools. Then fall back to git. Never fight the harness.

**Announce at start:** "I'm using the using-git-worktrees skill to set up an isolated workspace."

## Step 0: Detect Existing Isolation

**Before creating anything, check if you are already in an isolated workspace.**

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
BRANCH=$(git branch --show-current)
```

**Submodule guard:** `GIT_DIR != GIT_COMMON` is also true inside git submodules. Verify:

```bash
# If this returns a path, you're in a submodule, not a worktree — treat as normal repo
git rev-parse --show-superproject-working-tree 2>/dev/null
```

**If `GIT_DIR != GIT_COMMON` (and not a submodule):** Already in a linked worktree. Skip to Step 3 (Project Setup).

Report with branch state:
- On a branch: "Already in isolated workspace at `<path>` on branch `<name>`."
- Detached HEAD: "Already in isolated workspace at `<path>` (detached HEAD, externally managed). Branch creation needed at finish time."

**If `GIT_DIR == GIT_COMMON` (or in a submodule):** Normal repo checkout. Ask for consent before creating a worktree:

> "Would you like me to set up an isolated worktree? It protects your current branch from changes."

If the user declines, work in place and skip to Step 3.

## Step 1: Create Isolated Workspace

Try mechanisms in this order:

### 1a. Native Worktree Tools (preferred)

Do you have a way to create a worktree? It might be a tool with a name like `EnterWorktree`, `WorktreeCreate`, a `/worktree` command, or a `--worktree` flag. If you do, use it and skip to Step 3.

Only proceed to Step 1b if you have no native worktree tool available.

### 1b. Git Worktree Fallback

Create a worktree manually using git.

**Directory Selection** — follow this priority:
1. Check instructions for a declared worktree directory preference.
2. Check for existing project-local `.worktrees/` or `worktrees/`. If both exist, `.worktrees/` wins.
3. Default to `.worktrees/` at the project root.

**Safety Verification** — for project-local directories, verify ignored:
```bash
git check-ignore -q .worktrees 2>/dev/null || git check-ignore -q worktrees 2>/dev/null
```
If NOT ignored: add to `.gitignore`, commit, then proceed.

**Create the Worktree:**
```bash
project=$(basename "$(git rev-parse --show-toplevel)")
git worktree add ".worktrees/$BRANCH_NAME" -b "$BRANCH_NAME"
cd ".worktrees/$BRANCH_NAME"
```

**Sandbox fallback:** If `git worktree add` fails with a permission error, tell the user and work in the current directory instead.

## Step 3: Project Setup

Auto-detect and run appropriate setup for the project's language and build tooling. Detect the project type from its manifest files and run the corresponding install/build commands. Skip if no recognized project type is found.

## Step 4: Verify Clean Baseline

Run the project's test suite to ensure the workspace starts clean.

- **If tests fail:** Report failures, ask whether to proceed or investigate.
- **If tests pass:** Report ready.

### Report

```
Worktree ready at <full-path>
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| Already in linked worktree | Skip creation (Step 0) |
| In a submodule | Treat as normal repo (Step 0 guard) |
| Native worktree tool available | Use it (Step 1a) |
| No native tool | Git worktree fallback (Step 1b) |
| `.worktrees/` exists | Use it (verify ignored) |
| Directory not ignored | Add to .gitignore + commit |
| Permission error on create | Sandbox fallback, work in place |
| Tests fail during baseline | Report failures + ask |

## Common Mistakes

- **Fighting the harness** — using `git worktree add` when the platform already provides isolation. Fix: Step 0 detects existing isolation.
- **Skipping detection** — creating a nested worktree inside an existing one. Fix: always run Step 0 first.
- **Skipping ignore verification** — worktree contents get tracked, pollute git status. Fix: always use `git check-ignore` before creating project-local worktree.
- **Proceeding with failing tests** — can't distinguish new bugs from pre-existing issues. Fix: report failures, get explicit permission to proceed.

## Red Flags

**Never:**
- Create a worktree when Step 0 detects existing isolation
- Use `git worktree add` when you have a native worktree tool
- Skip Step 1a by jumping straight to git commands
- Create worktree without verifying it's ignored (project-local)
- Skip baseline test verification
- Proceed with failing tests without asking

**Always:**
- Run Step 0 detection first
- Prefer native tools over git fallback
- Follow directory priority: existing > default
- Verify directory is ignored for project-local
- Auto-detect and run project setup
- Verify clean test baseline
