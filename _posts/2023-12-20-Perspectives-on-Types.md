---
title: Perspectives on Types
tags:
  - programming
  - types
layout: post
teaser: '"Static vs. Dynamic" is such a straight-jacket...'
---
I think types for programming languages are a great idea. I'm not a static or a dynamic purist.  My thoughts are still refining, but here are some things that have influenced them.

First is Pierce's ["Types and Programming Languages"](https://mitpress.mit.edu/9780262162098/types-and-programming-languages/) (TAPL).  The introduction lays out a definition for a *type system* as "tractable syntactic method for proving the absence of certain program behaviors by classifying phrases according to the kinds of values they compute."  Later that section calls out "dynamic types" as a misnomer, but common enough as to not be confusing.  Pierce is scoping what his book will talk about, but the distinction is echoed by others: types can be about programs or values....and so "static vs dynamic" is the wrong tension, they are talking about different things.

The values-vs-programs divide is also rehearsed in ["What to know before debating type systems"](https://blogs.perl.org/users/ovid/2010/08/what-to-know-before-debating-type-systems.html) post (later reposted [here](https://cdsmith.wordpress.com/2011/01/09/an-old-article-i-wrote/), with minor commentary but emphasizing that the conclusion is the same). 

More recently, [Church vs. Curry Types](https://ericnormand.me/article/church-vs-curry-types) ([Eric Normand](https://ericnormand.me/)) appeared.  It still indicates that "static" and "dynamic" types are vastly different perspectives, but does not use programs-vs-values.  Instead it illustrates two different categories of type systems (extrinsic/curry and intrinsic/church) and how they relate to programs.  From the perspective of Pierce, both seem to be static systems since they are typing entire programs *but* they include a-type-that-means-no-type-commentary-is-provided-by-the-system. (An "untyped" type...but exactly how to interpret that type depends on if you are taking a Curry or Church prsepective.)  I like the notes about applying multiple type systems to a program, but I don't think it is limited to Curry/extrinsic types (Rust's lifetimes could be viewed as a separate type system from its parameter/variable/return type system, but both are Church/intrinsic types, `unsafe` blocks may mean there are four type systems at play, but I think that is stretch).
