---
name: writing-skills
description: Use when creating new skills, editing existing skills, or verifying skills work before deployment.
---

# Writing Skills

**Writing skills IS Test-Driven Development applied to process documentation.**

You write test cases (pressure scenarios with subagents), watch them fail (baseline behavior), write the skill (documentation), watch tests pass (agents comply), and refactor (close loopholes).

**Core principle:** If you didn't watch an agent fail without the skill, you don't know if the skill teaches the right thing.

## What is a Skill?

A **skill** is a reference guide for proven techniques, patterns, or tools. Skills help future agents find and apply effective approaches.

**Skills are:** Reusable techniques, patterns, tools, reference guides.

**Skills are NOT:** Narratives about how you solved a problem once.

## TDD Mapping for Skills

| TDD Concept | Skill Creation |
|-------------|----------------|
| **Test case** | Pressure scenario with subagent |
| **Production code** | Skill document (SKILL.md) |
| **Test fails (RED)** | Agent violates rule without skill (baseline) |
| **Test passes (GREEN)** | Agent complies with skill present |
| **Refactor** | Close loopholes while maintaining compliance |
| **Write test first** | Run baseline scenario BEFORE writing skill |
| **Watch it fail** | Document exact rationalizations agent uses |
| **Minimal code** | Write skill addressing those specific violations |
| **Watch it pass** | Verify agent now complies |
| **Refactor cycle** | Find new rationalizations → plug → re-verify |

## When to Create a Skill

**Create when:**
- Technique wasn't intuitively obvious to you
- You'd reference this again across projects
- Pattern applies broadly (not project-specific)
- Others would benefit

**Don't create for:**
- One-off solutions
- Standard practices well-documented elsewhere
- Project-specific conventions (put in project config)
- Mechanical constraints (if enforceable with validation, automate it — save documentation for judgment calls)

## SKILL.md Structure

**Frontmatter (YAML):**
- Two required fields: `name` and `description`
- `name`: Use letters, numbers, and hyphens only
- `description`: Third-person, starts with "Use when...", describes ONLY triggering conditions (NOT what the skill does)
  - Include specific symptoms, situations, and contexts
  - **NEVER summarize the skill's process or workflow** — agents may follow the description instead of reading the full skill
  - Keep under 500 characters if possible

```yaml
# ❌ BAD: Summarizes workflow
description: Use when executing plans — dispatches subagent per task with code review

# ✅ GOOD: Just triggering conditions
description: Use when executing implementation plans with independent tasks in the current session

# ✅ GOOD: Symptom-focused
description: Use when implementing any feature or bugfix, before writing implementation code
```

```markdown
---
name: skill-name-with-hyphens
description: Use when [specific triggering conditions and symptoms]
---

# Skill Name

## Overview
Core principle in 1-2 sentences.

## When to Use
Bullet list with SYMPTOMS and use cases. When NOT to use.

## Core Pattern (for techniques/patterns)
Before/after code comparison.

## Quick Reference
Table or bullets for scanning common operations.

## Implementation
Inline code for simple patterns. Link to separate file for heavy reference or reusable tools.

## Common Mistakes
What goes wrong + fixes.
```

## Agent Search Optimization

Agents read descriptions to decide which skills to load. Make it answer: "Should I read this skill right now?"

1. **Rich description field** — concrete triggers, symptoms, situations. Write in third person. Describe the *problem* (race conditions, inconsistent behavior) not *language-specific symptoms*.
2. **Keyword coverage** — error messages, symptoms ("flaky", "hanging"), synonyms ("timeout/hang/freeze"), tool names, library names, file types.
3. **Descriptive naming** — active voice, verb-first: `condition-based-waiting` not `async-test-helpers`, `root-cause-tracing` not `debugging-techniques`.

### Cross-Referencing Other Skills

Use skill name only with explicit requirement markers:
- ✅ `**REQUIRED BACKGROUND:** You MUST understand systematic-debugging`
- ❌ No `@` links — they force-load files immediately, consuming context before needed.

## Token Efficiency

**Target word counts:**
- Frequently-loaded skills: <200 words total
- Other skills: <500 words (still be concise)

**Techniques:**
- Move details to tool help (`--help`) instead of documenting all flags
- Cross-reference other skills instead of repeating workflow details
- Compress examples — one excellent example beats many mediocre ones
- Choose most relevant language for examples (not multi-language)

