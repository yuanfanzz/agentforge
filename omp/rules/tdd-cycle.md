---
description: Enforce red-green-refactor — no implementation without a failing test first
condition: "(?i)(implement|build the|write the code|add the feature)"
scope: "tool:write(**/*.test.*), tool:write(**/*.spec.*), tool:edit(**/*.test.*), tool:edit(**/*.spec.*), tool:write(**/*Test.*), tool:write(**/*Spec.*), tool:edit(**/*Test.*), tool:edit(**/*Spec.*)"
---

**TDD CYCLE CHECK.** skill://tdd applies.

Before writing implementation:
1. Write the test for the behavior → watch it FAIL (RED)
2. THEN write minimal implementation → watch it PASS (GREEN)
3. ONLY THEN refactor

Never write implementation without a failing test first. The test describes WHAT behavior, the implementation describes HOW.
