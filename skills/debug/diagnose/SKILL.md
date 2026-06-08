---
name: diagnose
description: Use when the user reports a bug, says something is broken or throwing, or describes a performance regression — disciplined diagnosis loop of reproduce, hypothesise, instrument, fix, and regression-test
---

# Diagnose

A discipline for hard bugs. Skip phases only when explicitly justified.

Use the project's CONTEXT.md to build a mental model of relevant modules. Check ADRs in the area you're touching.

## Phase 1 — Build a Feedback Loop

**This is the skill.** Everything else is mechanical. If you have a fast, deterministic, agent-runnable pass/fail signal, you will find the cause. Without one, no amount of staring at code will save you.

### Ways to construct a loop — try in roughly this order

1. **Failing test** at whatever seam reaches the bug — unit, integration, e2e.
2. **HTTP script** against a running dev server (curl, fetch, request client).
3. **CLI invocation** with fixture input, diffing stdout against a known-good snapshot.
4. **Headless browser script** — drive the UI, assert on DOM/console/network.
5. **Replay a captured trace.** Save a real request/payload/event log to disk; replay it in isolation.
6. **Throwaway harness.** Spin up a minimal subset of the system (one service, mocked deps) that exercises the bug path with a single call.
7. **Property / fuzz loop.** If "sometimes wrong output", run 1000 random inputs hunting the failure mode.
8. **Bisection harness.** Automate "boot at state X, check, repeat" so you can bisect-run it.
9. **Differential loop.** Run same input through old vs new version, diff outputs.
10. **HITL bash script.** Last resort — if a human must click, drive them with `scripts/hitl-loop.template.sh`. Captured output feeds back to you.

Build the right loop, and the bug is 90% fixed.

### Iterate on the loop

Once you have _a_ loop:
- Can it be faster? (Cache setup, skip unrelated init, narrow scope.)
- Can the signal be sharper? (Assert on the specific symptom, not "didn't crash".)
- Can it be more deterministic? (Pin time, seed RNG, isolate filesystem, freeze network.)

A 30-second flaky loop is barely better than no loop. A 2-second deterministic loop is a superpower.

### Non-deterministic bugs

Goal: **higher reproduction rate.** Loop the trigger 100×, parallelise, add stress, narrow timing windows, inject sleeps. A 50%-flake bug is debuggable; 1% is not — keep raising the rate.

### When you cannot build a loop

Stop and say so explicitly. List what you tried. Ask for: (a) access to the reproducing environment, (b) a captured artifact (HAR, log dump, core dump, screen recording), or (c) permission to add temporary production instrumentation. Do **not** hypothesise without a loop.

Do not proceed to Phase 2 until you have a loop you believe in.

## Phase 2 — Reproduce

Run the loop. Watch the bug appear. Confirm:
- The failure matches what the **user** described — not a different nearby failure.
- Reproducible across multiple runs (or high enough rate for non-deterministic).
- Exact symptom captured (error message, wrong output, timing) for later verification.

## Phase 3 — Hypothesise

Generate **3–5 ranked hypotheses** before testing any. Single-hypothesis generation anchors on the first plausible idea.

Each hypothesis must be **falsifiable** — state the prediction:

> "If <X> is the cause, then <changing Y> will make the bug disappear / <changing Z> will make it worse."

If you can't state the prediction, discard or sharpen it.

Show the ranked list to the user before testing. They often re-rank instantly. Proceed with your ranking if the user is AFK.

## Phase 4 — Instrument

Each probe maps to a specific prediction from Phase 3. **Change one variable at a time.**

Tool preference:
1. **Debugger / REPL inspection** — one breakpoint beats ten logs.
2. **Targeted logs** at boundaries that distinguish hypotheses.
3. Never "log everything and grep."

**Tag every debug log** with a unique prefix (e.g. `[DEBUG-a4f2]`). Cleanup becomes a single grep. Untagged logs survive; tagged logs die.

**Perf regressions.** Logs are usually wrong. Instead: establish a baseline measurement (timing harness, profiler, query plan), then bisect. Measure first, fix second.

## Phase 5 — Fix + Regression Test

Write the regression test **before the fix** — but only if a **correct seam** exists.

A correct seam exercises the **real bug pattern** as it occurs at the call site. If the only available seam is too shallow (single-caller test for a multi-caller bug, unit test that can't replicate the triggering chain), a regression test there gives false confidence.

**If no correct seam exists, that is the finding.** Note it. Flag this for improve-codebase-architecture.

If a correct seam exists:
1. Turn the minimised repro into a failing test at that seam.
2. Watch it fail.
3. Apply the fix.
4. Watch it pass.
5. Re-run the Phase 1 loop against the original scenario.

## Phase 6 — Cleanup + Post-Mortem

Required before declaring done:
- [ ] Original repro no longer reproduces (re-run the Phase 1 loop)
- [ ] Regression test passes (or absence of seam is documented)
- [ ] All `[DEBUG-...]` instrumentation removed (grep the prefix)
- [ ] Throwaway prototypes deleted or moved to a clearly-marked debug location
- [ ] The confirmed hypothesis stated in the commit/PR message

**Then ask: what would have prevented this bug?** If the answer involves architectural change (no good test seam, tangled callers, hidden coupling), hand off to improve-codebase-architecture with specifics. Make the recommendation **after** the fix — you have more information now than when you started.
