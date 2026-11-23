# Build a static site faster

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Reki120/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Reki120/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

An entry for the make.fun bounty "Build a static site faster" — a drop-in replacement for
makefun-site 1.0.0 that returns its output tree byte for byte, in less CPU time.

## What it does

`main.ts` is the harness's driver: it reads every case from `/input/cases.json`, answers
each one alone with `run(input)` from `solve.js`, and writes `/output/answers.json`.
`run(input)` builds one site — front matter, permalinks, Markdown, collections, tags,
layouts and the build report — exactly as makefun-site 1.0.0 does, in its own dialect
throughout: its front matter grammar, its Markdown subset (not CommonMark), its slug rule
and its template language.

## Where the time goes, and what changed

- **Escaping and inline rendering work on runs.** The reference escapes character by
  character and recurses on substrings for every construct; here plain text is scanned in
  runs and escaped a run at a time, and only the constructs themselves (code spans, strong,
  emphasis, links) break a run.
- **The Markdown lexer emits HTML as it goes.** Each line is classified with prefix checks
  (fences, headings, breaks, quotes, list markers) instead of a battery of regexes, and
  blocks are rendered in the same pass — no token objects are built in between.
- **Repeated work is done once per build.** Slugify results are cached for the length of a
  build (titles, tag names and headings repeat across a site), and each post's card is
  built once and shared by the page, index, tag and recent lists that show it.

Nothing is cached from one case to the next, and the output is a function of the input
alone, as the reference's is.

## Files

- `solve.js` — the entry: `run(input)`, dependency-free.
- `main.ts` — the driver the harness runs.
