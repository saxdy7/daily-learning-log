# Observability: Logs, Metrics, Traces

Monitoring tells you *that* something broke. Observability is about being able to ask
why — including questions you never thought to pre-define a dashboard for.

## What it is

Observability is the property of a system that lets you infer its internal state from
the data it emits. Conventionally that data comes in three shapes. **Metrics** are
numeric time series — request rate, error count, p99 latency — cheap to store because
they're aggregated at write time, which is also why they lose per-request detail.
**Logs** are discrete, timestamped events with arbitrary detail; expensive at volume,
but they carry the specifics a metric threw away. **Traces** stitch a single request's
journey across services into a tree of spans, each with a duration and parent, so you
can see where the 800ms actually went.

The three are complementary, not redundant: metrics tell you something is wrong,
traces tell you where, logs tell you what exactly.

## When it matters / one example

The classic case is a latency spike in a microservice architecture. Your dashboard shows
p99 checkout latency jumping from 200ms to 2s. Metrics alone can't tell you which of the
eleven downstream services is responsible. A trace of a slow request shows the payment
service span consuming 1.8s — and inside it, forty sequential 45ms spans to the same
database. That's an N+1 query. Then the log lines correlated by that trace ID show the
exact SQL.

The glue is correlation: propagate a trace ID through every call and stamp it on every
log line. Without that shared identifier you have three disconnected data sources rather
than one observable system.

---
_Logged on 2026-08-18._
