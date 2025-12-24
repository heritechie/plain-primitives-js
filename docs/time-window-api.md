# Time Window Counter — API Shape

This document defines the conceptual API shape for the Time Window Counter primitive.

It describes how consumers interact with the primitive, without specifying implementation details, storage choices, or language-specific constructs.

---

## Purpose

The API exists to provide a minimal and explicit way to:

- record occurrences of an event
- observe factual signals about frequency within a fixed time window

The API must not encode intent, policy, or decision-making logic.

---

## Design principles

The API is designed according to the following principles:

- **Explicitness over convenience**  
  All relevant inputs must be provided explicitly.

- **Signals over decisions**  
  The API returns factual observations, not actions or guidance.

- **Determinism**  
  Given the same inputs and storage guarantees,
  the API must always produce the same outputs.

- **Single responsibility**  
  The API records events and reports state.
  It does nothing else.

---

## Core operation

The Time Window Counter exposes a single conceptual operation:

> **record**

This operation represents the act of recording one occurrence of an event for a given key and time window.

There are no separate read-only or control operations.

---

## Input shape

The `record` operation accepts the following inputs.

All inputs are required unless explicitly stated otherwise.

---

### key

An opaque identifier representing what is being counted.

- the primitive does not interpret the key
- the key has no semantic meaning at this level
- validation beyond structural correctness is out of scope

---

### now

An explicit timestamp representing the current time.

- the primitive must not read system time implicitly
- the caller is responsible for providing this value
- this enables deterministic behavior and testability

If `now` falls outside the defined window boundaries, the behavior must be explicitly defined or rejected.

---

### window

A fixed time window definition.

The window must be defined explicitly and deterministically, for example by providing:

- a start timestamp
- a duration

The primitive must not infer or shift window boundaries implicitly.

---

### threshold (optional)

A numeric value provided by the caller for evaluating a derived condition.

- the primitive does not choose thresholds
- the primitive does not store thresholds
- the threshold is evaluated only for the current operation

If omitted, no threshold-related signal is produced.

---

## Output shape

The `record` operation returns a set of factual signals describing the current state of the counter within the window.

---

### count

The number of occurrences recorded for the given `(key, window)` after the current operation.

This value is purely observational.

---

### window information

The API may return explicit window boundary information, such as:

- window start time
- window end time
- or remaining time until window expiration

The exact representation is not prescribed.

---

### threshold_exceeded (optional, derived)

A boolean signal indicating whether:

`count >= threshold`

This signal is derived entirely from caller-provided input and current counter state.

It does not imply any required action.

---

## Explicitly excluded operations

The API must not expose operations such as:

- allow / deny checks
- reset or clear
- peek or read-without-record
- retry or backoff hints
- capacity or quota queries
- sliding or adaptive window behavior

If such behaviors are required,
they must be implemented in a higher-level abstraction.

---

## Error surface

The API may surface errors only for:

- invalid or inconsistent input
- storage-level failures
- explicit violations of time or window constraints

The API must not surface errors as a result of:

- high counts
- threshold exceedance
- perceived misuse or abuse patterns

All error interpretation and handling is the responsibility of the calling layer.

---

## Determinism guarantees

Given:

- the same sequence of `record` operations
- identical input parameters
- consistent storage guarantees

The API must return consistent outputs.

The API must not:

- rely on hidden state
- adapt behavior based on historical patterns
- compensate for storage inconsistencies

---

## Composability

This API is designed to be composed with
application logic, policies, or abstractions.

For example, higher-level logic may:

- log when thresholds are exceeded
- trigger alerts or notifications
- enforce access or rate policies

Those behaviors are intentionally out of scope.

---

## Language and runtime neutrality

This API shape is language-agnostic.

The reference implementation may be written in a specific language, but the conceptual contract must remain consistent across runtimes and ecosystems.

---

## Summary

The Time Window Counter API provides a minimal, explicit interface for recording events and observing frequency within a fixed time window.

Its value lies in what it refuses to provide:
decisions, policy, and hidden behavior.
