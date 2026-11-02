+++
title = "Functional Requirements"
weight = 1
description = "What JoinSheets does: a complete, self-contained pipeline from flat tables to a result, end to end."
+++

JoinSheets began as the user-facing layer on top of a larger project -- a
separate engine for versioned, historized storage of data pulled in from
disparate sources (`ds`). That lower layer deliberately stayed out of
semantics and UI; everything about actually looking at, combining, and
exporting the data lived one layer up. Over time that upstream layer grew
its own complete logic and split off into its own standalone product, with
its own self-contained scope -- JoinSheets.

## The data pipeline

JoinSheets covers one continuous, end-to-end flow:

1. **Acquire** -- bring data in as flat tables: either pre-generated source
   files following a manifest contract, or a direct import from an `.xlsx`
   spreadsheet, via the optional
   [JoinSheetsImport](https://github.com/JoinSheets/JoinSheetsImport)
   converter.
2. **Build a dataset** -- combine the sources that matter into one working
   dataset: set-style operations between sources (union, subtraction,
   intersection), joins, and row-selection conditions.
3. **Form a view** -- decide which fields the result actually shows and how
   they're computed: parsing and typing raw values, formula fields,
   aggregation, and priority-based collision resolution when more than one
   source contributes to the same field.
4. **Work the table** -- the everyday table operations on the built result:
   search, sort, group, filter -- without rebuilding the dataset underneath.
5. **Export** -- hand the result back out in a tabular format (Excel/CSV).

## Core requirements

- **Flat tables as the unit of work.** No nested or hierarchical data model
  -- rows and columns, the shape people already have in a spreadsheet.
- **The data types a spreadsheet actually has.** Text, dates, numbers,
  booleans, and empty values, parsed and typed consistently.
- **Priority-based collision resolution.** When the same field is available
  from more than one source, the result uses the one worth trusting more --
  a simplified echo of `ds`'s own trust-weighting idea, one layer up.
- **A result people can take with them.** The output has to leave in the
  same tabular format users came in with -- Excel or CSV -- not be locked
  inside the app.

See [Technical Decisions](../technical-decisions/) for how each of these is
actually implemented, and with what tools.
