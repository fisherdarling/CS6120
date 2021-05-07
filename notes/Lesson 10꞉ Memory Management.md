---
attachments: [Clipboard_2021-05-06-17-37-21.png]
tags: [Compilers]
title: 'Lesson 10: Memory Management'
created: '2021-04-28T04:40:09.415Z'
modified: '2021-05-07T01:36:38.431Z'
---

# Lesson 10: Memory Management

Automatic memory management: Garbage Collection.

Memory management is difficult, and so doing it automatically is really useful from a programmer's perspective.

From Microsoft: 70% of their security issues were from memory management bugs in "unsafe" languages.

Manual:
- C, C++

Automatic + Dynamic:
- Java, JS, OCaml, Scala,
Haskell, Ruby, Python, Swift, C#,
PHP, Kotlin, Go, MATLAB

Automatic + Static:
- Rust

Memory management is difficult, especially for branches that require runtime input (user input, etc). It also needs to know the semantics of whatever memory container, etc is difficult. Dealing with dynamic behavior is difficult, which is why memory managment is generally done at runtime, not compile time.

We only want to free memory after it's "unreachable".

We define "unreachable" as: the program is unable to get to some object from any other object in the program. More of a "graph" theoretic approach to GC.

Data is vertices in the graph, and pointers are the edges.

There is the "heap" so non-stack memory, and then variables that can point to things on the heap (locals, and globals). These variables are called "roots".

We start looking for heap data from the "roots".

# Two Main GC Types:

GC Terminology:
- Collector: Short for GC. The runtime system that calls free for you.
- Mutator: The actual program you're running, in contrast.
- Live: Will be accessed again in the future.
- Dead: Otherwise.

Two ways to automatically call `free` on unreachable objects:

- Reference Counting: *Each* object has a variable that tracks the `in-degree` in the heap graph, on _every_ pointer update.

Increment/Decrement

If the value is zero, a pointer to the object points to somewhere else, we free the object. If a zero count object is deleted, it needs to recursively call the decrement on its members.

- Mark/Sweep or Tracing GC (sometimes just called "GC"): No extra metadata, just analyzes the graph. Runs periodically (whenever you feel like it).

Run a graph search (DFS/BFS). Mark all reachable ojects. Objects that aren't marked are deleted. Nodes have different colors, gray, white, and black.

- Gray: Currently Visiting
- White: Unvisited
- Black: Done Visiting

Roots start as black, and all nodes that black nodes point to are colored grey. Color grey as black and re-color children. Once it converges, free all non-colored nodes.

Pros for each:

RC:
- Easy to implement
- Free's ASAP
- Deterministic

M/S:
- HANDLES CYCLES
- Less per-access overhead (mutator is fast)
- Controllable

Languages that use RC usually have backup M/S for finding cycles.

## Conservative GC
Can only free garbage (never non-garbage), but not guaranteed to free _ALL_ unreachable objects. (every other GC non retroactively reclassified as "precise")

Good for C/C++, when you don't know the locations / types of pointers at run time (all ints). Conservative GC's look at all pointer-like variables, and mark memory regions that _could_ be pointers as black. Common data structures are robust to errors from conservative GC's. Conservative GCs work _way better_ on 64-bit machines than on 32-bit machines (pointer and random value conflict). 64-bit pointers are much more precise than 32-bit pointers.

## Parallel Collectors
Use multiple threads to do the tracing part, so it goes faster.

## Concurrent Collectors
The collector _and_ mutator run **concurrently**. All previous GCs were reclassified as "stop-the-world" collectors.
```
STW: MUTATOR -> GC -> MUTATOR -> GC ...
Concurrent: GC    GC    GC
            MUTATOR MUTATOR MUTATOR
```

Pros:
- No pauses
- Speedup?

Cons:
- Synchronize
- HARD TO DO

## Incremental Collectors
"Advance" the tracing process just a little bit each time.

## Moving / Compacting / Copying Collectors
Move memory around so that it is more contiguous and "compacted".

After moving the data around, the pointers to the old location are now wrong. For updating pointers, you could "invert" the graph, 

## Semispace Collectors
1. Allocated into "from" space.
2. Once "from" space fills up, run the GC
3. Mark all reachable objects as black in the from space
4. Copy living stuff to the "to" space. 
5. Free the "to" space, which is just one operation!

## Generational Collectors
Exploit the "generational hypothesis": Most objects die young

Recently-Allocated objects are more likely to become unreachable in a given interval of time than objects that have already lasted a long time. This generally holds with emperical evidence.

Divide the heap into multiple sections:
- Nursery: Gen 0, allocated in there
- Gen 0
- Gen 1
- Gen n

Run collection on the nursery a lot more frequently then the other generations. Survivors are placed in the next generation.

![](@attachment/Clipboard_2021-05-06-17-37-21.png)




