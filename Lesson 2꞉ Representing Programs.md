---
tags: [Compilers]
title: 'Lesson 2: Representing Programs'
created: '2021-02-13T21:06:48.325Z'
modified: '2021-02-13T21:58:47.533Z'
---

# Lesson 2: Representing Programs

Concrete Syntax does not represent _semantics_ that well. Trees are perfect for running a program and are really convienent for writing a compiler. Many compilers never really transition out of an AST. This class will use "lists" of instructions rather than a tree. AST nodes all have different semantics, which make it hard to write passes over everything. Children may mean many different things. We can make things more regular and predicable by using a list.

We transform the program into something that's more of an assembly language, in a sense that it's a list of instructions. All have a regular form, name of an operation and variables that are all regular. Not a bunch of different kinds of syntax nodes. You can declar labels, functions, and run instructions.

`dst: type = opcode args...;`

Really convienent way to process programs as a compiler. Technically true that this representation can have concrete and abstract syntax.

Many different ways to extract information from this list of instructions.

* `Control Flow Graph` (CFG)
  * Directed Graph
  * Vertices are instructions
  * Edges indicate _possible_ flow of control
  * Exactly one _entry_ vertex & one _exit_ vertex.

Chains of instructions are very common in a CFG. Therefore, we create `Basic Blocks`. Jumps / Branches only occur at the end of a basic block. Basic Blocks become the "unit" of a CFG Vertex. Basic blocks cannot grow beyond a branch instruction. You can only jump to the beginning of another basic block. Ends of basic blocks are called `Terminators`.

Pseudo Code for instructions -> basic blocks / label_map:

```
in: instrs => list of instructions
out: blocks => List of Lists of instrs
out: labels => map of labels to blocks // easy to add

blocks = []
curr_block = []

while instrs.has_next():
  next = instrs.next()
  if next is not a label:
    curr_block.push(next)
  if next is terminator or label:
    blocks.push(curr_block)
    curr_block = []

return blocks
```

Next steps is to create a CFG. Graph with an edge lists.

```
in: blocks, labels
out: cfg => Label -> List of Labels

for block in blocks:
  last = block.terminator
  
  if last is JMP:
    cfg[block] += last.dest
  elif last is BR:
    cfg[block] += [last.true, last.false]
  else:
    cfg[block] += block + 1 (next block)
    if there is a last block
```

# Getting Started with Bril

Big Red Intermediate Language. Meant to be easy to start with, and require no parsing. The output of the parse is a large JSON file. The JSON representation is the "true" representation of the bril language. The human representation is simply just for human usage.

Any programming language and any set of tools to load and interpret bril languages. Piping model for easily adding transformations and such.

Calls do not count as terminators for basic blocks since basic blocks are "atomic". If a call is executed, eventually the next instruction will be executed.















