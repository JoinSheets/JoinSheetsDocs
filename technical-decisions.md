+++
title = "Technical Decisions"
weight = 2
description = "JoinSheets' core technical requirements, the architectural decisions behind them, and the technology stack."
+++

[Functional Requirements](../functional-requirements/) covers *what*
JoinSheets has to do. This page covers how, and with what tools and
technologies, it gets that done -- plus the technical requirements and
constraints that shaped those decisions in the first place.

## Technical requirements

JoinSheets was built as part of a larger project, under a fairly strict set
of requirements that ended up shaping both the app's architecture and its
technology stack:

- **Cross-platform.** Users need to run the app under both Windows and
  Linux. The answer was a web app, with JavaScript as the application's
  language -- which also leaves running JoinSheets as a mobile app on the
  table as a future option.
- **Network security.** The app must not open network ports or accept
  inbound/outbound requests from the outside. JoinSheets is built for local
  file-based work in a closed network, and keeps working fully isolated,
  with no network path out at all.
- **Local-only operation.** No backend, no server-side part -- everything
  runs client-side, on the browser engine. Data access goes through
  `file://` -- everything the app works with is just files in a known
  format, either supplied externally or imported through the app itself.
- **Easy to install.** JoinSheets is one fully static site, equivalent to a
  plain folder of files -- no install, no server process, no build step to
  run first. Copying the app's folder (or handing it to a colleague, data
  and settings already in it) is enough to run it anywhere.
- **No dependencies.** The app ships as its own complete set of files, with
  nothing to pull in or download to make it run. The only requirement is
  any modern browser on the user's machine.

**The one compromise:** a single external dependency,
[SheetJS](https://sheetjs.com/) (`xlsx.full.min.js`, Community Edition),
used by the optional [JoinSheetsImport](https://github.com/JoinSheets/JoinSheetsImport)
converter to read `.xlsx` files and turn them into the app's internal data
format. It's vendored into the repository at a fixed version, under its own
license file, and never fetched from a CDN or any other external resource
-- there is nothing to reach out to, at build time or at run time,
including to update it. Under especially strict requirements, this
dependency can be dropped entirely -- the app degrades gracefully, losing
only the ability to import `.xlsx` files (export, which doesn't touch
SheetJS at all, keeps working either way), rather than stopping.

For the external system the app was originally written for, that system
was the actual producer of the data files, already delivered in the
required format. Importing tabular data from arbitrary external files came
later, to cover quick one-off tasks, and stays optional -- a converter for
any other tabular format isn't hard to add if it's ever needed.

## Implementation details

- **Vanilla JavaScript.** No frameworks, no external dependencies, and a
  fairly small app -- together, that's what makes plain JavaScript (and
  hand-rolling a few small wheels, even where a proven library already
  exists) the right call here.
- **Modularity.** Each module is a small UMD-style IIFE attaching to
  `window.DS_APP` in the browser and exporting via `module.exports` under
  Node for tests -- see `util.js`'s `el()` hyperscript helper for the
  lowest layer.
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

## Style

The project writes in a functional style: the pure-function modules
(`dataset.js`, `entities.js`, `formula.js`, `export.js`, `store.js`) never
touch the DOM or a browser API, and never mutate -- `store.js`'s reducer,
for instance, builds a new state on every action via `Object.assign`
rather than patching the old one in place. Anything that needs a real
browser (loading files, the clipboard, storage) is deliberately pulled out
into its own side-effect layer (`effects.js`), kept separate from that
logic.

That split isn't just a style choice: as a side effect, it's also what
makes it possible to cleanly cover this, the riskiest layer of logic, with
`node:test` unit tests -- see [Testing](../testing/) for more on that.
