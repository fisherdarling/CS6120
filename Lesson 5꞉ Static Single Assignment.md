---
tags: [Compilers]
title: 'Lesson 5: Static Single Assignment'
created: '2021-04-15T03:56:03.957Z'
modified: '2021-04-15T04:26:52.592Z'
---

# Lesson 5: Static Single Assignment

Static Single Assignment (SSA):

A lot of the problems that are dealt with are variable name conflicts. Life would be nice if there was only every one unique name per variable.

SSA:
- Every Variable has _exactly_ one assignment.

To turn a program into an SSA form, we create a phi node.

phi nodes select between multiple names depending on incoming CFG edges.

`a.4 = phi a.2 a.3`

Typically we use the labels of incoming blocks:

`a.4 = phi .left a.2 .right a.3`

The phi instruction is an extension of the bril language.

We need to be careful with loops and labels. Basically all incoming labels need to be included in a phi instruction.

## The SSA Philosophy

- definitions == variables
- instructions == values
- arguments == data flow graph edges

In LLVM memory, instructions are literally pointers to other instructions.


phi-nodes need to be inserted whenever there are two definitions for a variable in different blocks.

No phi-nodes needed for blocks where the definition dominates that block. We use the dominance frontier to figure out where we need to put phi-nodes. So all nodes in the dominance frontier of the entry into the CFG.

Converting to SSA:
- Insert phi-nodes
  Will be nodes like `x = phi x x x x ...`
- Rename variables to unique names.

Inserting phi-nodes
```
for v in vars:
  for d in defs[v]: # Blocks where v is assigned
    for block in DF[d]: # Dominance frontier
      Add a phi-node to block if not already there
      Add block to defs[v] unless it's already added.
```

Renaming Variables
```
stack[v] is a stack of variable names

def rename(block):
  for instr in block:
    replace each argument to instr with stack[old name]
    replace instr's dst with a new name
    push new name onto stack[old name]
  
  for s in block's successors:
    for p in s's phi-nodes:
      make p read from stack[v]

  for b in blocks immediately dominated by block:
    rename(b)
  
  pop all names pushed to stack

rename(entry)
```

## Converting *From* SSA

SSA has no "mutation," but real machines do.

phi-nodes for some variable always have variables that lead into it.

To remove phi-nodes you add blocks immediately before the target block that are in the path of the variable's uses. These blocks then contain simple assignments to the original variable.



