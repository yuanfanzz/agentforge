---
description: Block subagent self-review patterns — independent reviewer required
grace_period: 0
condition: "(?i)(self.review|I('ve| have) reviewed|my own review|I checked my|reviewing my own)"
scope: "always"
---

**NO SELF-REVIEW.** skill://subagent-driven-development requires independent review.

Dual-stage review means:
1. Spec compliance review by a FRESH subagent — not you
2. Code quality review by a FRESH subagent — not you

Your own review of your own code is not acceptable. Dispatch a reviewer subagent with the reviewer prompt template. The reviewer must be independent — no shared context, no prior knowledge of implementation decisions.
