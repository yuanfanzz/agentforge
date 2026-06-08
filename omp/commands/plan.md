# Plan Command

Break a PRD into executable issues and triage them.

## Arguments

- `$ARGUMENTS` — optional PRD reference (issue number, URL, or path)

## Steps

### 1. Locate the PRD

If `$ARGUMENTS` provided, use it. Otherwise:
- Check the issue tracker for the most recent PRD
- Read `issue://` for the PRD content

### 2. Decompose into Issues

Invoke `skill://to-issues`. This will:
- Break the PRD into vertical-slice issues
- Each issue is independently deliverable end-to-end
- Issues are ordered by dependency

### 3. Triage

Invoke `skill://triage`. This will:
- Apply labels to each new issue
- Move issues through the state machine
- Issues that are fully specified become `ready-for-agent`
