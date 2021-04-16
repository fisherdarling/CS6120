---
title: 'Lesson 7: Loop Optimization'
created: '2021-04-16T03:52:16.643Z'
modified: '2021-04-16T04:13:25.831Z'
---

# Lesson 7: Loop Optimization

Optimizations generally occur on Natural Loops. There's a single entrance block and no loop "cross-over".

Recap: Loop-Invariant Code Motion
- Some instructions in a loop can be moved outside of the loop if they do not depend on the loop.

## Induction Variable Elimination (IVE):

A loop on `f(a[i])` has to do an addition and multiplication for a load: `a + i * stride`. We can optimize this by just having a variable that we increment by `stride` on every iteration.

`for(a_i = a; a < a + 100 * stride; a += stride)`

Moves more "natural" loops with subscripting to pointer bumping.

IVE Steps:
1. Find the _basic_ induction variables.
- Look for `i += e` (we add some loop-invariant expression `e` to a loop variable).
- SSA Makes this harder, we have to traverse phi-nodes to determine if the same variable is being incremented. Non SSA makes this pretty trivial (use that redefines).
2. Find _derived_ induction variables.
- `j = c * i + d`: `c` and `d` are loop-invariant, then `j` is a "derived" loop-induction variable.
3. Replace the derived induction variables
- BASIC: `i += e`
- DERIVED: `j = c * i + d`
Actual Modification
- PRE-HEADER:
  `j = d`
- IN LOOP:
  `j += c * e` // `c * e` should be put in the loop header.
4. Clean up with copy propogation, DCE. Very common that we eliminate the old induction variable.

## Loop Unswitching

```
for (let i = 0; i < 100; i++) {
  if (c) { <- Loop Invariant
    f();
  } else {
    g();
  }
}
```

We can "flip" the order of the if and the for. Move the for loops within the branches. The condition _must_ be loop invariant.

## Loop Fusion

Two loops right next to eachother can sometimes be placed together. We have two for loops with the same looping structure.

Writes from the second loop _must not_ impact reads from the first loop. You need to be able to interleave loop bodies.

```
for (let i = 0; i < 100; ++i) {
  b[i] = f(a[i]);
}
for (let i = 0; i < 100; ++i) {
  c[i] = g(b[i]);
}
```

could potentially be converted to:

```
for (let i = 0; i < 100; ++i) {
  c[i] = g(f(a[i]));
}
```

This optimization happens a lot with machine learning compilers. Rather than buffering an entire fully connected matrix operation and then doing some sort of RELU layer, we can essentially "stream" it.

The rest of the class is going to move on to other parts of language implementation, not just "function" level optimizations.
