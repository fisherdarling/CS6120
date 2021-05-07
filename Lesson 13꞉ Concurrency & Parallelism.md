---
attachments: [Clipboard_2021-05-06-19-30-10.png, Clipboard_2021-05-06-19-34-20.png]
tags: [Compilers]
title: 'Lesson 13: Concurrency & Parallelism'
created: '2021-05-07T00:57:30.547Z'
modified: '2021-05-07T01:36:38.378Z'
---

# Lesson 13: Concurrency & Parallelism

Parallel programming has greatly exploaded in recent memory. Single thread performance has stagnated, therefore requiring more CPUs to get more performance.

Parallel Programing Models:
- Message Passing (supercomputers)
- Shared Memory (pthreads): truely everywhere
- Data Parallelism (cuda)
- Task Parallelism (fork/join)
- Actors

Semantics of shared memory accesses vary across different architectures and implementations. Almost no programming language allows you to assume the ordering of instructions...

Sequential Consistency:
The intuitive "interleaving" semantics of shared memory (`SeqCst` in Rust?). Essentially instructions from all threads are "interleaved". All possible interleavings are valid programs. This is convienent since every parallel execution has a sequential, non-parallel interleaving.

The happens-before relation: partial order
- Program order within threads
- "messages" across threads
Observing an affect from another thread is an edge in the "happens-before" graph. Under sequential consistency, executions correspond to HB graphs, and the value for every load comes from the most recent store to that location in that order.

## Reality SUCKS
No realistic hardware (and almost no compiler) provides SC.

CPU Core caches take some time to populate / sync with main memory, and therefore values under SeqCst that are impossible are actually allowed. CPU Cache Coherence tries to solve this but it is difficult to get right.

Intutition around interleavings is __WRONG__.

## Synchronization & data races
Realistic memory momdels rely on special synch operations:
mutexes (locks), semaphores, condvars, barriers, atomic ops

Data race:
- two accesses to the same mem location
- in different threads
- at least one of which is a write
- unordered by synchronization

All good memory models obey this slogan:
DRF => SC
Data-Race-Free programs, enjoy sequential consistency. But racy ones may have a tougher life...

- C and C++, as of 2011: Data races are undefined behavior. Otherwise, SC.
- Java: Races have semantics, but they are super weird and probably wrong. DON'T DO DATA RACES and your life is easier.
- Rust: Races are impossible statically! 😍
- ISAs: A subject of much consternation and formal reasoning. But always DRF=>SC.

## Compilers that enforce memory models?

_aside:_ 
> Dekker's Algorithm?

If the mutex code is a normal hardware operation, then there's no way for it to know that there's a data race. Could use inline assembly for special sync operations. Then the compiler needs to know all of the priviledged compiler instructions.

If the compiler doesn't know about those instructions (and that they have side effects), then the compiler might actually just reoder those instructions above the lock, removing the synchronization.

The compiler could tree lock calls as a "black box" that could touch everything in memory.

Some optimizations might result in a data race:
![](@attachment/Clipboard_2021-05-06-19-30-10.png)

The compiler could compile it to a speculative operation that could __actually introduce__ a data race (christ).

![](@attachment/Clipboard_2021-05-06-19-34-20.png)

Simple optimization to just store an entire word that can add a data race (other thread writes to `d`)

Writing compilers that can optimize code in a multi-threaded context is _hard_ yet necessary.

Paper on the topic `Threads Cannot Be Implemented As a Library`.

Why is the dominant paradigm `shared memory parallelism`?
- This is the world we live in, deal with it.
