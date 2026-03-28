---
name: write-clear-code
description: Apply a human-first coding style immediately before writing or modifying source code. Use this skill when implementing code changes to keep logic easy to parse.
---

# Human-First Coding Style

## Apply Before Writing Code

- Extract complex conditional expressions into intermediate variables with meaningful names.
- Prefer early returns to reduce nesting and keep the happy path obvious.
- Keep interfaces simple; avoid creating thin wrappers/factories with little behavior.
- Prefer composition over inheritance unless inheritance is clearly simpler in this context.
- Use the smallest language feature set needed for readability.
- Accept small duplication when abstraction would increase mental overhead.
- Write comments for high-level context or WHY; avoid line-by-line WHAT comments.

## Decision Order

1. Flatten control flow (introduce guard clauses).
2. Name important conditions (especially boolean logic).
3. Remove unnecessary abstraction layers.
4. Keep only comments that explain intent, tradeoffs, or constraints.

## Example

Bad:

```ts
if (
  user &&
  user.isActive &&
  (user.role === "admin" || user.role === "owner") &&
  !user.isBanned
) {
  startSession(user);
}
```

Better:

```ts
const hasUser = user != null;
const isActive = hasUser && user.isActive;
const canManage = hasUser && (user.role === "admin" || user.role === "owner");
const isAllowed = hasUser && !user.isBanned;

if (!isAllowed || !isActive || !canManage) return;
startSession(user);
```
