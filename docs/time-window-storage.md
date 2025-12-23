# Time Window Counter — Storage Contract

This document defines the minimum storage capabilities required to support the Time Window Counter primitive.

It intentionally avoids implementation details, technology choices, and optimization strategies.

---

## Purpose

The storage contract exists to answer a single question:

> What must a storage layer be able to do
> so that a Time Window Counter can function correctly?

If a storage implementation satisfies this contract, the primitive can rely on it safely.

---

## Scope

This contract applies only to:

- fixed time windows
- per-key counting
- deterministic behavior
- foreground (request-driven) operations

It explicitly does not address:

- scheduling
- background processing
- retries or backoff
- distributed coordination guarantees
- fairness or quotas

---

## Conceptual entities

This contract refers to the following conceptual entities. They are not prescriptive data models.

### Key

An arbitrary identifier representing “something” being counted.

- format is irrelevant (string, bytes, hash, etc.)
- interpretation is out of scope

---

### Window

A fixed interval of time defined by:

- a start timestamp
- a duration

Window boundaries must be explicit and deterministic.

---

### Counter State

A numeric value associated with a specific `(key, window)` pair.

This state represents the count of occurrences within that window.

---

## Required capabilities (MUST)

These capabilities are non-negotiable.

---

### 1. Atomic increment

The storage must support incrementing the counter state for a given `(key, window)` atomically.

This ensures:

- correctness under concurrent access
- predictable counts

The exact atomicity guarantees depend on the storage system, but increments must not be lost or partially applied.

---

### 2. Window-scoped isolation

The storage must isolate counter state by window.

When the active window changes:

- a new counter state must be observed
- previous window state must not affect the new window

The storage does not need to understand window semantics, but it must support separation of state across windows.

---

### 3. Time-bound expiration

The storage must support expiring counter state when a window ends.

This expiration may be:

- approximate
- best-effort

However, state must not persist beyond the window’s lifetime in a way that affects correctness.

---

### 4. Read current counter value

The storage must allow reading
the current counter value for a given `(key, window)`.

This read operation is required to expose factual signals such as the current count or derived threshold evaluation.

Historical reads are explicitly out of scope.

---

## Optional capabilities (SHOULD)

These capabilities improve ergonomics but are not required for correctness.

---

### 5. Expiration metadata access

The ability to retrieve:

- the window end time
- or remaining time until expiration

This enables exposure of time-related signals without additional computation.

If unavailable, the primitive may omit such signals or compute them externally.

---

### 6. Lazy or idempotent initialization

The ability to create counter state on demand without race conditions.

This is an optimization concern and must not be required for correctness.

---

## Explicit non-requirements

The storage must not be required to:

- make decisions
- enforce thresholds
- block or reject operations
- apply rate limiting or throttling
- perform sliding or adaptive window logic
- store historical or analytical data
- guarantee fairness or ordering

Any requirement in this list indicates a higher-level abstraction, not a primitive concern.

---

## Failure semantics

This contract does not define:

- retry behavior
- fallback strategies
- error classification

If storage operations fail, the primitive may surface the failure, but must not interpret it or apply policy.

---

## Determinism expectations

Given:

- the same sequence of increment operations
- the same window boundaries
- the same storage guarantees

The observed counter values and signals must be consistent.

The primitive must not attempt to smooth, correct, or compensate for storage behavior.

---

## Portability check

This contract should be satisfiable by:

- an in-memory key-value store
- a Redis-like system with TTL
- a database with basic atomic update and expiration support

If a storage system cannot satisfy this contract, it is not suitable for hosting this primitive.

---

## Summary

The Time Window Counter storage contract defines the minimum guarantees required to support deterministic, fixed-window counting.

It intentionally avoids policy, optimization, and infrastructure assumptions.

Storage is a detail. Correctness is the contract.
