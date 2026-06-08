---
name: to-prd
description: Use when turning the current conversation context and codebase understanding into a PRD ready for the project issue tracker. Synthesizes what is already known — no interviews.
---

# To PRD

Take the current conversation context and codebase understanding and produce a PRD. Do NOT interview the user — synthesize what you already know.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary (`CONTEXT.md`) throughout the PRD, and respect any ADRs in the area you're touching.

2. Sketch out the seams at which you're going to test the feature. Prefer existing seams over new ones. Use the highest seam possible. If new seams are needed, propose them at the highest point you can.

   Check with the user that these seams match their expectations.

3. Write the PRD using the template below, then publish it to the project's issue tracker. Apply the appropriate label — no additional triage needed.

## PRD Template

### Problem Statement

The problem that the user is facing, from the user's perspective.

### Solution

The solution to the problem, from the user's perspective.

### User Stories

A long, numbered list of user stories. Each in the format:

> As a `<actor>`, I want a `<feature>`, so that `<benefit>`

Example:
> As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending

This list should be extensive and cover all aspects of the feature.

### Implementation Decisions

A list of implementation decisions made. This can include:
- The modules that will be built or modified
- The interfaces that will change
- Technical clarifications
- Architectural decisions
- Schema changes
- API contracts
- Specific interactions

Do NOT include specific file paths or code snippets — they may become outdated quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.

### Testing Decisions

- A description of what makes a good test (only test external behavior, not implementation details)
- Which modules will be tested
- Prior art for the tests (similar types of tests in the codebase)

### Out of Scope

What is out of scope for this PRD.

### Further Notes

Any further notes about the feature.
