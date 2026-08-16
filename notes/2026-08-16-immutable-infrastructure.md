# Immutable Infrastructure

_Category: devops_

The fastest way to stop debugging "works on that server but not this one" is to stop editing servers at all. Immutable infrastructure treats a running machine as disposable output, not as something you maintain.

## What it is

Under the traditional (mutable) model, you provision a server once and then keep changing it in place — SSH in, run `apt upgrade`, patch a config, restart a service. Over months, every server drifts down its own path, and no two are quite alike. That's configuration drift, and it's why a deploy can succeed on nine boxes and fail on the tenth.

Immutable infrastructure inverts this. You bake a versioned artifact — a machine image, a container image — that contains the OS packages, runtime, and application at a known state. To change anything, even a one-line config tweak, you build a new image and replace the running instances with fresh ones. Nothing is ever patched live. The server is cattle, not a pet.

## When it matters / one example

The payoff is that your artifact is the thing you tested. If image `v42` passed staging, the production instances running `v42` are byte-identical — no drift between them. Rollback becomes trivial: redeploy `v41` instead of trying to reverse a half-applied change.

The obvious gotcha is state. Databases, uploaded files, and logs can't be thrown away with the instance, so they must live outside it — in managed storage, a volume, or a log shipper. Teams that adopt immutability without first externalising state end up deleting data on their first rolling replacement. Sort out where state lives before you make the servers disposable.

---
_Logged on 2026-08-16._
