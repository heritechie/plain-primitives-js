# Time Window Counter

This document defines the meaning, scope, and boundaries
of the Time Window Counter primitive.

It is intentionally non-technical and implementation-agnostic.

---

## Definition

A Time Window Counter answers a single question:

> How many times did a key occur within a fixed time window?

It provides a factual signal about frequency over time,
without interpreting what that frequency means.

---

## Why this is a primitive

A time window counter operates at the lowest useful level
of temporal reasoning.

It does not know:

- what the event represents
- why the event happened
- whether the event is good or bad
- what should happen next

It only observes and reports a fact:
the number of occurrences within a bounded time window.

Because it is context-free and decision-free,
it can be reused across many domains without modification.

---

## Why this is not a rate limiter

A rate limiter enforces policy.

It decides whether something should be:

- allowed
- blocked
- delayed
- rejected

A Time Window Counter does none of these things.

It never blocks execution.
It never throws errors for normal conditions.
It never enforces fairness or priority.
It never applies backoff or retry logic.

Those behaviors require intent and policy,
which must live outside the primitive.

---

## Fixed window semantics

This primitive uses a fixed time window.

A fixed window means:

- the window has a clear start and end
- counts reset when the window expires
- behavior is predictable and easy to reason about

Sliding or adaptive windows are intentionally excluded
because they introduce additional complexity and implicit policy.

If sliding behavior is needed,
it should be built on top of this primitive,
not embedded within it.

---

## Signals provided

The Time Window Counter may provide signals such as:

- current count within the window
- whether a threshold has been reached or exceeded
- remaining time until the window expires

These signals are informational only.

They do not imply what action should be taken.

---

## Non-goals

This primitive must never:

- block or allow requests
- return domain-specific errors
- encode business or policy language
- make assumptions about fairness or abuse
- hide time or state implicitly
- adapt behavior based on past outcomes

If any of the above are required,
they belong in a higher-level abstraction.

---

## Determinism

Given the same inputs and the same underlying storage behavior,
this primitive must always produce the same outputs.

There must be no hidden timers,
background processes,
or implicit state transitions.

All time boundaries must be explicit.

---

## Language and runtime neutrality

Although the reference implementation will be written
in JavaScript/TypeScript,
the concept of a Time Window Counter is language-agnostic.

It should be possible to reimplement this primitive
in another language without reinterpretation of its meaning.

---

## Relationship to higher-level logic

This primitive is meant to be composed with
application logic, policies, or abstractions.

For example:

- a service may decide to log when a threshold is exceeded
- a policy may decide to block after repeated violations
- an alerting system may trigger notifications

Those decisions are intentionally out of scope.

---

## Summary

The Time Window Counter is a foundational primitive
for temporal frequency observation.

It is intentionally simple, explicit, and boring.

Its value comes from what it refuses to do,
not from how much it does.
