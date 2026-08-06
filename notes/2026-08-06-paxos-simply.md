# Paxos, Simply
_Category: distributed-systems_

Paxos gets called impenetrable so often that Leslie Lamport had to write a follow-up called "Paxos Made Simple." The core idea is actually small — the hard part is convincing yourself it works when messages drop, nodes crash, and the network partitions.

## What it is

Paxos is a consensus algorithm: a way for a group of nodes to agree on a single value even when some of them fail. Three roles participate — proposers suggest values, acceptors vote, learners find out what was decided. It runs in two phases. **Phase 1 (prepare):** a proposer picks a proposal number `n`, higher than any it's used before, and asks a majority of acceptors "promise me you'll ignore anything numbered less than `n`." **Phase 2 (accept):** if the majority promised, the proposer asks them to accept its value at number `n`. Once a majority accepts, the value is chosen and cannot change.

The trick that makes it safe: in phase 1, acceptors also tell the proposer about any value they've already accepted. If any acceptor reports a prior value, the proposer must propose *that* value in phase 2, not its own. This is how Paxos preserves the "once chosen, forever chosen" property under any ordering of messages.

## When it matters / one example

Almost every strongly-consistent distributed system — Google's Chubby, Spanner, and Megastore, Kubernetes' etcd (via Raft, a descendant), Cassandra's lightweight transactions — is running some flavor of Paxos underneath. In practice you rarely implement raw Paxos: **Multi-Paxos** batches decisions to amortize the prepare phase, and **Raft** is the readable rewrite most people actually build with today.

---
_Logged on 2026-08-06._
