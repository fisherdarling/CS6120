---
attachments: [Clipboard_2021-05-06-17-52-02.png, Clipboard_2021-05-06-17-57-24.png, Clipboard_2021-05-06-18-00-52.png]
tags: [Compilers]
title: 'Lesson 11: Dynamic Compilers'
created: '2021-05-06T23:39:35.230Z'
modified: '2021-05-07T01:36:38.441Z'
---

# Lesson 11: Dynamic Compilers

__Just-In-Time (Dynamic) Compilers__: These compilers are exedcuted as the program is executed. In AOT (Ahead of Time) compilation, compilation is done before the program is ever ran.

## Why JIT?
- Exploit dynamic information to do better optimizations.
- Dynamic languages can make very few guarantees and benefit from dynamic information. "This number is always an integer".

For example, in python:

```
a + b = a.__add__(b)
```

Which, if types are known at runtime (a and b are both floats), could be compiled to: `fadd r1 a b`. Some languages really need dynamic information in order to optimize dynamic languages.

Nitpicks: 
- Languages ARE NOT interpreted or compiled!!! That is purely based off of their "implementation."
- "Compiler vs. Interpreter" IS A SPECTRUM! CPython parses into an AST which is _compiled_ into bytecode which is then _interpreted_.

## How to JIT

Flow:
- Observe program (interpret)
- Feed observations into compiler (compile)
- Repeat

Why do we switch from compiled back to interpreter? Hot routines are compiled first and some code will remain being interpreted.

![](@attachment/Clipboard_2021-05-06-17-52-02.png)

JIT Tiers:
Interp -> Simple Compiler -> Good Compiler -> Amazing Compiler

## Tracing vs. Method JITs

Method JIT: The "normal" kind, compile a function at a time. Unit of compilation is a "function".

Tracing JIT: "Funkier" kind. Extract a hot path through the CFG and optimize it like it was one big function.

![](@attachment/Clipboard_2021-05-06-17-57-24.png)

After we find a path, we compile the code like it has __NO__ branches. Kinda hard to figure what the "common" case is.

Most mainstream JITs are method JITs. Though PyPy is a tracing JIT. PyPy traces an interpreter for python to generate machine code.

Tracing Example:
![](@attachment/Clipboard_2021-05-06-18-00-52.png)

We write down every statement for the path (interprocedural):
```
y = x + 1
guard(x < 100) # Divergence in control flow, add a "guard" (abort) 
a = y;
z = a - 1;
return z;
```

Guard instructions "abort" the execution of the straight line program and return to interpreted code. It's ok if recovering from a guard takes a while since 99% of the time the guard is true.

Interprocedural tracing leads to really simple instructions.

First, guard _as early as possible_:
```
guard(x < 100) # Divergence in control flow, add a "guard" (abort) 
y = x + 1
a = y;
z = a - 1;
return z;
```

Second, something like local value numbering (LVN):
```
guard(x < 100);
return x;
```

JITs let you do optimizations that don't really seem apparent in static code, but make a lot more sense at runtime and there are no branches.

Tracing JITs take complicated programs and distill simple blocks of code that can be easily optimized.
