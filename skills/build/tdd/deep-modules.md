---
name: tdd-deep-modules
description: Use when designing module interfaces — guides toward deep modules (small interface, lots of implementation) and away from shallow pass-throughs.
---

# Deep Modules

From "A Philosophy of Software Design":

**Deep module** = small interface + lots of implementation

```
Small Interface (few methods, simple parameters)
───────────────────────────────────────────────
                                               │
         Deep Implementation                   │
         (complex logic hidden)                │
                                               │
                                               │
───────────────────────────────────────────────
```

**Shallow module** = large interface + little implementation (avoid)

```
Large Interface (many methods, complex parameters)
───────────────────────────────────────────────
         Thin Implementation (just passes through)
───────────────────────────────────────────────
```

## Design Questions

When designing interfaces, ask:

1. Can I reduce the number of methods?
2. Can I simplify the parameters?
3. Can I hide more complexity inside?

## Example

A shallow module exposes every internal step:

```
# SHALLOW: caller must orchestrate every step
module.parse(input)
module.validate(parsed)
module.transform(validated)
module.format(transformed)
```

A deep module hides complexity behind one call:

```
# DEEP: one call does it all
module.process(input)   # parsing, validation, transform, format all internal
```

## Why It Matters for TDD

Deep modules are easier to test:

- Fewer public methods = fewer tests needed
- Simpler parameters = less test setup
- Implementation can change without breaking tests
- Tests describe behavior, not internal orchestration

When you find yourself writing tests that call a sequence of methods in order, ask whether those methods should be private and the sequence should be a single public entry point.
