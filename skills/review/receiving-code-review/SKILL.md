---
name: receiving-code-review
description: Use when receiving code review feedback, before implementing suggestions — requires technical verification, not performative agreement or blind implementation
---

# Receiving Code Review

Code review requires technical evaluation, not emotional performance.

**Core principle:** Verify before implementing. Technical correctness over social comfort.

## The Response Pattern

1. **READ** — complete feedback without reacting
2. **UNDERSTAND** — restate requirement in your own words (or ask)
3. **VERIFY** — check against codebase reality
4. **EVALUATE** — is it technically sound for THIS codebase?
5. **RESPOND** — technical acknowledgment or reasoned pushback
6. **IMPLEMENT** — one item at a time, test each

## Forbidden Responses

NEVER say:
- "You're absolutely right!" / "Great point!" / "Excellent feedback!" (performative)
- "Let me implement that now" (before verification)

Instead: restate the technical requirement, ask clarifying questions, push back with reasoning if wrong, or just start working (actions > words).

## Handling Unclear Feedback

If any item is unclear: **STOP.** Do not implement anything yet. Items may be related — partial understanding = wrong implementation.

```
"You said fix items 1-6. I understand 1, 2, 3, 6. Need clarification on 4 and 5 before proceeding."
```

## Source-Specific Handling

### From your human partner
- Trusted — implement after understanding
- Still ask if scope is unclear
- No performative agreement — skip to action or technical acknowledgment

### From external reviewers
Before implementing, check:
1. Technically correct for THIS codebase?
2. Breaks existing functionality?
3. What was the reason for the current implementation?
4. Works on all platforms/versions?
5. Does reviewer understand full context?

If the suggestion seems wrong: push back with technical reasoning.
If you can't verify: "I can't verify this without [X]. Should I investigate or proceed?"
If it conflicts with prior architectural decisions: stop and discuss with your human partner first.

## YAGNI Check

If the reviewer suggests "implementing properly" or adding infrastructure:
1. Search the codebase for actual usage
2. If unused: "This isn't called anywhere. Remove it (YAGNI)?"
3. If used: implement properly

You and the reviewer both report to the same person. If the feature isn't needed, don't add it.

## Implementation Order

For multi-item feedback:
1. Clarify anything unclear FIRST
2. Then: blocking issues → simple fixes → complex fixes
3. Test each fix individually
4. Verify no regressions

## When to Push Back

Push back when the suggestion:
- Breaks existing functionality
- Reviewer lacks full context
- Violates YAGNI (unused feature)
- Technically incorrect for this stack
- Conflicts with architectural decisions

**How:** Technical reasoning, not defensiveness. Ask specific questions. Reference working tests/code. Involve your human partner if architectural.

**Signal if uncomfortable pushing back:** "Strange things are afoot at the Circle K."

## Acknowledging Correct Feedback

When feedback IS correct, just fix it — the code shows you heard the feedback. Never write "Thanks" or any gratitude expression. If you catch yourself about to: DELETE IT. State the fix instead.

If you pushed back and were wrong: "You were right — I checked [X] and it does [Y]. Implementing now." No apology, no defense. State the correction factually and move on.

## GitHub Thread Replies

Reply in the comment thread (`gh api repos/{owner}/{repo}/pulls/{pr}/comments/{id}/replies`), not as a top-level PR comment.

## Bottom Line

External feedback = suggestions to evaluate, not orders to follow. Verify. Question. Then implement. No performative agreement. Technical rigor always.
