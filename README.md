# Plain Primitives

Plain Primitives is a collection of minimal, deterministic building blocks
for reliable software systems.

These primitives provide signals, not decisions.
They are intentionally plain, opinion-light, and context-free.

## What this project is not

Plain Primitives is not:

- a framework
- a convenience utility library
- a collection of business helpers
- a policy or decision engine

If something decides _what should happen_,
it does not belong here.

## Philosophy

### Primitives give signals, not decisions

A primitive only answers questions like:

- how many times did this happen?
- has this occurred before?
- is this value unique?
- did this happen within a given window?

It never answers _what should be done next_.

### Plain by design

"Plain" means:

- no hidden state
- no background behavior
- no smart defaults
- no implicit policy

All important inputs are explicit.
All behavior is predictable.

### Deterministic behavior

Given the same inputs, a primitive should always
produce the same output.

Primitives do not:

- guess
- infer intent
- adapt behavior automatically

### Language-agnostic concepts

This project starts with a JavaScript/TypeScript implementation,
but the concepts are designed to be portable across languages.

The code is a reference implementation,
not a language lock-in.

## What belongs here

Examples of valid primitives include:

- time-based counters
- idempotency guards
- soft coordination primitives
- deduplication signals

These are patterns that appear repeatedly across systems
and are often re-implemented inconsistently.

## What does not belong here

This project will not include:

- business rules
- request handling logic
- retry or fallback policies
- framework abstractions

## Goal

The goal of Plain Primitives is not to be clever.

The goal is to provide small, trustworthy building blocks
that remain understandable under pressure.

## Project status

This project is intentionally grown slowly.

New primitives are added only when they prove to be:

- broadly reusable
- context-free
- simpler than common ad-hoc alternatives
