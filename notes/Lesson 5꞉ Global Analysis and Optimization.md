---
tags: [Compilers]
title: 'Lesson 5: Global Analysis and Optimization'
created: '2021-04-15T03:04:06.739Z'
modified: '2021-04-15T03:55:32.010Z'
---

# Lesson 5: Global Analysis and Optimization

Continuing with analyzing entire functions.

A path is a chain of edges along the graph. Same as in graph theory.

**Dominators**: A CFG vertex A dominates verted B iff: all paths from the entry to B includes A. Essentially, A must execute before B. It's reflexive

Domination Exercise:

- entry: {entry}
- loop: {entry, loop}
- body: {entry, loop, body}
- then: {entry, loop, body, then}
- endif: {entry, loop, body, endif}
- exit: {entry, loop, exit}

**dominator tree**: a node in the tree dominates all nodes below it.

- A **strictly dominates B** iff: A dominates B and A != B
- A **immediately dominates B** iff: A dominates B, and a does not strictly dominate any other node that dominates B. (A is B's parent).
- A's **domination frontier** contains B if A does not dominate B, but A dominates a predecessor of B. (It's the fringe in the CFG right after A's domination stops.). Nodes that share a dominator with "A" are in the frontier.

- A **post-dominates** B iff all paths from B to the exit include A. Same with strict post-dominiation.

Finding Dominators:

```
dom = {} <- vertex to set of verticies
while dom is still changing:
  for vertex in CFG:
    dom[vertex] = {vertex} union the intersection of [dom[p] for predecessor in vertex.predecessor]
```

`O(n^2)` worst case. If you sort the CFG in reverse post-order, it will run in linear time - outer loop runs in constant number of times (WHAT ???). Only holds for a "reducible" CFG.

**Reducible**:
- Natural Loops: Cycle in CFG with a *single entry* (one vertex with in-edge).
- Backedge: An edge in A -> B where B dominates A.
Edges that aren't in the domination graph are back edges.

Actual definition of natural loop:

For a backedge A->B (B is the header and entrypoint):
  The smallest set of verticies L including A and B such that:
    - For all vertex, v in L: PREDS(v) is subset of L OR v = B

If a language has a GOTO, then it is irreducible.

## Loop-Invariant Code Motion

Moving code that doesn't rely on the loop to the outside of the loop.

```
Until convergence:
For every instruction in a given loop:
  Mark an instr as L.I. iff
  For all arguments x, either:
    - All REACHING DEFINITIONS (from earlier)
      of x are OUTSIDE the loop
    - There is exactly one definition, and it
      is already marked as L.I.
```

Move L.I. Instructions to preheaders IFF:
- Definition dominates all its uses
- No other definitions of the same variable
- Dominates all loop exits
  - This can be relaxed if the definition is DEAD after the loop.
  - And it CANNOT throw an exception like a divide.
These are the rules for **speculative** optimizations ^.






