---
tags: [Compilers]
title: 'Lesson 12: Program Synthesis'
created: '2021-05-07T00:18:11.420Z'
modified: '2021-05-07T01:36:38.450Z'
---

# Lesson 12: Program Synthesis

Generating (synthesizing) programs from a "spec". A description is given and a function is generated. The "flash fill" feature in excel takes some inputs and tries to generate an equation that will produce those cells.

Theories are like types: a is a float. Theories are combined together to make queries.

Small SMT example:
```
import z3

def solve(phi):
    s = z3.Solver()
    s.add(phi)
    s.check()
    return s.model() # Evaluation

if __name__ == '__main__':
    z = z3.Int('z')
    n = z3.Int('n')

    # y = z3.BitVec('y', 8)
    form = z3.ForAll([z], z*n == z)
    print(solve(form))
```

Sketch based program synthesis takes in a partial program and tries to complete it.

Simple "hole" solver, `h` evalues to `2`:
```
import z3

def solve(phi):
    s = z3.Solver()
    s.add(phi)
    s.check()
    return s.model() # Evaluation

if __name__ == '__main__':
    # x * 2
    # x << ?? <- hole

    x = z3.BitVec('x', 8)
    slow_expr = x * 4

    h = z3.BitVec('h', 8) # ??
    fast_expr = x << h

    goal = z3.ForAll([x], slow_expr == fast_expr)
    print(solve(goal))
```

The interpretor becomes a constraint generator.
