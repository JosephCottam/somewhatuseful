---
layout: post
title: "Biformality of Programming Languages"
date: 2023-08-07
tags: 
  - programming
  - types
teaser: Type and data-level programs need the same low-level concepts. Why do they look different?
---

I came a cross an [interesting critique on static-vs-dynamic types](https://hirrolot.github.io/posts/why-static-languages-suffer-from-complexity.html) that took an angle that illustrated something I've never thought of before.  

The basic idea is that languages with static type systems tend to develop two implementations of everything, one that works at the type-level (static) and one that works at the value-level (dynamic).  This is "biformality" in that every formal capability needs to be represented twice.  

Two implementations causes (at least) two problems. First, the two implementations are not the same, so programmers have to learn everything twice and the way things are expressed is very different so translation is non-trivial.  Second, the static and dynamic systems are developed under different pressures, so they develop their representations in different sequences, with different corner cases, etc. So programs might not be exactly directly expressible in both ways.  

This short-comings lead to ad-hoc static systems built into the dynamics layers to try to provide guarantees...and ad-hoc dynamic systems built into the static layers to provide expressivity. (Echos of [Greenspun's 10th rule](https://en.wikipedia.org/wiki/Greenspun%27s_tenth_rule) are noted.)

Dynamic languages don't get off the hook in the article: The guarantees of static analysis are valuable.  Dropping such guarantees cuts off a lot of power that computers bring to tame complexity.  (This relies on Peirce's notion of a type-system being based on 'syntactic methods', so anything that requires simulating program execution is off the table.)

Macros are not a solution according to the article...but this is the part I understood the least.  I have little experience with macros (and most of that using only simple macros in scheme). The claim is that macros "simply have no idea about the language being manipulated". I believe this is an assertion that macros do not know language semantics, only syntax, and thus can only have limited utility unless they also include a fair amount of simulation...but I'm reaching on that point.  Perhaps this explains why lisp-style macros are useful in lisp (relatively simple semantics, that closely follow the language syntax) and less useful in other languages (the AST is further from the language syntax, and semantics are less obvious).  This is just a guess...

[Idris](https://www.idris-lang.org/) is put up as a language that avoids "biformality" and thus is an interesting datapoint in a static-vs-dynamic typing discussion.  I don't know idris, and did not follow the example.  But taken at face-value (assuming all points are correct and representative), it would be an interesting place to watch.
