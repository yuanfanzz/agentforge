---
description: Enforce CONTEXT.md glossary terms — catch generic terms that conflict with domain language
condition: "(?i)(\\buser\\b|\\bcustomer\\b|\\baccount\\b|\\border\\b|\\bpayment\\b|\\bproduct\\b|\\bcart\\b|\\bsubscription\\b|\\brole\\b|\\bpermission\\b)"
scope: "tool:write(**/*.php), tool:write(**/*.ts), tool:write(**/*.tsx), tool:edit(**/*.php), tool:edit(**/*.ts), tool:edit(**/*.tsx)"
---

**DOMAIN CHECK.** You used a term that may conflict with the project glossary.

Read `CONTEXT.md` before writing domain code. Use the canonical terms defined there.

If the glossary defines a different term for what you mean, use the glossary term. If you genuinely mean something different from the glossary, state it explicitly in a comment.

Example: If CONTEXT.md says "Customer = paying entity, User = login identity" and you write "user" when you mean the paying entity, you MUST use "Customer" instead.
