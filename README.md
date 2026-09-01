# The Sunday Topol

Two hundred original crosswords, no build step. The **Daily** edition is 100
easy 15×15 puzzles pitched at everyday American general knowledge; the
**Sunday** edition is 100 medium 21×21 puzzles with deeper cuts and trickier
clues. Progress, mistakes-checking, clue reveals, a visibility-aware solve
clock, and a fireworks bulletin when you finish.

## Quick start

```bash
# It's a static site — any server works:
cd the-sunday-topol
python3 -m http.server 8000
# → http://localhost:8000
```

Or just open `index.html` in a browser. Everything runs client-side;
progress and solve times are saved to `localStorage` on the device.

## Deploy to Vercel (step by step)

```bash
# 1. Install the Vercel CLI once, if you haven't
npm i -g vercel

# 2. From the project folder, link and deploy
cd the-sunday-topol
vercel          # first run: accept defaults, no build command, output dir "."
vercel --prod   # promote to production
```

No environment variables are needed. To hook up a custom domain, add it under
the project's **Settings → Domains** in the Vercel dashboard.

## Architecture

```
the-sunday-topol/
├── index.html            ← The whole app. Vanilla JS, inline CSS, no build step.
├── puzzles.js            ← Generated data: 200 verified puzzles (~290 KB).
├── generate-puzzles.py   ← Builds & verifies puzzles.js from the word banks.
├── icon-512.png          ← Favicon / PWA icon
├── apple-touch-icon.png  ← iOS home screen icon
└── vercel.json           ← Static hosting config (long cache for puzzles.js)
```

## Regenerating the puzzles

The word/clue banks live at the top of `generate-puzzles.py` (`EASY` and
`MEDIUM`). Add or edit entries, then:

```bash
python3 generate-puzzles.py puzzles.js
```

The generator builds freeform (criss-cross) grids — every answer interlocks,
every crossing letter is shared — then verifies each puzzle: exact grid size,
single connected component, and clue/grid consistency. It refuses to write a
set that doesn't verify. Fixed seeds make output reproducible.

## How solving works

- **Clock** — runs only while a puzzle is on screen *and* the tab is visible;
  it pauses on tab switch and resumes where it left off, surviving reloads.
- **Check Clue / Check Puzzle** — marks exactly which letters are wrong
  (red slash); marks clear when you retype the square.
- **Reveal Clue** — fills the active answer in blue.
- **Completion** — when the grid is full and correct: fireworks, plus a
  bulletin with the solve time. Solved puzzles show their time on the index.
