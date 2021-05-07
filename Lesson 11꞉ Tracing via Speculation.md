---
deleted: true
title: 'Lesson 11: Tracing via Speculation'
created: '2021-05-07T00:10:09.786Z'
modified: '2021-05-07T00:18:09.785Z'
---

# Lesson 11: Tracing via Speculation

Three instructions:
- __speculate__: Enter a speculative execution context. Changes aren't made until "commit" is called.
- __commmit__: End speculation and commit the current state.
- __guard__: Leave the speculative context and "abort" the tracing.


