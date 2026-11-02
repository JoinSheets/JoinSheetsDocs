+++
title = "Testing"
weight = 3
description = "The project's testing approach: unit tests, the smoke test, and (eventually) broader end-to-end coverage. How to check your change didn't break anything."
+++

This page describes the project's testing approach, the main kinds of tests,
and the tools behind them. Before opening a pull request, make sure your
change didn't break anything.

JoinSheets has no automated CI -- the full suite is run by hand before each
release ships. Running it yourself before opening a pull request is good
practice too: it's the minimum protection against a sudden break during a
merge, or an implicit conflict nobody noticed.

## Unit tests

Plain [`node:test`](https://nodejs.org/api/test.html) (Node's built-in test
runner -- no test framework dependency), under `tests/`. Run the whole suite
with:

```bash
./run_tests.sh
```

(a thin wrapper around `node --test tests/*.test.js` -- an explicit file
list, not a recursive search, so the optional `import/` submodule's own
tests don't get swept into this repository's run).

| File | Covers |
|---|---|
| `dataset.test.js` | Joining sources, selection conditions, the advanced filter, sorting. |
| `entities.test.js` | Parsing numbers/dates/booleans, typing, collision resolution -- the riskiest logic in the app: edge cases (Excel serial dates, ambiguous separators) are the easiest thing to break with an eyeballed change. |
| `export.test.js` | CSV/Excel export and export filename construction. |
| `formula.test.js` | The tokenizer/parser/evaluator behind formula fields. |
| `preset.test.js` | Preset normalization and the store's reducer -- by far the largest file, since most user actions are reducer cases. |

These are pure-function tests: no `document`/`window`, nothing DOM-related.

## Smoke test

A second, separate check: `tests/smoke/dom-smoke.js`. It actually runs the
`<script>` sequence from `index.html` -- the real bootstrap, real
`sample-data/` -- inside a minimal homemade DOM stub (no `jsdom`, no npm
dependency; just enough of `document`/`window`/`sessionStorage`/
`requestAnimationFrame` for one boot-to-Table pass to run).

It catches what the unit tests structurally can't: a forgotten export, the
wrong `<script>` order in `index.html`, a broken lazy cross-module
reference. Run it with:

```bash
node tests/smoke/dom-smoke.js                   # a regular boot -> Table
node tests/smoke/dom-smoke.js --simulate-crash   # recovery from the crash watchdog
```

It isn't picked up by `node --test`/`run_tests.sh` -- the filename doesn't
match their auto-discovery pattern, so it has to be run on its own. It's also
not a substitute for a real browser: if a change touches a DOM API the stub
doesn't implement (a real `querySelector`, `CSSOM`, `getBoundingClientRect`,
an actual click), this won't catch it -- that still needs manual, visual
verification in a browser.

Broadening this kind of testing -- covering more test cases, moving it
closer to real end-to-end coverage -- is a planned future direction.

## Before opening a pull request

1. Run `./run_tests.sh` -- all tests passing is the minimum bar.
2. Run both `node tests/smoke/dom-smoke.js` modes.
3. For anything touching the DOM/UI that the smoke test doesn't check,
   open `index.html` in a browser and verify it by hand and eye.
4. Add tests for what you changed. A fix without a regression test, or a new
   piece of logic without coverage, isn't finished -- `preset.test.js` and
   `entities.test.js` are good examples of what thorough coverage looks like
   for reducer cases and parsing/typing edge cases respectively.

## Tests and releases

The full suite (both unit tests and the smoke test, in both modes) runs
before every release, and has to pass, before any change ships -- see
[Releases](../releases/).
