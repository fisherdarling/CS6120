---
tags: [Compilers]
title: 'Lesson 1: Intro'
created: '2021-02-13T20:39:21.831Z'
modified: '2021-04-15T02:01:06.991Z'
---

# Lesson 1: Intro

What is in a compiler? Why are the important? Compiler optimizations and making code go faster is a huge topic in compilers, but not necessarily the only one. There's many other topics and fields in creating a compiler. Proebsting's Law: Improvements in Compiler tech will double the perf of a typical program every 18 years. 

Expansion in languages and frameworks. R / Tensorflow count as DSLs. DSLs have been found to better match how people think about certain problems. Diversity of hardware targets is greatly increasing. N x M scaling problem. N = number of languages is increasing, M = number of targets is increasing. Novel hardware is super exciting, but without good compilers, the entire power of the hardware can't be reached.

Video Writeup:

Lesson 1's video was mainly about the syllabus, but the first third was mainly about answering the question "why do we build / study compilers?" The general answer is to "make programs go fast," but that is a small part of what goes into actually implementing a language. The goal of a compiler is to turn a language that a human "produced" and converting into something that a machine can execute. We were asked to describe some of the other aspects that go into language design. The following are some of the ones I came up with:

1. Typing / Type Inference / Type System
2. Runtime: Garbage Collection, Multi-Threading
3. Operating System interaction and implementation
4. Foreign Function Interface (FFI)

We then learned about the (joke) law, Proebsting's Law. The law describes that every 18 years, work in the field of compilers will double the speed of common programs. The originally a joke, the linked paper describes that there has in fact been a slow growing expontential that describes compiler speed work.

Finally we learned about why compiler research and theory is so important in present research. Domain Specific Language (DSLs) are a "better way to match the semantics of how people think." Essentially, rather than having a single "common" language to rule them all, we are experiencing a rapid expansion of DSLs that compile to different targets. The N x M problem follows from this expansion. New languages, N, are growing in number and the number of compilation targets, M, is growing as well. For a language to be successful, it should compile to multiple targets, but due to the expansion of N and M, we have an exponentially growth in the amount of compiler work required. A lot of the time new hardware is created to target some specific optimizations, yet there is a lack of good compilers targetting for those hardware system. No one will adopt new hardware if it requires hand-written assembly to program.


