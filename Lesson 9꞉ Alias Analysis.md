---
title: 'Lesson 9: Alias Analysis'
created: '2021-04-28T04:14:33.095Z'
modified: '2021-04-28T04:39:57.940Z'
---

# Lesson 9: Alias Analysis

Almost every programming language in use uses pointers or references.

Bril has a memory extension for pointers. There's an `alloc` and `store` extension.

```
vals: ptr<int> = alloc n;
```

Tricky C code for pointer analysis:

```
bool foo(char *a, char *b) {
  *a = 'a';
  *b = 'b';
  return *a == 'a';
}
```

This func appears to always return true, but a and b could actually point to the same location in memory. Theoretically `*b = 'b';` could be dead code, but we can't delete it.

We use pointer analysis to:
- eliminate dead stores
- propagate stores through to subsequent loads
- code motion (loop invariant code motion) when other stores are present
- decide whether code is safe to parallelize

Things that are "easy" to do on stack variables are hard to do on heap variables (pointers).

## Alias Analysis: Problem Statement

For every program point, and for every pair of pointer-typed variables `p` and `q`, do `p` and `q` point to the same memory location at that point in time?

Problems:
- Undecidable (halting problem)
- Answer could be "sometimes"
- Inherently inter-procedural

Goal is to classify pairs of variables into three categories:
- _Must_ Alias
- _Must Not_ Alias
- _Might_ Alias?

We generally ask "may alias" queries. Our goal is to have 100% likelihood if they do not alias.

## Data Flow Analysis for Alias Analysis

Direction: Forward
Domain: {`x` -> `{locations}`}, x is some variable
Initial Value: {`x` -> `{}`}, for all x
Merge: Union every variable
Transfer:
- `x = const K`: map[x] = {}  (constants)
- `x = id y`: map[x] = map[y] (copies)
- `x = alloc y`: map[x] = {New Location} (new memory)

A single memory location is associated with every allocation site. We need to move away from runtime semantics (allocation on every loop) and make something that's static.

## Heap Models:

What is a "memory location"?

Simple heap model: one location per _static allocation site_.
- When we have two different pointers that come from the same allocation site at runtime, they will be considered the same location in memory. Our analysis is conservative, and since we're doing a "may alias analysis" this is safe and valid, though not necessarily perfect.
- This lets us actually complete the analysis.

For realistic languages, offset allocation sites with array offsets & field names. Each offset and field is its own memory location.

## Context-Sensitive Alias Analysis

Just a little bit of context is enough to determine a lot of `must` and `must not` alias queries. Our goal is precision, but adding context adds a lot of cost.

In general, a scalable alias analysis pass is an open research problem that has no good solution yet. Alias analysis is key for programs with pointers, and is key for optimizing programs that do anything with memory.


