# TypeScript Generics

_Category: languages_

Generics are how you write a function or type once and have it stay type-safe across every type you feed it — instead of reaching for `any` and losing the compiler's help entirely.

## What it is

A generic introduces a type *parameter*: a placeholder the caller fills in, the same way a function parameter is a placeholder for a value. `function first<T>(items: T[]): T | undefined` says "whatever element type the array has, that's what comes back." Call it with `string[]` and TypeScript infers `T = string`; the return type is `string | undefined` with no annotation needed at the call site. You can constrain the parameter with `extends` — `<T extends { id: string }>` accepts only objects that have an `id`, which lets you safely read `item.id` inside the function body. Defaults work too: `<T = unknown>`.

## When it matters / one example

The classic case is a wrapper that must preserve its input's shape. An API client typed as `fetchJson(url: string): Promise<any>` poisons everything downstream — every property access afterwards is unchecked. Typed as `fetchJson<T>(url: string): Promise<T>`, the caller writes `await fetchJson<User>('/me')` and gets real autocomplete and real errors on typos.

The common gotcha: reaching for generics when a plain union would do. If a function only ever handles `string | number`, a type parameter adds ceremony without adding safety. A useful rule of thumb — if the type parameter appears only once in the signature, it probably shouldn't be a generic at all.

---
_Logged on 2026-08-15._
