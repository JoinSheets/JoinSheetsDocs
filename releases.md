+++
title = "Releases"
weight = 2
description = "How JoinSheets versions, packages, and ships releases -- plus hotfixes and the nightly preview."
+++

This section describes the core principles behind JoinSheets' release policy
and the workflow for shipping a new release.

## Development phase

The project is currently in beta: the core functionality is settled, and work
now is about polishing individual pieces based on what early users run into.

During alpha, changes landed fast and somewhat chaotically, as new ideas or
different ways to implement a feature came up, and releases were spontaneous
-- driven by how fast development was moving and how much had changed.

Beta means small, incremental changes -- mostly UI/UX polish and general
user-experience improvements, occasionally a new feature or an improvement to
an existing one, no large architectural jumps.

## Branches

Moving into beta required splitting branches apart -- separating the actual
result of development (the code users get) from the artifacts of the process
itself (scripts, tests, experimental/unstable/unfinished work).

The project uses the following branches:

- `develop` -- day-to-day work, where every change lands before a release.
- `main` -- a clean branch holding only the current release, with no test
  suites or developer-only scripts. Only the product itself and the material
  needed to demonstrate it live here.
- Larger changes land on `develop` through their own short-lived branches
  first; this isn't a hard rule without exceptions, but the default.
- Small changes and hotfixes go in directly, so the development tree doesn't
  grow more than it needs to.

A release, then, is `develop` merged into `main`, with development-only
artifacts (tests, the test runner, and so on) deliberately left out of that
merge.

## Release cadence

Moving into beta made the project's changes more predictable and smaller in
scope, and work on it more orderly. Small improvements happen almost
continuously, but shipping every one of them as its own release would be
excessive.

At the project's current pace, a two-week interval works well -- enough time
to accumulate changes worth a version bump, and roughly the length of a
standard two-week sprint, which is usually enough to develop one or two new
features.

Releases ship roughly every two weeks, on the 1st and the 15th of each month.

## Workflow

JoinSheets is a small app with no build step and no external dependencies --
there's no bundler, no build step, and no elaborate CI pipeline. Shipping a
release is mostly a manual process.

The release process includes:

- running the full test suite,
- preparing the release artifacts,
- merging the accumulated changes into `main`,
- tagging the release commit with its version number,
- publishing it as a GitHub Release,
- updating the JoinSheets organization's GitHub Pages site.

## Release contents

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

## Versioning

- Releases are tagged on a specific commit on `main` (the tag name is the
  version string itself, e.g. `0.2.0b1`).
- JoinSheets is currently in **beta**: version numbers continue the same
  numbering the project used during its alpha phase, just with a `b` instead
  of an `a` -- `0.1.0b1`, `0.2.0b1`, and so on.

## Hotfixes

A small, low-risk, purely visual or behavioral fix found shortly after a
release doesn't have to wait for the next scheduled one -- it can go directly
onto `main` and get mirrored onto `develop` right away, so it isn't lost (or
reintroduced) the next time `develop` merges in. This is a judgment call, not
an automatic one: something that touches more than a few lines, or needs real
testing, waits for the next regular release instead.

## Nightly build

At any given moment, two versions of the app exist side by side: the latest
official release, available on `main` and published on the JoinSheets
organization's page at
[joinsheets.github.io](https://joinsheets.github.io/), and the current
development version from `develop`, published at
[`/nightly-demo/`](https://joinsheets.github.io/nightly-demo/).

The development version can be unstable, but it's published on the
organization's site too, as the "nightly build". It isn't linked from the
site and is excluded from search engines via `robots.txt` -- reachable only
by its direct URL.

The nightly build is published to GitHub Pages between releases, with no
fixed schedule. It isn't a release-prep step -- it's a day-to-day development
tool, triggered manually, whenever, release coming up or not.
