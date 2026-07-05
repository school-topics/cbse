# Chapter build spec — Coordinates (Ganita Manjari G9, Ch.1)

**Read this first.** This is the master spec for building/upgrading the
Coordinates chapter. It references the other files in this package. Order of
work is at the bottom.

## What already exists (do not rebuild)
The live chapter already has the four-tier shell working:
- Explore (drag bed/wardrobe/door on a grid; coords, distance, quadrant update live)
- Practice (10 guided, explained, unscored)
- Quiz (5 scored, one-shot)
- Challenge (3 progressive sets)
Keep the shell, nav, scoring, and styling. This package *fills gaps and adds
depth*, it does not restart the chapter.

## The goal of this upgrade
Close the six [gap] items and tighten the [partial] items identified in
`00-coverage-map.md`, so the chapter fully covers the book — including the
deeper end-of-chapter material the current app skips.

## Two structural additions
1. **Deep-dive activities.** A new activity type alongside the four tiers, for
   the standalone interactives that don't fit "drag a point in Reiaan's room."
   Six of them, specced in `deepdives/`. Each is self-contained.
2. **NCERT section tagging.** Every activity (existing and new) gets a
   `data-ncert="<ID>"` attribute using the coverage-map IDs (e.g. `C1-3.8`).
   This is the hook for later linking activities to book sections. Cheap to add
   now, expensive to retrofit — do it as you touch each activity.

## Content sources in this package
- `00-coverage-map.md` — the section audit + stable IDs. Source of truth for
  what to cover and what's still missing.
- `01-question-bank.md` — Practice, Quiz, and Challenge questions with worked
  answers and the NCERT ID each maps to. Use to extend the existing tiers so
  the deeper material (collinearity, midpoint, circle, etc.) shows up in
  questions, not only in deep-dives.
- `deepdives/*.md` — one build spec per deep-dive interactive.

## Deep-dive build order (easy → hard)
Build in this order; each later one reuses patterns from earlier:
1. `C1-3.8-city-grid.md` — DONE (prototype + spec exist). Port it in first as
   the template for the deep-dive activity type.
2. `C1-3.9-pixel-screen.md` — easy, pairs with city grid.
3. `C1-3.6-circle-distance.md` — reuses the live-distance pattern from Explore.
4. `C1-3.3-collinearity.md` — reuses live-computation pattern.
5. `C1-3.5-midpoint-trisection.md` — HARD (discovery-based; review carefully).
6. `C1-3.7-vertices-from-midpoints.md` — HARDEST (reverse problem; top of ladder).

## Shared conventions for all deep-dives (extracted from the C1-3.8 prototype)
- Vanilla HTML/SVG/JS, no new dependencies; match the existing chapter's stack.
- Touch + mouse both work. Tap targets ≥44px effective (transparent hit-circle
  r≈9 over a small visible dot for grid nodes).
- Coordinate readouts in mono font, prominent.
- Where a book puzzle has a "gotcha," add a guided-question button that resolves
  it in one sentence (see city-grid's two buttons) rather than leaving it implicit.
- Round every displayed number.
- Reuse the chapter's existing grid/axis drawing helpers if they exist; don't
  duplicate axis-rendering code across six files — factor a shared helper.
- Each deep-dive ends by flipping its coverage-map row from [gap] to [covered].

## Definition of done for the chapter
- All six deep-dives built and reachable from the chapter menu.
- Question bank items merged into Practice/Quiz/Challenge.
- Every activity carries its `data-ncert` ID.
- Coverage map re-audited: no [gap] rows remain; [partial] rows addressed or
  consciously deferred with a note.
