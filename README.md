# WHO•E

A single-page browser app: "WHO•E — Word Games for Early Readers." Three word
games for kids — compound words, sight words, and word-building — with
`SpeechSynthesisUtterance` audio and `localStorage` progress.

## What's actually here

The whole app is one file, `index.html` (duplicated byte-for-byte at
`public/index.html`, confirmed by `md5sum` — no build step joins them).
Vanilla JS in a `<script type="module">` block, no framework or external
runtime dependency.

Three modes, switched by tapping a tile, all read straight from the code:

- **Compound Link** — three prompts share one hidden middle word (e.g.
  `BOY___ / ___SHIP / GIRL___` → `FRIEND`). The player types the answer on
  an on-screen keyboard against a 53-second timer, with two hints available.
  There's a 20-entry `COMPOUNDS` word list built into the file. Stars earned
  scale with time remaining and hint usage (max 4 per puzzle).
- **Sight Pop** — the app speaks a word (via the Web Speech API) and the
  player taps it out of three on-screen options. Word lists are the Dolch
  Pre-K through 3rd-grade sight-word sets, also hardcoded in the file.
- **Build-a-Word** — a Dolch word is scrambled; the player retypes it
  correctly on an on-screen keyboard.
- A "daily" mode picks puzzles deterministically from the date: the day's
  `YYYYMMDD` string is hashed into a seed and run through the same
  glibc-style LCG as `vector-arcade` (`s*1103515245+12345 & 0x7fffffff`), so
  `?daily=YYYYMMDD&n=1` (or `n=3`/`n=5`) gives everyone the same puzzle set
  for that day.
- Stars and streak persist in `localStorage` (`whoe_stars`, `whoe_streak`,
  `whoe_level`); the Clear button in the nav wipes the first two.

The hub link in the nav points to `https://dumbmodel.com`, matching
`vector-arcade`'s nav — this is another entry in that same family of daily
browser games.

## Discrepancy worth flagging

The footer claims "offline-capable," but there is no `manifest.json` or
service worker in this repo (`find . -iname "*manifest*" -o -iname "sw.js"`
returns nothing) — nothing here would work without a network connection to
load the page itself.

## `src/`, `package.json`, `tsconfig.json`

Not part of the served app. `src/index.ts` is a single exported string
constant (`WHO_E = "WHO-E early reader daily seed LCG glibc 1103515245"`);
nothing in `index.html` imports it, and there is no test file. No lockfile
is committed.

## Running it

Open `index.html` in a browser, or serve the directory statically —
`vercel.json` sets `outputDirectory: "public"` explicitly, so a Vercel
deploy serves the copy at `public/index.html`. `vercel.json` also rewrites
`/` to `/index.html` and sets a no-cache header specifically on `/sw.js`,
even though no `sw.js` exists in the repo.
