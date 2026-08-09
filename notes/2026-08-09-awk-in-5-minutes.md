# awk in 5 Minutes
_Category: tooling_

`awk` is the tool you reach for when a one-liner shell pipe needs to slice columns, filter rows, or do a bit of arithmetic — the stuff that's too fiddly for `cut` but not worth writing a script for.

## What it is

`awk` reads input line by line, splits each line into fields (whitespace by default, or whatever `-F` sets), and runs your program against every line. A program is a list of `pattern { action }` blocks: the pattern decides which lines run the action, and the action is a tiny C-like language with variables, `if`, `for`, associative arrays, and `printf`. Fields are `$1`, `$2`, …, and `$0` is the whole line. `NR` is the current line number, `NF` is the field count. Special blocks `BEGIN { }` and `END { }` run before the first line and after the last — perfect for headers and totals.

## When it matters / one example

Say you have a CSV of orders and want total revenue per country:

    awk -F, 'NR>1 { sum[$3] += $5 } END { for (c in sum) print c, sum[c] }' orders.csv

Three ideas in one line: skip the header (`NR>1`), accumulate into an associative array keyed by country, and dump totals at the end. The gotcha most people hit: default field-splitting collapses runs of whitespace, but `-F,` splits on exact commas — so an empty field between two commas counts as a real (empty) field, which changes `NF`.

---
_Logged on 2026-08-09._
