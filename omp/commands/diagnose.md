# Diagnose Command

Debug using the diagnose skill's 6-phase discipline.

## Arguments

- `$ARGUMENTS` — bug description, error message, or failing test reference

## Steps

Invoke `skill://diagnose` and follow the 6 phases:

### Phase 1: Build a Feedback Loop
Prefer oh-my-pi's `eval` kernel (Python or JS) for constructing test harnesses.
Use `tool.read()`, `tool.search()` from inside eval cells.

### Phase 2: Reproduce
Run the loop. Confirm the failure matches what the user described.

### Phase 3: Hypothesise
Generate 3-5 ranked, falsifiable hypotheses before testing any.

### Phase 4: Instrument
Prefer oh-my-pi's `debug` tool (DAP) over print statements:
- `debug set_breakpoint` + `debug continue` + `debug variables`
- One breakpoint beats ten logs

### Phase 5: Fix + Regression Test
Write the regression test BEFORE the fix at the correct seam.

### Phase 6: Cleanup
- Re-run the Phase 1 loop — bug no longer reproduces
- Remove all instrumentation
- State the confirmed hypothesis in the commit message
- If architectural change would prevent this bug class, hand off to `skill://improve-codebase-architecture`
