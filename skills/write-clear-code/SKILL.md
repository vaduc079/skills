---
name: write-clear-code
description: Coding principles to apply immediately before writing or modifying source code. Use when implementing a code change, adding a function or helper, handling errors or unknown input, or keeping logic easy to read.
---

# Write code

Write code a person can read top to bottom without holding state in their head.

**Why:** Code is read far more than it is written. Extra helpers, repeated checks, and defensive branches each cost the next reader time and hide the real logic.

## Structure

- Keep logic in one function unless it is composable or reusable.
- Do not extract a single-use helper. Inline it at the call site unless it is reused, hides a genuinely complex boundary, or has a clear independent name that improves the caller.
- Prefer early returns to nesting; keep the happy path obvious.
- Name complex conditions with intermediate variables.
- Keep interfaces simple; skip thin wrappers and factories with little behavior.
- Prefer composition over inheritance unless inheritance is clearly simpler here.
- Use the smallest language feature set that reads well.
- Accept small duplication when an abstraction would add mental overhead.
- Comment the WHY, non-obvious constraints, and surprising behavior; never obvious assignments or control flow.

## Complex logic

When a function has several validation branches or supporting details, make the main function read as the happy path and move the details into small helpers below it. Extract only when the helper names a real concept.

```ts
export function loadThing(input: unknown) {
  const config = requireConfig(input)
  const metadata = readMetadata(input)
  return createThing({ config, metadata })
}

function requireConfig(input: unknown) {
  ...
}
```

- Keep helpers next to the code they support, below the main export when that reads better.
- Do not split simple expressions into many single-use helpers.

## Validation and errors

- Validate unknown values once, at the boundary that owns them. Pass typed values inward.
- Do not revalidate what a schema, constructor, or internal type already guarantees (no repeated `typeof value === "object"` or property-existence checks).
- Avoid `try/catch` where possible.
- Before adding complexity for a speculative or vanishingly unlikely race or security edge case, tell the user the concrete failure mode, its likelihood, and the complexity cost, and get their buy-in. Do not silently widen scope for theoretical robustness.

## TypeScript

- Do not use `any`.
- Rely on type inference. Add explicit annotations or interfaces only for exports or clarity.
- Prefer `flatMap`, `filter`, and `map` over `for` loops. Use a type guard on `filter` so inference holds downstream.

## Example

Bad:

```ts
if (user && user.isActive && (user.role === "admin" || user.role === "owner") && !user.isBanned) {
  startSession(user);
}
```

Better:

```ts
const canManage = user.role === "admin" || user.role === "owner";
if (!user.isActive || user.isBanned || !canManage) return;
startSession(user);
```

**Stop:** Do not add a helper, a check, or a `try/catch` until you can name the concrete caller or failure it serves.
