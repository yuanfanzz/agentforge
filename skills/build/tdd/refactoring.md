---
name: tdd-refactoring
description: Use when the TDD cycle reaches REFACTOR — identifies refactor candidates and patterns to apply while tests are green.
---

# Refactor Candidates

After the TDD cycle, look for:

- **Duplication** → Extract function/class
- **Long methods** → Break into private helpers (keep tests on public interface)
- **Shallow modules** → Combine or deepen (see [deep-modules.md](deep-modules.md))
- **Feature envy** → Move logic to where data lives
- **Primitive obsession** → Introduce value objects
- **Existing code** the new code reveals as problematic

## Refactoring Discipline

1. **Only refactor while GREEN.** Never restructure when tests are failing — you lose the safety net.
2. **Run tests after each step.** Small refactors, verified immediately.
3. **Don't change behavior.** Refactoring means restructuring without changing what the system does. If a test fails during refactoring, you changed behavior — undo or fix.
4. **Consider deepening.** The best refactor hides complexity behind a simpler interface. Ask: "Could this group of methods become one method with internal complexity?"

## Signs a Refactor Is Needed

- Writing a new test requires more than 3 lines of setup
- A test name is getting hard to write because the behavior is unclear
- You're repeating the same assertion pattern across tests
- Adding a feature requires touching 4+ files
- The same 3-4 method calls appear in sequence across multiple tests (→ extract a public entry point)
