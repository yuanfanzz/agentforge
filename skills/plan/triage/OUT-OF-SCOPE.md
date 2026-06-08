# Out of Scope

A knowledge base of rejected features and changes. Prevents maintainers and agents from re-litigating already-settled questions.

## File Structure

```
.out-of-scope/
├── wontfix-use-graphql.md
├── wontfix-dark-mode-v1.md
└── wontfix-rename-customer-to-client.md
```

Each file named `wontfix-<slug>.md`.

## Template

```markdown
# <Brief title>

**Rejected:** <YYYY-MM-DD>
**Reason:** <1-2 sentences explaining the rejection>
**Context:** <Optional — any relevant discussion or alternatives considered>
```

## When to Write

When closing an enhancement as `wontfix` and the reason is non-obvious. Skip for:
- Ephemeral reasons ("not worth it right now")
- Self-evident rejections ("breaks the architecture")
- The reporter clearly understands why

## When to Read

During triage, before recommending `wontfix` on a new issue, scan for similar past rejections. Surface any match: "This resembles the rejected proposal in `.out-of-scope/wontfix-<slug>.md` — is this different?"

During grill-with-docs, reference `.out-of-scope/` to avoid re-litigating past decisions.
