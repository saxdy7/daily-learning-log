# Adapter Pattern

_Category: engineering_

Most real codebases die not from bad logic but from bad seams — the places where your code touches someone else's. The Adapter pattern is the cheapest way to keep those seams from leaking.

## What it is

An adapter is a thin class that wraps an incompatible interface and re-exposes it as the interface your code already expects. You define the interface *you* want (`PaymentGateway.charge(amount, currency)`), then write a small class per vendor that translates your call into whatever the vendor's SDK actually wants — different method names, argument order, units, error types. Your business logic depends only on your own interface; the adapter absorbs the mismatch.

The key discipline is direction: the interface belongs to the consumer, not the provider. If you let the vendor's shape define your interface, you haven't adapted anything — you've just added a layer.

## When it matters / one example

Say you integrate Stripe and your service calls `stripe.PaymentIntent.create(amount=cents, currency="usd")` in fifteen places. Two years later you add Razorpay for Indian customers, which takes paise, uses a different capture flow, and throws its own exception hierarchy. Without an adapter, you're editing fifteen call sites and sprinkling `if provider == ...` through the codebase.

With one, you write `StripeAdapter` and `RazorpayAdapter`, both implementing `charge(Money) -> ChargeResult`, both normalizing currency units and mapping vendor exceptions onto your own `PaymentDeclined` / `PaymentUnavailable`. Business code never changes. As a bonus, tests get a `FakeAdapter` for free — no network, no sandbox keys.

The gotcha: adapters should translate, not decide. The moment one starts making business decisions (retry policy, fallback provider), it stops being an adapter and needs to move up a layer.

---
_Logged on 2026-08-14._
