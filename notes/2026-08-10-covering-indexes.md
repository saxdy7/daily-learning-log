# Covering Indexes
_Category: databases_

A covering index is one of the cheapest performance wins in a relational database — a query that would normally hit the table can be answered entirely from the index itself, skipping the extra I/O of a heap or clustered-key lookup.

## What it is
An index "covers" a query when every column the query needs — in the SELECT list, WHERE clause, JOIN conditions, and ORDER BY — is present inside the index. The planner sees that no additional columns are required from the base table and does an index-only scan. In Postgres this shows up as `Index Only Scan` in EXPLAIN; in SQL Server and MySQL/InnoDB the same idea applies, with SQL Server letting you declare non-key payload columns explicitly via `INCLUDE (...)`. Those included columns are stored at the leaf level of the index but are not part of the sort key, so they add storage without hurting insert performance as much as extra key columns would.

## When it matters / one example
Say you have `orders(id PK, customer_id, status, total, created_at)` and a hot dashboard query:
`SELECT status, total FROM orders WHERE customer_id = ? AND created_at > ?`.
An index on `(customer_id, created_at)` alone will find the right rows but then jump back to the heap for `status` and `total`. Changing it to `(customer_id, created_at) INCLUDE (status, total)` (or just adding those columns to the key in Postgres) lets the engine answer the query from the index alone — often a 5–10x speedup on wide tables. The tradeoff: bigger index, slightly slower writes, and it only helps queries whose column set stays within the covered columns.

---
_Logged on 2026-08-10._
