---
tags: [Compilers]
title: 'Lesson 3: Local Analysis & Optimization'
created: '2021-03-20T20:04:33.447Z'
modified: '2021-04-15T01:59:56.695Z'
---

# Lesson 3: Local Analysis & Optimization

Few different categories of optimizations:

1. Local: Within a single basic block (no control flow).
2. Global: Work on an entire function.
3. Interprocedural (multiple functions)

## Dead Code Elimination:

Delete any instruction that assigns to a variable that is never used, as long as that instruction has no side-effects.

```
used = {}
for instr in func:
  used += instr.args

for instr in func:
  if instr.dest and instr.dest not in used:
    delete instr
```

Redundant store elimination, should be done iteratively until it converges:
```
last_defs = {} <- var -> instr
for instr in block:
  # check for uses
  last_def -= insr.args
  if instr.dest in last_def:
    delete last_def[instr.dest]
```

## Local Value Numbering (LVN):

Three main forms of redundancy:
1. Dead Code Eliminiation (DCE)
2. Copy Propogation
  Several instructions that reuse same value, just use the value directly.
3. Common Subexpression Elimination (CSE)
  Same computation done multiple times, just reuse the computation.

Conflation between values and variables. E.g., one variable with two values.

The idea is that each value is tracked throughout the program, and numbers are assigned.

Table that tracks each instruction in the name:

| \# | Value | Canonical Variable Name |
| --------- | ----------- | ----------- |
| 1 |           | x
| 2 | #1 + #1   | a        |
| 3 | #2 * #2   | b        |

Step through the code, keeping track of the value number for each variable at the given point in time.
We essentially write an interpreter that instead of holding concrete runtime values, it holds variables
from the table above ^. As we go over op codes, we create a tuple of its args and its operations.

For example `sum1: int = add a b` is transformed into `(add, 1, 2)` where 1 and 2 are the row ids for `a` and `b`. As we traverse opcodes, we rewrite them to use the canonical variable names referenced in the table for there numeric operands.

For the above `sum1: int = add a b`, there would be no change.
But for a `sum2: int = add a b` on the next line, the tuple form would be `(add, 1, 2)` which already exists. It would then be rewritten to `sum2: int = id sum1`. Essentially copying the previous memory location.

A following op `prod: int = mul sum1 sum2` would be written as `(mul, 3, 3)` (where 3 is the sum2 opcode). It would then be rewritten as `prod: int = mul sum1 sum1`, reusing value #3.

### Canonicalization of Tuples

For operators where order does not matter, we sort the operands and use that as the tuple. This allows `(add, 2, 1) = (add, 1, 2)`.

## Constant Folding

The LVN algorithm and table lets you remove redundant code and "fold" constants. Basically do any constant calculation in the compiler, and reduce the number of actual operations.

```
a: int = const 4;
b: int = const 2;
sum1: int = add a b;
sum2: int = add a b;
prod: int = mul sum1 sum2;

print prod;

# Is optimized to:

print 36;
```











