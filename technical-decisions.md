+++
title = "Technical Decisions"
weight = 2
description = "Zero-build, vanilla JS, a Redux-style store, a lazy data format -- how JoinSheets implements its functional requirements, and with what tools."
+++

[Functional Requirements](../functional-requirements/) covers *what*
JoinSheets has to do. This page covers *how* -- the technical requirements
behind that, and the concrete implementation choices that satisfy them.

## Technical requirements

These constraints aren't implementation detail -- they're requirements in
their own right, and they shape every decision below:

- **Security and zero trust.** The app has no backend and no account system
  to trust or compromise -- there's nothing on a server to attack, because
  there's no server.
- **Local-only operation.** Runs entirely client-side, including straight
  from `file://` -- no install, no server process, no build step to run
  first.
- **Works inside a closed network.** No outbound requests of any kind are
  needed for the app to function -- it has to work fully air-gapped, with
  no network path out at all.
- **No dependencies.** The app ships as its own complete set of files, with
  nothing to fetch to make it run.

**The one compromise:** a single external dependency,
[SheetJS](https://sheetjs.com/) (`xlsx.full.min.js`, Community Edition), used
by the optional [JoinSheetsImport](https://github.com/JoinSheets/JoinSheetsImport)
converter to read `.xlsx` files. It's vendored into the repository at a
fixed version, under its own license file, and never fetched from a CDN or
any other external resource -- there is nothing to reach out to, at build
time or at run time, including to update it.

## Implementation

- **No bundler, no framework.** Each module is a small UMD-style IIFE
  attaching to `window.DS_APP` in the browser and exporting via
  `module.exports` under Node for tests -- see `util.js`'s `el()` hyperscript
  helper for the lowest layer.
- **Data loaded as plain scripts.** Sources are plain `.js` files injected
  via `<script>` tags (not `fetch()`, which `file://` blocks), writing into
  a shared `window.DS` namespace.
- **A single store + reducer** (`store.js`), split into one reducer per
  action-namespace domain (`preset/*`, `group/*`, `table/*`, ...), dispatched
  through a small pub/sub (`createStore`).
- **A lazy columnar source format**: each source file defines one function
  per field, returning that field's values for every row positionally -- V8
  never materializes a field's function body unless it's actually called, so
  unused fields cost nothing to load.
- **Formulas, not `eval`.** Computed fields use a small hand-written
  tokenizer/parser/evaluator (`formula.js`), deliberately not `eval`/`new
  Function`, since presets are plain JSON files people exchange.

## Testing

Pure-function modules (`dataset.js`, `entities.js`, `formula.js`, `export.js`,
`store.js`) are the reason this architecture keeps a hard line between logic
and DOM-touching code: it's what makes them cleanly covered by `node:test`
unit tests in the first place. See [Testing](../testing/) for how to actually
run the suite.
