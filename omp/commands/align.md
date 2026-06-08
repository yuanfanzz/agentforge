# Align Command

Run the full alignment pipeline: grill decisions with the domain glossary, then synthesize into a PRD.

## Arguments

- `$ARGUMENTS` — optional feature description or context to focus the alignment

## Steps

### 1. Grill with Docs

Invoke `skill://grill-with-docs`. This will:
- Interview relentlessly about every aspect of the feature
- Challenge terminology against CONTEXT.md
- Sharpen fuzzy language into canonical terms
- Cross-reference claims with existing code
- Update CONTEXT.md inline as terms resolve
- Offer ADRs when decisions are hard-to-reverse, surprising, and trade-off-driven

### 2. Synthesize PRD

When grill-with-docs completes, invoke `skill://to-prd`. This will:
- Synthesize the aligned understanding into a PRD
- Publish to the issue tracker configured in `docs/agents/issue-tracker.md`
- Apply the appropriate label
