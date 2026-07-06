# Chapter Playbook — building an interactive NCERT chapter

How we turn an NCERT chapter into an interactive learning app. Derived from the
Coordinates build (`class9/maths/coordinates/`); apply it to every new chapter.

## Principles

- **One self-contained file.** Vanilla HTML/SVG/JS in a single `index.html`, no
  dependencies, touch + mouse. Match the existing chapter's stack and styling.
- **Coverage maps are the source of truth.** Build the maps *first*, then build
  against them, then validate 1:1. Never author activities ad hoc.
- **`data-ncert` on everything.** Every activity carries its coverage-map ID —
  the hook that lets sections filter the bank and enables book↔activity linking.
- **Validate against the actual PDF.** Cross-check every numeric answer; run
  automated smoke tests. This is where bugs surface.
- **Reversible.** Keep work uncommitted until the user reviews; commit/push only
  when they say so.
- **Attribute.** NCERT holds copyright; credit the source, claim nothing as
  original, provide a takedown path (see `ATTRIBUTION.md`).

## Per-chapter files

```
class9/<subject>/<chapter>/
  index.html                  # the app (one file)
  iemh1XX.pdf                 # the NCERT chapter PDF (bundled for figure refs)
  spec/
    00-coverage-map.md        # topic/section audit — one row per book section
    02-questions-map.md       # every question/example — one row per item, stable IDs
```

## Process

1. **Extract the PDF.** Pull full text and probe images:
   `pip install --break-system-packages pymupdf`, then `fitz` — `page.get_text()`
   for text, `page.get_images()` / `get_drawings()` to see which figures are
   raster (extractable) vs vector (we recreate interactively).
2. **Build the topic coverage map** (`00-coverage-map.md`): walk the chapter's
   headings top to bottom, one row each — `ID | Section | what the book does |
   status (covered/partial/gap/text) | note`. IDs like `C<chap>-<group>.<n>`.
3. **Build the questions coverage map** (`02-questions-map.md`): one row per
   *every* worked example, exercise, Think-and-Reflect prompt and end-of-chapter
   question — `ID | location | question | answerability (self/fig/open) | ncert |
   status`. Tally at the end. This drives Textbook mode.
4. **Audit the existing `index.html`** — keep the working shell; the map's `gap`
   and `partial` rows are the build backlog.
5. **Pick the interactive spine.** Coordinates → the plane. Numbers → the number
   line. Build deep-dives for the `gap` rows on ONE shared grid/axis helper.
6. **Extend the question bank** from the PDF so each topic is exercised; tag
   every item with `data-ncert`.
7. **Section-first shell:** book-section cards → tiles (Learn / Practice / Quiz /
   Challenge / Deep dives, filtered by `data-ncert`) + a cumulative
   "Whole-chapter test". Each section's **Learn** is a guided concept explainer
   whose cards link into the matching interactive.
8. **Textbook mode:** render all questions-map rows with staged **Hint →
   Approach → Answer** (reveal on click), IDs matching the map. Bundle the PDF in
   an inline, scrollable, toggleable panel beside the questions.
9. **Validate** (see checklist). Fix everything it finds.
10. **Attribute, then commit + push** on the user's go-ahead.

## Deep-dive conventions

- All deep-dives share ONE `Grid(svg, view)` helper (grid/axes/ticks/primitives)
  and ONE `Drag(grid)` pointer pipeline — never duplicate axis rendering.
- Tap targets ≥ ~44px (transparent hit-circle over a small visible dot).
- Round every displayed number. Coordinate/value readouts in mono font.
- Where the book puzzle has a "gotcha," add a guided button that resolves it.
- Provide a "Load book values / Show an example" button using the exact numbers
  from the PDF, so the student can reproduce and check the textbook problem.
- End each deep-dive by flipping its coverage-map row `gap → covered`.

## Validation checklist

- **Syntax:** parse every `<script>` block (`new Function(code)`).
- **Math vs PDF:** replay every numeric answer against the book's stated values.
- **DOM smoke (jsdom):** every menu/card/tile launches and reveals its screen;
  all back-buttons return correctly; filters (section, deep-dive) are right.
- **Coverage 1:1:** assert built question IDs == questions-map IDs (0 missing,
  0 extra, 0 dup); every item has an Approach + Answer.
- **Regression:** re-run the whole suite after each change; fix stale tests.

## Definition of done

- No `gap` rows remain in `00-coverage-map.md`; `partial` rows addressed or
  consciously deferred with a note.
- Every questions-map row present in Textbook mode (Hint where useful, Approach,
  Answer); coverage validated 1:1.
- Every activity carries a `data-ncert` ID.
- Attribution present; PDF credited.
- All validation green.
