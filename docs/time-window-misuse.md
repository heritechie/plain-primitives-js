# Time Window Counter — Misuse Scenarios

This document describes common ways the Time Window Counter
can be misused, even when the usage appears reasonable.

These scenarios exist to protect the primitive boundary
and to clarify where certain logic must live instead.

---

## Misuse #1: Using the primitive as a rate limiter

### Pattern

Using the Time Window Counter to directly allow or block requests.

```text
signal = counter.record({ key, now, window, threshold })

if (signal.threshold_exceeded) {
  rejectRequest()
}
```

### Why this is a misuse

While technically possible, this couples the primitive
to request-handling semantics.

The primitive does not know what a request is,
nor should it be responsible for controlling execution flow.

### Where this logic belongs

In a policy or application layer that consumes the signal
and decides how to act on it.

## Misuse #2: Expecting retry or backoff guidance

### Pattern

Using window timing information to tell callers
when they should retry an operation.

```text
retryAfter = signal.window.end - now
```

### Why this is a misuse

This turns a factual signal into guidance.

Retry timing, backoff strategies, and pacing decisions
are policy concerns and depend on domain context.

### Where this logic belongs

In a retry, scheduling, or orchestration layer
that interprets signals according to intent.

## Misuse #3: Treating count as a severity or score

### Pattern

Interpreting the counter value as a measure of severity.

```text
severity = signal.count
```

### Why this is a misuse

The counter reports frequency, not importance.

Severity implies interpretation, prioritization, or domain-specific meaning, which the primitive cannot infer.

### Where this logic belongs

In domain logic that understands what higher frequency means for a specific system or use case.

---

## Misuse #4: Assuming fairness or equal distribution

### Pattern

Expecting the counter to ensure fair usage across keys, users, or actors.

### Why this is a misuse

Fairness is a policy decision that requires coordination, quotas, or prioritization strategies.

The primitive provides no such guarantees.

### Where this logic belongs

In a higher-level coordination or quota management system.

---

### Misuse #5: Expecting sliding or adaptive window behavior

### Pattern

Assuming counts decay smoothly over time or adapt based on past behavior.

### Why this is a misuse

The Time Window Counter uses fixed window semantics.

Sliding or adaptive windows introduce implicit policy and additional complexity.

### Where this logic belongs

In a higher-level abstraction built on top of the fixed-window primitive.

---

### Misuse #6: Reading state without recording an event

### Pattern

Attempting to query the current count without recording an occurrence.

```text
counter.peek(key)
```

### Why this is a misuse

Separating observation from recording encourages race conditions and split decision logic.

The primitive is intentionally event-driven.

### Where this logic belongs

In application logic that tracks state explicitly or uses cached signals appropriately.

---

### Guiding principle

If a usage pattern makes the primitive responsible for:

- deciding what should happen
- guiding caller behavior
- interpreting domain meaning
- enforcing policy

then that usage is outside the scope of this primitive.

---

### Summary

Misuse scenarios are not errors or edge cases.

They are examples of misplaced responsibility.

The Time Window Counter remains simple and reusable by refusing to absorb intent, policy, or interpretation.
