+++
title = "Contributing"
weight = 5
description = "Issues, pull requests, and how to get involved."
+++

JoinSheets is under active development, and any engagement is genuinely
appreciated -- an issue, a pull request, feedback on any platform, even a
star on GitHub.

## For users

The team is actively working on usability and the interface, and tries to
base changes on real user experience. The best contribution from a user is
an issue: report a problem, or suggest a usability improvement. Include the
app's version -- open the gear icon in the app's main window (Settings) and
check **About**.

## For developers

Pull requests are welcome, but open an issue first and get feedback before
implementing a change. Known issues and planned directions are also tracked
there -- check first, since what you have in mind might already be
underway. Either way, reaching the team through any available channel
before making a change is good practice.

Whatever's currently in scope for the upcoming release is the priority --
see the "Planned next" section of
[CHANGELOG.md](https://github.com/JoinSheets/JoinSheets/blob/main/CHANGELOG.md).

Read through the documentation to get a feel for the project -- some design
decisions can look odd at first glance without the context of how the
project got here.

Try to follow the project's established approach to branching, workflow,
testing, and git-flow:

- `main` is always clean and holds the current release -- no dev-only
  artifacts. Day-to-day work happens on `develop`; larger changes and bug
  fixes get their own branch off `develop`.
- Open the pull request against `develop`.
- Run the existing tests before that. Don't forget to add your own unit
  tests for whatever you changed.

See [Releases](../releases/), [Testing](../testing/), and the rest of the
[documentation](../) for more on branch structure, this project's git-flow,
its testing setup, and how releases ship.

## Join in

The team wants to grow -- both the people building JoinSheets and the
community using it. Join in, write in, send feedback on how the app works
for you. Some small issues are deliberately left open rather than closed
quickly, in the hope that a new contributor picks them up -- look for ones
labeled **good first issue**.