## Code Examples

**One excellent example beats many mediocre ones.**

Good example: complete and runnable, well-commented explaining WHY, from real scenario, ready to adapt.

Don't: implement in 5+ languages, create fill-in-the-blank templates, write contrived examples.

## The Iron Law

```
NO SKILL WITHOUT A FAILING TEST FIRST
```

This applies to NEW skills AND EDITS to existing skills.

Write skill before testing? Delete it. Start over.
Edit skill without testing? Same violation.

**No exceptions:** not for "simple additions," not for "just adding a section," not for "documentation updates." Don't keep untested changes as "reference." Delete means delete.

## Bulletproofing Skills Against Rationalization

Agents are smart and will find loopholes when under pressure.

### Close Every Loophole Explicitly

Don't just state the rule — forbid specific workarounds:

```markdown
Write code before test? Delete it. Start over.

**No exceptions:**
- Don't keep it as "reference"
- Don't "adapt" it while writing tests
- Delete means delete
```

### Address "Spirit vs Letter" Arguments

Add foundational principle: **"Violating the letter of the rules is violating the spirit of the rules."**

### Build Rationalization Table

Capture excuses from baseline testing:

| Excuse | Reality |
|--------|---------|
| "Too simple to test" | Simple code breaks. Test takes 30 seconds. |
| "I'll test after" | Tests passing immediately prove nothing. |
| "Tests after achieve same goals" | Tests-after = "what does this do?" Tests-first = "what should this do?" |

### Create Red Flags List

```markdown
## Red Flags - STOP and Start Over

- Code before test
- "I already manually tested it"
- "Tests after achieve the same purpose"
- "It's about spirit not ritual"

**All of these mean: Delete code. Start over with TDD.**
```

## RED-GREEN-REFACTOR for Skills

### RED: Write Failing Test (Baseline)
Run pressure scenario with subagent WITHOUT the skill. Document exact behavior: choices, rationalizations (verbatim), which pressures triggered violations.

### GREEN: Write Minimal Skill
Write skill addressing those specific rationalizations. Run same scenarios WITH skill. Agent should now comply.

### REFACTOR: Close Loopholes
Agent found new rationalization? Add explicit counter. Re-test until bulletproof.

## Testing by Skill Type

| Type | Test With | Success Criteria |
|------|-----------|-----------------|
| **Discipline-enforcing** (rules/requirements) | Academic questions + pressure scenarios. Identify rationalizations. | Agent follows rule under maximum pressure |
| **Technique** (how-to guides) | Application scenarios + variation. Missing-information tests. | Agent successfully applies technique to new scenario |
| **Pattern** (mental models) | Recognition + application scenarios. Counter-examples. | Agent correctly identifies when/how to apply pattern |
| **Reference** (documentation/APIs) | Retrieval + application scenarios. Gap testing. | Agent finds and correctly applies reference information |

## Anti-Patterns

- **Narrative example** — "In session 2025-10-03, we found..." Too specific, not reusable.
- **Multi-language dilution** — example.js, example.py, example.go. Mediocre quality, maintenance burden.
- **Generic labels** — helper1, helper2, step3. Labels should have semantic meaning.

## Skill Creation Checklist

**RED Phase:**
- [ ] Create pressure scenarios (3+ combined pressures for discipline skills)
- [ ] Run scenarios WITHOUT skill — document baseline behavior verbatim
- [ ] Identify patterns in rationalizations/failures

**GREEN Phase:**
- [ ] Name uses only letters, numbers, hyphens
- [ ] YAML frontmatter with `name` and `description`
- [ ] Description starts with "Use when...", includes specific triggers/symptoms, third person
- [ ] Address specific baseline failures
- [ ] Run scenarios WITH skill — verify agents comply

**REFACTOR Phase:**
- [ ] Identify NEW rationalizations from testing
- [ ] Add explicit counters (if discipline skill)
- [ ] Build rationalization table
- [ ] Create red flags list
- [ ] Re-test until bulletproof

**Quality:**
- [ ] Quick reference table
- [ ] Common mistakes section
- [ ] No narrative storytelling
- [ ] Supporting files only for tools or heavy reference
