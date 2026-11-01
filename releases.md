+++
title = "Releases"
weight = 2
description = "How JoinSheets versions, packages, and ships releases -- plus hotfixes and the nightly preview."
+++

JoinSheets is a zero-build static app: there's no bundler, no build step, and no
CI pipeline. A release is a deliberate, manual act -- merging accumulated work
into `main`, packaging it, and tagging it -- not something that happens on every
push. This page describes how that works.

## Versioning

- Releases are tagged on a specific commit on `main` (the tag name is the
  version string itself, e.g. `0.2.0b1`).
- JoinSheets is currently in **beta**: version numbers continue the same
  numbering the project used during its alpha phase, just with a `b` instead
  of an `a` -- `0.1.0b1`, `0.2.0b1`, and so on.
- Beta means small, incremental changes: UI/UX work, occasional new features,
  no large architectural jumps.

## Branching

- `develop` is where day-to-day work happens. `main` is always a clean,
  releasable snapshot -- no test suites or developer-only scripts, only what
  the product itself needs to run.
- A release is `develop` merged into `main`, with development-only files
  (tests, the test runner, ...) deliberately left out of that merge.
- Larger changes land on `develop` through their own short-lived branches
  first; this isn't a hard rule without exceptions, but the default.

## What's in a release

Every release commit on `main` adds one archive (`JoinSheets-<version>.tar.gz`)
at the repository root -- a snapshot of the release's production files (code +
sample data, no tests, no submodule content). The previous version's archive
isn't kept around; its own tag and GitHub Release page are the historical
record, not the file in the tree.

The same archive is also published as the asset on that version's
[GitHub Release](https://github.com/JoinSheets/JoinSheets/releases), alongside
a description of what changed. Releases are marked `prerelease: true` for as
long as the project stays in beta.

See [CHANGELOG.md](https://github.com/JoinSheets/JoinSheets/blob/main/CHANGELOG.md)
for the actual list of changes, release by release, plus what's planned for
the next one.

## Hotfixes

A small, low-risk, purely visual or behavioral fix found shortly after a
release doesn't have to wait for the next scheduled one -- it can go directly
onto `main` and get mirrored onto `develop` right away, so it isn't lost (or
reintroduced) the next time `develop` merges in. This is a judgment call, not
an automatic process: something that touches more than a few lines, or needs
real testing, waits for the next regular release instead.

A hotfix like this doesn't necessarily get its own fresh archive and GitHub
Release on the spot -- the packaged artifacts catch up at the next real
release, which bundles everything accumulated since, hotfixes included.

## Nightly preview

Separately from all of the above: [joinsheets.github.io](https://joinsheets.github.io/)'s
own `build.sh` builds two things every time it runs -- the official site (with
the live demo pinned to the latest release tag) and a second copy of the demo
app built from `develop`'s current tip, published at
[`/nightly-demo/`](https://joinsheets.github.io/nightly-demo/). It isn't linked
from anywhere on the site and is excluded from search engines via
`robots.txt` -- reachable by URL, not advertised.

This isn't a release-prep step or a gate anything has to pass -- it's a
day-to-day development tool, run whenever, release coming up or not. Its main
value: it catches two kinds of problems a purely local preview can't --
visual/UX issues that only become obvious once you actually look at the
rendered page, and environment-specific behavior like GitHub Pages' own CDN
caching, which a local Hugo server simply doesn't reproduce.
