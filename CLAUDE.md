# CLAUDE.md

This file gives Claude Code full context for working on **The Sunday Topol** —
Aaron's crossword app.

## What this project is

A static, client-only crossword app with 200 original puzzles:

1. **The Daily (Easy)** — 100 puzzles, exactly 15×15, clues pitched at an
   American adult's everyday general knowledge (household names, geography,
   food, TV, sports).
2. **The Sunday (Medium)** — 100 puzzles, exactly 21×21, trickier vocabulary
   and more oblique clues, with long marquee entries.

Front page shows per-edition tallies (solved / in progress / not started).
Solving view has letter-level mistake checking (per clue or whole puzzle),
clue reveal, a clock that only runs while the puzzle is visible on screen,
and a fireworks bulletin with the solve time on completion.

## Owner context

- **Aaron** — Atlanta. Builds personal web apps as a hobby. GitHub
  `aarontopol`. Prefers **single-file HTML + vanilla JS, no build step**,
  Vercel for hosting. Wants step-by-step deployment instructions when
  something's new.
- **Aesthetic:** refined, editorial, serif-driven (see The Daily Topol).
  This app: newsprint cream paper, ink black, spot red `#8f2d2b`, gold
  accents; Playfair Display masthead, JetBrains Mono for clocks/labels,
  Georgia body.

## Architecture

```
the-sunday-topol/
├── index.html            # The whole UI + logic. Vanilla JS, inline CSS.
├── puzzles.js            # GENERATED — do not hand-edit. window.PUZZLES data.
├── generate-puzzles.py   # Word banks + freeform grid builder + verifier.
├── icon-512.png, apple-touch-icon.png
└── vercel.json           # Static config; immutable cache for puzzles.js
```

### Data format (`window.PUZZLES`)

`{ easy: [...100], medium: [...100] }`, each puzzle:
`{ s: size, g: [row strings, "." = block], a: [[num,row,col,len,clue],…], d: [...] }`.
Answers are read from the grid (`g`), not stored separately.

### localStorage

Key `topol-xw:<diff>:<idx>` → `{ l: letters[], m: marks[], t: seconds,
done, dt: finishSeconds }`. Marks: `"shown"` (revealed, persisted) or
`"bad"` (wrong-check, session-only).

## Known gotchas

- `puzzles.js` is generated with fixed seeds — regenerate via
  `python3 generate-puzzles.py puzzles.js` after editing the banks; the
  script self-verifies every grid and will raise on a bad bank entry.
- Word-bank entries must be A–Z only (no spaces/hyphens); `clean_bank`
  silently drops malformed or duplicate words.
- The clock deliberately never uses wall-clock deltas — it ticks only while
  `document.visibilityState === "visible"` so time away from the tab never
  counts.
- The `[hidden] { display:none !important }` rule is required because
  several containers set their own `display`.
- Grid must stay exactly 15×15 / 21×21 (`verify` asserts it): don't shrink
  to the bounding box in `finalize`.

## Roadmap ideas (not built)

- Pencil mode; per-edition streaks; print stylesheet ("really" a newspaper);
  shareable result card; dark mode.
