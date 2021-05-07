---
tags: [Compilers]
title: 'Lesson 8: Interprocedural Analysis'
created: '2021-04-16T04:13:37.911Z'
modified: '2021-05-07T01:36:38.410Z'
---

# Lesson 8: Interprocedural Analysis

So far we have done plenty of local analysis and global (function) level analysis. Interprocedural analysis allows us to optimize and learn about other functions.

Facts from one function influence other functions.

For Interprocedural Analysis we use call graphs.

**Call Graphs**: Nodes are functions and edges are function calls.

Open vs Closed Worls analysis:
- Open World: You only see a subset of the code that's running. We only see some of the functions going on.
- Closed World: We assume that the code that we see is everything. Nothing else can influence execution and all of the code is there.

Why do we use an "open world" assumption?
- Modularity: Parallelizable, Incremental, QoL
- Speed: There's a lot less program to analyze
  - Optimizations can have diminishing returns
- Run-Time Code Loading: We can't be sure that we have all of the code that we're running at compile-time.

Opportunities for assuming a closed world:
- Link-Time Optimization (LTO): Seconday optimization phase after all "modules" have been compiled.
- Just-In-Time (JIT) Compilers: We assume temporarily that we've seen all code that will execute. If code changes (like loading a class file) we have to revisit our assumptions.

## Inlining

If we have a function call, we can delete the call and copy and paste it into the function.

Inlining Needs Heuristics:

Cost: Increased code size reduces instruction cache locality and bloats executables.

Benefit: 
- Remove call overhead
- Downstream optimizations

Inlining is considered one of the most beneficial things that a compiler can do since it enables global analysis across interprocedural code.

## Devirtualization

Virtual function calls are everywhere in object oriented languages:

```
Foo o = new Baz();
o.m() // Which m()? Foo? Baz?
```

We don't know what function to inline in object oriented code...

Simple opportunity for devirtualization. The above example is obviously `Baz`'s `m()`. We can use some global analyses that can determine which `m()` is "reachable".

## Context Sensitivity

Does some function when called from other function change some value? This allows us to do things like DCE across function calls.

**k-frame calling context**: Number of functions anaylzed before determining if "x" question is true.

If we have `k` stack frames, we require `n^k` queries. Trade-off of optimization time and optimization level.


