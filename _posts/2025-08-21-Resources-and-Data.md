---
tags:
  - programming
title: Data types vs. Resource Types
source:
  - https://without.boats/blog/revisiting-a-smaller-rust/
date: 2025-08-21
layout: post
teaser: The type system only hints at some important information
---
[Without Boats](https://without.boats/) revisited ideas about a ["smaller rust"](https://without.boats/blog/revisiting-a-smaller-rust/).  The section on Resource types vs. Data types was very instructive.  The core distinction is that data can be copied while resources are shared.  This distinction is *lost* programming languages (Rust included) because the language uses a metaphor of RAM (which is a resource). 

However, when you try to interpret different parts of memory it breaks into two major categories:
- **Data**: Immutable, meaning is derived entirely from context, separate copies are indistinguishable and interchangeable.
- **Resources**: Maybe mutable, carry some inherent meaning and copies might be distinguishable from each other (minimally because mutability may lead them to have different values over time). 

Attending to this distinction enables some optimizations and freedoms that are often lost. 

The post is instructive in how it illustrates that the distinction is latent in the Rust type system.  `Copy` items are data.  Any time you use a mutable borrow (`&mut`), you are treating something like a resource.  Rust isn't *enforcing* a data/resource distinction, it assumes everything is a Resource, but it *manifests* one and that might inform future language design.  This is in contrast to clojure that assumes everything is data and goes to some lengths to ensure that most resources behave like that through extensive use of persistent data structures, to get a resource you use the `transient` or `!` function variants, but it is presented as n optimization.