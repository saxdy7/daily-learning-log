# Two Pointers Pattern

_Category: algorithms_

Most "find a pair/window in this array" problems have an obvious O(n²) nested-loop
solution and a much better O(n) one. Two pointers is usually the bridge between them.

## What it is

You keep two indices into a sequence and move them according to a rule, so that
between them they only ever make one full pass — O(n) time, O(1) extra space.
There are two common shapes. **Converging pointers** start at opposite ends and
walk inward; this needs a sorted (or otherwise ordered) input, because you decide
which pointer to move based on whether the current value is too big or too small.
**Same-direction pointers** (fast/slow, or the sliding window) both start at the
left, with the fast one scanning ahead and the slow one marking the boundary of a
valid region or a write position.

The key insight is the discard argument: each move has to let you safely eliminate
a whole set of candidates you'll never need to check again. If you can't justify
that, two pointers is giving you a wrong answer, not a faster one.

## When it matters / one example

Classic case: find two numbers in a **sorted** array summing to a target. Start
`lo = 0`, `hi = n-1`. If `a[lo] + a[hi] < target`, no pair involving `a[lo]` can
ever reach the target — every other partner is smaller than `a[hi]` — so you
advance `lo` and discard a whole row of the implicit matrix. If the sum is too
big, decrement `hi`. One pass, no hash map.

The gotcha: this correctness argument depends entirely on the ordering. Apply the
same loop to an unsorted array and it silently returns wrong results rather than
failing loudly. The same trap appears in sliding-window variants, where the window
must be monotonic — growing it can only ever increase the metric — or shrinking
from the left isn't safe.

---
_Logged on 2026-08-13._
