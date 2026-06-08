---
name: to-issues
description: Use when the user wants to convert a plan, spec, or PRD into independently-grabbable issues on the project issue tracker using tracer-bullet vertical slices.
---

# To Issues

Break a plan into independently-grabbable issues using **vertical slices** (tracer bullets). Each slice cuts through every integration layer end-to-end — not a horizontal layer.

The issue tracker and triage label vocabulary should already be configured. If not, run `setup-agentforge` first.

## Process

### 1. Gather context

Work from the conversation context. If the user passes an issue reference (number, URL, or path), fetch it from the issue tracker and read its full body and comments.

### 2. Explore the codebase (optional)

If you haven't already explored the codebase, do so now. Issue titles and descriptions must use the project's domain glossary (from CONTEXT.md) and respect any ADRs in `docs/adr/` that touch the area.

### 3. Draft vertical slices

Break the plan into **tracer bullet** issues. A tracer bullet cuts through ALL layers (schema, data, API, UI) from trigger to result. Never create horizontal layer-only slices.

Slices have a **mode**:

- **AFK** — can be implemented and merged without human interaction
- **HITL** — requires a human decision, design review, or manual step

Prefer AFK over HITL where possible.

<slice-rules>
- Each slice delivers a narrow but COMPLETE path through every layer
- A completed slice is independently demoable or verifiable
- Prefer many thin slices over few thick ones
</slice-rules>

### 4. Quiz the user

Present the proposed breakdown as a numbered list. For each slice:

- **Title**: short descriptive name
- **Mode**: AFK or HITL
- **Blocked by**: which other slices (if any) must complete first
- **User stories covered**: which user stories this addresses (if the source material has them)

Then ask:

1. Does the granularity feel right? (coarse / fine / just right)
2. Are the dependency relationships correct?
3. Should any slices be merged or split further?
4. Are the correct slices marked HITL vs AFK?

Iterate until the user approves.

### 5. Publish to the issue tracker

For each approved slice, publish a new issue. Use the body template below. These are ready for AFK agents — apply the correct triage label unless instructed otherwise.

Publish in dependency order (blockers first) so you can reference real issue identifiers in the "Blocked by" field.

<issue-template>
## Parent

A reference to the parent issue (omit if the source wasn't an existing tracker issue).

## What to build

A concise description of this vertical slice. Describe end-to-end behavior, not layer-by-layer implementation.

Avoid specific file paths or code snippets — they go stale fast. Exception: if a prototype produced a snippet (state machine, reducer, schema, type shape) that encodes a decision more precisely than prose, inline it here and note it came from a prototype. Trim to the decision-rich parts only.

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- A reference to the blocking issue (if any)

Or "None — can start immediately."
</issue-template>

Do NOT close or modify the parent issue.
