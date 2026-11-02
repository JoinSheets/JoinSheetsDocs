+++
title = "Contributing"
weight = 4
description = "Issues, pull requests, and how to get involved."
+++

JoinSheets is under active development, and any engagement is genuinely
appreciated -- an issue, a pull request, feedback on any platform, even a
star on GitHub.

## For users

The team is actively working on usability and the interface, and makes
changes based on real user experience. The best contribution from a user is
an issue: report whatever got in your way or felt inconvenient. Include the
app's version -- open the gear icon (Settings) and check **About**.

## For developers

Pull requests are welcome, but open an issue first and get feedback before
implementing a change. Known issues and planned directions are tracked there
too -- check first, since what you have in mind might already be underway.
Either way, reaching the team through any available channel before making a
change is good practice.

The priority at any given moment is whatever's in scope for the upcoming
release -- see the "Planned next" section of
[CHANGELOG.md](https://github.com/JoinSheets/JoinSheets/blob/main/CHANGELOG.md).

## Workflow

- `main` is always clean and releasable — no dev-only artifacts. Day-to-day
  work happens on `develop`; larger changes and bug fixes get their own
  branch off `develop`.
- Open the pull request against `develop`.
- Run the test suite first, and add tests for whatever you changed, at least
  minimally -- following the project's existing testing approach is
  considered good practice here. See [Testing](../testing/).

## Join in

The team wants to grow -- both the people building JoinSheets and the
community using it. Join in, write in, send feedback on how the app works
for you. Some small issues are deliberately left open rather than closed
quickly, in the hope that a new contributor picks them up -- look for ones
labeled **good first issue**.

See [Releases](../releases/) for the branch structure, the git-flow this
project follows, and how releases actually ship.
