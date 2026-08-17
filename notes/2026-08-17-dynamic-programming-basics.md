# Dynamic Programming Basics

_Category: algorithms_

Dynamic programming has a reputation for being hard, but most of the difficulty
is naming. It's just recursion where you stop recomputing the same thing.

## What it is

DP applies when a problem has two properties. First, **optimal substructure**:
the best answer for a problem can be built from the best answers to smaller
versions of it. Second, **overlapping subproblems**: those smaller versions
repeat many times across the recursion tree. If both hold, you can solve each
subproblem once and store the result.

There are two ways to do that. **Memoization** (top-down) keeps the natural
recursive shape and adds a cache keyed on the function's arguments — write the
recursion first, then add a dictionary. **Tabulation** (bottom-up) throws away
recursion and fills an array in dependency order, so the smallest subproblems
are solved before anything that needs them. Same complexity, different
constant factors and stack behaviour.

## When it matters / one example

Naive Fibonacci is the classic demo: `fib(n) = fib(n-1) + fib(n-2)` recomputes
`fib(30)` an exponential number of times, so it runs in O(2^n). Cache each
result and it drops to O(n) — same code, one dictionary.

The real-world version is more interesting. Edit distance (Levenshtein), used
by spell checkers and `git diff`, is a 2D table where cell `(i, j)` is the cost
of turning the first `i` characters of one string into the first `j` of
another. Each cell depends only on its left, top, and top-left neighbours —
which means you only ever need the previous row in memory, dropping space from
O(n·m) to O(min(n, m)). That "only keep the rows you still need" trick shows up
across almost every tabulated DP, and it's usually the difference between a
solution that fits in cache and one that doesn't.

---
_Logged on 2026-08-17._
