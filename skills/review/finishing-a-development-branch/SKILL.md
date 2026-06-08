---
name: finishing-a-development-branch
description: Use when implementation is complete, all tests pass, and you need to decide how to integrate the work — presents structured options for merge, PR, or cleanup
---

# Finishing a Development Branch

Guide completion of development work with clear options and a chosen workflow.

**Core principle:** Verify tests → Detect environment → Present options → Execute choice → Clean up.

**Announce at start:** "I'm using finishing-a-development-branch to complete this work."

## Step 1: Verify Tests

Run your project's test suite before offering options.

**If tests fail:**
```
Tests failing (<N> failures). Must fix before completing:

[Show failures]

Cannot proceed with merge/PR until tests pass.
```

Stop. Do not proceed.

**If tests pass:** Continue.

## Step 2: Detect Environment

Determine workspace state:

```bash
GIT_DIR=$(cd "$(git rev-parse --git-dir)" 2>/dev/null && pwd -P)
GIT_COMMON=$(cd "$(git rev-parse --git-common-dir)" 2>/dev/null && pwd -P)
```

| State | Menu | Cleanup |
|-------|------|---------|
| `GIT_DIR == GIT_COMMON` (normal repo) | 4 options | No worktree |
| `GIT_DIR != GIT_COMMON`, named branch | 4 options | Delegate to using-git-worktrees |
| `GIT_DIR != GIT_COMMON`, detached HEAD | 3 options (no merge) | Externally managed |

## Step 3: Determine Base Branch

```bash
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

Or ask: "This branch split from main — is that correct?"

## Step 4: Present Options

**Normal repo and named-branch worktree — exactly 4 options:**

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and create a Pull Request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

**Detached HEAD — exactly 3 options:**

```
Implementation complete. You're on a detached HEAD (externally managed workspace).

1. Push as new branch and create a Pull Request
2. Keep as-is (I'll handle it later)
3. Discard this work

Which option?
```

## Step 5: Execute Choice

### Option 1: Merge Locally

```bash
# Get main repo root
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"

# Merge first — verify before removing anything
git checkout <base-branch>
git pull
git merge <feature-branch>

# Verify tests on merged result
<run your test suite>

# After merge succeeds: cleanup worktree, then delete branch
git branch -d <feature-branch>
```

Cleanup worktrees by delegating to using-git-worktrees skill.

### Option 2: Push and Create PR

```bash
git push -u origin <feature-branch>

gh pr create --title "<title>" --body "$(cat <<'EOF'
## Summary
<2-3 bullets of what changed>

## Test Plan
- [ ] <verification steps>
EOF
)"
```

Do NOT clean up the worktree — it's needed for PR iteration.

### Option 3: Keep As-Is

Report: "Keeping branch <name>. Worktree preserved at <path>." Do not clean up.

### Option 4: Discard

**Confirm first:**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path>

Type 'discard' to confirm.
```

Wait for exact confirmation.

If confirmed:
```bash
MAIN_ROOT=$(git -C "$(git rev-parse --git-common-dir)/.." rev-parse --show-toplevel)
cd "$MAIN_ROOT"
# Cleanup worktree, then force-delete branch
git branch -D <feature-branch>
```

## Step 6: Cleanup

Only runs for Options 1 and 4. Delegate worktree removal to using-git-worktrees.

For normal repos (`GIT_DIR == GIT_COMMON`): no worktree — done.

For harness-owned workspaces: do NOT remove them. The host environment manages cleanup. Leave the workspace in place.

## Quick Reference

| Option | Merge | Push | Keep Worktree | Delete Branch |
|--------|-------|------|---------------|---------------|
| 1. Merge locally | yes | - | - | yes |
| 2. Create PR | - | yes | yes | - |
| 3. Keep as-is | - | - | yes | - |
| 4. Discard | - | - | - | yes (force) |

## Red Flags

**Never:**
- Proceed with failing tests
- Merge without verifying tests on result
- Delete work without confirmation
- Force-push without explicit request
- Remove a worktree before confirming merge success
- Clean up worktrees you didn't create

**Always:**
- Verify tests before offering options
- Detect environment before presenting menu
- Present exactly 4 options (or 3 for detached HEAD)
- Get typed confirmation for Option 4
- Clean up worktree for Options 1 & 4 only
- `cd` to main repo root before worktree removal
