# HTML Report Format

The architectural review is rendered as a single self-contained HTML file in the OS temp directory. Use CSS for layout and styling. For diagrams, mix hand-built divs/SVG with a diagramming approach of your choice — use graph/flow libraries for structured relationships (call graphs, dependencies, sequences) and custom visuals for editorial content (mass diagrams, cross-sections, collapse animations).

## Scaffold

Self-contained HTML file. No external CDN dependency is required — use inline CSS and SVG. If you choose to include a diagramming library, bundle it or use a single script import that will work in a file:// context.

## Header

Repo name, date, and a compact legend: solid box = module, dashed line = seam, red arrow = leakage, thick dark box = deep module. No introduction paragraph — straight into the candidates.

## Candidate card

The diagrams carry the weight. Prose is sparse, plain, and uses the glossary terms ([LANGUAGE.md](LANGUAGE.md)) without ceremony.

Each candidate is one `<article>`:

- **Title** — short, names the deepening (e.g. "Collapse the Order intake pipeline").
- **Badge row** — recommendation strength (`Strong`, `Worth exploring`, `Speculative`), plus a tag for the dependency category (`in-process`, `local-substitutable`, `ports & adapters`, `mock`).
- **Files** — monospaced list.
- **Before / After diagram** — the centrepiece. Two columns, side by side.
- **Problem** — one sentence. What hurts.
- **Solution** — one sentence. What changes.
- **Wins** — bullets, ≤6 words each. e.g. "Tests hit one interface", "Pricing logic stops leaking", "Delete 4 shallow wrappers".
- **ADR callout** (if applicable) — one line in a warning-tinted box.

No paragraphs of explanation. If the diagram needs a paragraph to be understood, redraw the diagram.

## Diagram patterns

Pick the pattern that fits the candidate. Mix them. Don't make every diagram look the same.

### Graph/flow diagrams (dependencies / call flow)

Use when the point is "X calls Y calls Z, and look at the mess." Style leakage edges red and the deep module dark. Sequence diagrams work well for "before: 6 round-trips; after: 1."

### Hand-built boxes-and-arrows

Modules as bordered boxes with labels. Arrows as inline SVG `<line>` or `<path>` elements positioned absolutely over a relative container. Use when you want the "after" diagram to feel like one thick-bordered deep module with greyed-out internals.

### Cross-section (layered shallowness)

Stack horizontal bands to show layers a call passes through. Before: many thin layers each doing nothing. After: one thick band labelled with the consolidated responsibility.

### Mass diagram ("interface as wide as implementation")

Two rectangles per module — one for interface surface area, one for implementation. Before: interface rectangle nearly as tall as implementation (shallow). After: interface short, implementation tall (deep).

### Call-graph collapse

Before: a tree of function calls as nested boxes. After: the same tree collapsed into one box, with now-internal calls shown faded inside.

## Style guidance

- Lean editorial, not corporate-dashboard. Generous whitespace.
- Colour sparingly: one accent (green or indigo) plus red for leakage and amber for warnings.
- Keep diagrams ~320px tall so before/after sits comfortably side by side without scrolling.
- Use small uppercase labels for module labels inside diagrams — they should read as schematic, not as UI.
- The report is static — no app code, no interactivity beyond rendering.

## Top recommendation section

One larger card. Candidate name, one sentence on why, anchor link to its card.

## Tone

Plain English, concise — but the architectural nouns and verbs come straight from [LANGUAGE.md](LANGUAGE.md).

**Use exactly:** module, interface, implementation, depth, deep, shallow, seam, adapter, leverage, locality.

**Never substitute:** component, service, unit · API, signature · boundary · layer, wrapper.

**Phrasings that fit the style:**

- "Order intake module is shallow — interface nearly matches the implementation."
- "Pricing leaks across the seam."
- "Deepen: one interface, one place to test."
- "Two adapters justify the seam: production and test."

**Wins bullets** name the gain in glossary terms: *"locality: bugs concentrate in one module"*, *"leverage: one interface, N call sites"*, *"interface shrinks; implementation absorbs the wrappers"*. Don't write *"easier to maintain"* or *"cleaner code"*.

No hedging, no throat-clearing. If a sentence could be a bullet, make it a bullet. If a bullet could be cut, cut it.
