# Architecture Health Checklist

Companion reference for the code-review skill. Use on `--audit` or structural changes. Each dimension lists code-observable signals only.

---

## Dimension 1: Modularity & Boundary Clarity

Signals:

- Circular imports between modules.
- Module directly reads or writes another module's data store instead of using the owning API.
- Business logic in HTTP route/controller code.
- Cross-module shortcuts introduced to avoid existing service boundaries.
- A module importing from many unrelated modules; treat this as a smell, not a finding by itself.

Severity guidance: HIGH when changed code moves business logic into the HTTP layer, bypasses ownership boundaries, or creates circular dependencies.

---

## Dimension 2: Maintainability & Cognitive Load

Signals:

- Functions or files are large because they mix unrelated responsibilities.
- Deep nesting makes error handling or state transitions hard to follow.
- One function mixes input parsing, validation, persistence, notification, and response shaping.
- Similar branches or code blocks are repeated instead of sharing a clear helper.
- Refactor and behavior changes are mixed so the actual behavior delta is hard to review.

Severity guidance: report size or nesting only when it materially affects comprehension, testability, or change risk.

---

## Dimension 3: Extensibility

Signals:

- Repeated `if type == ...` / `switch` chains on the same axis with several branches.
- Hardcoded strategy selection where a registry, map, or dependency injection pattern already exists in the codebase.
- New behavior requires modifying central business logic instead of adding a small isolated implementation.

Severity guidance: MEDIUM for localized extensibility issues; HIGH when the branch chain is in a critical business path or is already causing bugs.

---

## Dimension 4: Testability

Signals:

- `new ConcreteService()` or direct client construction inside business logic that should be mockable.
- Direct imports of singleton DB, queue, or HTTP clients inside code that is otherwise unit-testable.
- Hidden time, randomness, environment, or network dependencies without injection or clear isolation.
- No test seam for behavior that changed.

Severity guidance: HIGH for business logic that cannot be tested without real external I/O.

---

## Dimension 5: Resilience & Observability

Signals:

- External HTTP, DB, or queue calls without timeouts.
- Swallowed exceptions such as `except: pass` or `.catch(() => {})`.
- Error logs lack useful context such as operation, resource id, user id, or request id.
- Critical external dependencies have no retry, fallback, idempotency, or failure-handling story where the project normally expects one.

Severity guidance: HIGH for swallowed exceptions or missing timeouts on external I/O; MEDIUM for missing useful error context.

---

## Boundary Contracts

Signals:

- Caller assumptions are not enforced before crossing a layer or module boundary.
- Callee requirements are implicit and easy for callers to violate.
- A state name, helper name, or boolean flag implies a stronger guarantee than the code actually checks.
- Downstream code consumes outputs or side effects that may be missing, stale, partial, or failed.
- Failure paths leave persisted state, in-memory state, or observable events inconsistent.

Severity guidance: HIGH when a violated contract can cause production failure, data inconsistency, authorization bypass, or unrecoverable user-visible errors.

---

## Severity Summary

| Signal                                              | Typical severity |
| --------------------------------------------------- | ---------------- |
| Circular imports introduced by the change           | HIGH             |
| Business logic moved into HTTP/controller layer     | HIGH             |
| Untestable hard dependency in business logic        | HIGH             |
| Swallowed exception on external I/O                 | HIGH             |
| Missing timeout on external call                    | HIGH             |
| Violated boundary contract with production impact   | HIGH             |
| Mixed refactor plus behavior change that hides risk | MEDIUM           |
| Repeated branch chain on a growing behavior axis    | MEDIUM           |
| Size or nesting that materially hurts reviewability | MEDIUM           |
| Missing useful context on error paths               | MEDIUM           |
