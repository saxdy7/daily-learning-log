# Argon2, bcrypt, scrypt
_Category: security_

Password hashing is not the same problem as general-purpose hashing. You _want_ it to be slow, and you want it to resist attackers who show up with a rack of GPUs or a custom ASIC. Argon2, bcrypt, and scrypt are the three algorithms most people actually reach for, and each one makes a different bet about which resource is expensive to attack with.

## What it is

bcrypt (1999) is CPU-bound and uses a tunable cost factor that doubles the work per increment. It's battle-tested and everywhere, but its 72-byte input limit and lack of a memory-hardness knob are showing their age against modern hardware.

scrypt (2009) added memory-hardness: it forces the attacker to allocate a large working buffer, which makes GPU and ASIC attacks much more expensive per guess. You tune N (memory), r (block size), and p (parallelism).

Argon2 (2015, winner of the Password Hashing Competition) is the current default recommendation. Argon2id — the hybrid variant — combines side-channel resistance from Argon2i with the GPU resistance of Argon2d, and gives you separate memory, time, and parallelism knobs.

## When it matters / one example

For a typical web app storing user passwords in 2026, OWASP suggests Argon2id with roughly 19 MiB of memory, 2 iterations, 1 lane — or bcrypt with cost 12 if your language's Argon2 binding is painful. What you should _not_ do is reach for SHA-256, MD5, or a plain HMAC. Those hash in nanoseconds; an attacker with a rented GPU cluster will burn through a leaked table of them in hours.

---
_Logged on 2026-08-08._
