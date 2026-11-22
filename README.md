# Fast Site Builder — site-generator-speed-01-run-20261122
[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/mietrea519-cyber/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/mietrea519-cyber/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for make.fun's own makefun-site 1.0.0: `solve.js` answers
exactly as it does on every case — the same output tree, byte for byte — in
about a third of the time. `main.ts` is the harness driver: it reads every
case from `/input/cases.json`, answers each with `run()`, and writes
`/output/answers.json`.

## How it is faster

- **Markdown in one pass.** The renderer lexes and renders each body in a
  single sweep instead of building token objects first: blocks are recognised
  by their first character and rendered straight to HTML, and inline markup
  is one left-to-right scan that copies runs of plain text whole (most
  paragraph text holds no markup at all and is returned untouched after one
  scan).
- **Memoised slugs.** Slugifying is pure, so each distinct title, tag and
  heading is slugified once per build; ASCII text skips the NFKD
  normalisation and combining-mark strip entirely.
- **No re-joining.** Front matter keeps the body as a slice of the input
  instead of splitting it into lines and joining them back.
- **Array-built templates.** Rendering writes each page's pieces into one
  array and joins once, instead of concatenating a string per template node.

## Correctness

Differential-tested against the reference implementation on the kit's
practice set (3/3 cases byte-identical), on 120 freshly generated cases, on
300 random inline-markup bodies, and on hand-built edge cases covering every
front-matter refusal, Markdown block and inline form, template parse error,
include-depth limit, Unicode slug rule and pagination edge.

## Practice results (this machine, 3 cases)

| | time |
|---|---|
| makefun-site 1.0.0 (baseline) | ~470 ms |
| this entry | ~172 ms |

About 2.7× faster, with identical answers.

## Run it

```sh
MAKEFUN_INPUT_DIR=<input> MAKEFUN_OUTPUT_DIR=<output> node main.ts
```
