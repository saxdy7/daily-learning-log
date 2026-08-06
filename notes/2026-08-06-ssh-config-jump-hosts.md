# SSH Config and Jump Hosts
_Category: tooling_

If you're typing full `ssh user@host -p 2222 -i ~/.ssh/xyz` commands every day, you're doing SSH wrong. `~/.ssh/config` turns that mess into one-word aliases and makes reaching machines behind bastions almost invisible.

## What it is

The SSH client reads `~/.ssh/config` before every connection. Each `Host <alias>` block sets defaults — `HostName`, `User`, `Port`, `IdentityFile`, `ForwardAgent`, and dozens of others — that apply when you run `ssh <alias>`. Wildcards work too: a `Host *.internal` block can set common defaults for a whole environment. The key entry for multi-hop networks is `ProxyJump` (`-J` on the command line). It tells SSH to first connect to a bastion host, then tunnel the real connection through it, all in one command with no manual `ssh` inside `ssh`.

## When it matters / one example

Say your production database sits in a private VPC reachable only through a bastion. With config:

```
Host bastion
  HostName bastion.example.com
  User ops
  IdentityFile ~/.ssh/ops_ed25519

Host db-prod
  HostName 10.0.4.17
  User postgres
  ProxyJump bastion
```

Now `ssh db-prod` just works, and so does `scp file db-prod:/tmp/` and `rsync` — because they all speak SSH under the hood. Bonus: `ProxyJump` chains, so `ProxyJump bastion1,bastion2` traverses two hops. This beats the old `ProxyCommand ssh -W %h:%p bastion` idiom, which required a shell on the jump host; `ProxyJump` only needs SSH forwarding, which is safer to allow.

---
_Logged on 2026-08-06._
