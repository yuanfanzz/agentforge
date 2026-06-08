---
description: Block completion claims without fresh verification evidence
condition: "(?i)(done|complete|fixed|passing|ready to|all tests pass|works now|should be|probably)"
scope: "always"
---

**STOP. verification-before-completion gate.**

You are about to claim completion. The iron law: NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE.

1. IDENTIFY: What command proves this claim?
2. RUN: Execute the FULL command — fresh, complete, right now
3. READ: Full output, exit code, count failures
4. VERIFY: Does output confirm the claim?
   - NO → State actual status with evidence
   - YES → State claim WITH evidence
5. ONLY THEN: Make the claim

"Should work" / "probably passes" / "seems fine" is not verification. Run the command.
