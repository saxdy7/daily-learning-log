# Cache Invalidation Strategies
_Category: databases_

Phil Karlton famously said the two hard problems in computer science are naming things and cache invalidation. The second one bites because a cache is just a lie you tell for speed — and eventually the lie has to be corrected.

## What it is
Cache invalidation is the policy for deciding when a cached value is no longer trustworthy and must be removed, refreshed, or marked stale. The common strategies are TTL (expire after N seconds), write-through (update the cache on every write to the source of truth), write-behind (write to cache first, flush to DB asynchronously), and explicit invalidation (the writer tells the cache "drop key X"). A separate axis is cache-aside vs read-through: does the application check the cache and populate on miss, or does the cache layer do it transparently? Each combination trades freshness against write cost and complexity.

## When it matters / one example
It matters the moment two callers can disagree about the truth. Classic gotcha: you cache a user's profile with a 10-minute TTL and add explicit invalidation on the update endpoint — but a background job also updates that row directly in SQL, bypassing the app. Now the cache stays wrong for up to 10 minutes and support tickets roll in. The lesson: invalidation lives at the level where writes actually happen. If writes can come from multiple paths (jobs, replicas, admin tools), either route them all through one write path or use a DB-level signal like CDC/logical replication to invalidate the cache. TTL alone is a bandage, not a fix.

---
_Logged on 2026-08-12._
