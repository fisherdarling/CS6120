---
tags: [Compilers]
title: 'Lesson 6: LLVM'
created: '2021-04-15T04:27:22.727Z'
modified: '2021-04-15T05:21:42.998Z'
---

# Lesson 6: LLVM

## Introduction to LLVM

Extremely popular compiler infrastructure that doesn't actually stand for anything.

"Supercharged" version of bril. Similar and opposite to bril. Bril is an idealized version of LLVM and LLVM is actually used in the real world. LLVM is a lot less friendly than in bril.

Main point of the video is walking through writing your own pass.

```
int main() {
  int x = 42;
  return x;
}
```

Compiles to:
```
define dso_local i32 @main() #0 {
  %1 = alloca i32, align 4
  %2 = alloca i32, align 4
  store i32 0, i32* %1, align 4
  store i32 42, i32* %2, align 4
  %3 = load i32, i32* %2, align 4
  ret i32 %3
}
```

Every LLVM program is in SSA.

## Writing an LLVM Pass:

Wrote a simple pass for replacing an LLVM IR binary operator with a multiplication.

The second pass was really cool, and builds into how compilers insert performance tracking / more fine grained profiling. The pass involved writing a dynamic library on the side with a single func that prints its integer argument. Then adding a call to that library func for the first binary operator found.

