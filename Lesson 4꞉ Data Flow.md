---
tags: [Compilers]
title: 'Lesson 4: Data Flow'
created: '2021-04-15T02:01:22.904Z'
modified: '2021-04-15T03:02:59.618Z'
---

# Lesson 4: Data Flow

This lesson is mainly about global analysis. LVN cannot be used for constant propagation across _arbitrary_ control flow graphs.

**Reaching Definitions Problem**:

Vocab:
- **use**: An instruction uses all of its arguments
- **definition**: An instruction defines the variable it writes to
- **available**: Definitions that reach a given program point are available there
- **kill**: Any definition kills all of the currently available definitions

For every definition and every use,
determine whether the definition reaches the use.

## Data Flow Framework

1. Figure out the thing you want to know at the entry and exit of a block.
2. Write an equation for every block relating the entry to the exit.
3. Add equalities according to edges in the CFG.
4. Solve the system of equations (using an off-the-shelf solver).

Each basic block has an `in` and `out` variable. Distinct variables for each block. The equations relate the in for one block to the out of that block: 

$f(in_{b1}) = out_{b1}$

Combining the out variables for BBs that point to the same BB requires a `merge` function to combine the output of two different blocks.

Standard "Ingredients":

- `in_b1 = ____________` (entry)
- For all blocks b, `out_b(in_b) = ____________`
- `merge(out_a, out_b, ...) = ____________`

Reaching Definitions Answers:

- `in_b1 = ARGS` (entry)
- For all blocks b, `out_b(in_b) = DEF_b union (in_b without KILLS_b)`
- `merge(out_a, out_b, ...) = Big U union`

## Worklist Algorithm for Data Flow:

This algorithm is guaranteed to converge.

Given a CFG, initial value, `merge` and `transfer`:

```
in[entry] = args
out[*] = args

worklist = all blocks
while worklist is not empty:
    b = pick any block from worklist
    in[b] = merge(out[p] for every predecessor p of b)
    out[b] = transfer(b, in[b]) # this is output func
    if out[b] changed:
      worklist += successors of b
```

There are "forward" and "backward" versions. To make it backwards, flip in and out and predecessors.

The algorithm still converges even if there are cycles.

## Requiremetns for Data Flow Analyses

**Monotonicity**: The values can only go in "one direction."
- For sets, you can only add things or remove things, not both.

This idea forms a lattice of the possible states that the algorithm can be in. Which is a partial order. If you only ever follow the edges on the partial order, the algorithm will converge. So the transfer function must be monotonic.

**meet**: Merge function. Big meet is merge function across all paths.

Correct solution to a data flow analysis is its "meet-over-all-paths" solution.

## Other Analyses

Reaching Definitions, Live Variables, Constant Propogation, Available Expressions, Initialized Variables, Interval Analysis,

Live Variables:
- Domain: Sets of variables
- Direction: Backward
- Merge: Union
- Transfer (backwards): `f(out_b) = use_b union (out_b without KILL_b)` (why is this backwards?)
