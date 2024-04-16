---
title: Tests are post-hoc brittleness
teaser: Brittle-like-a-fuse is a good thing for all systems
date: 2024-04-16
source: https://journal.infinitenegativeutility.com/leaving-haskell-behind
---
In their [post on leaving Haskell behind](https://journal.infinitenegativeutility.com/leaving-haskell-behind), the term "brittle" is used as a compliment to mean things break in obvious ways. This is in contrast to the derogatory sense that "brittle" often takes in software, rough "unable to easily adapt to a new use case".  I like the idea that brittle is a not a judgement but a *property* and how it is manifests (or is used) in a system determines if it is a good or bad thing.

Wikipedia says "A material is brittle if, when subjected to stress, it fractures with little elastic deformation and without significant plastic deformation."  That is to say, brittle things won't bend, they just break.  

Fuses are an example where brittleness is used to benefit an electrical system.  Rather than slowly degrading, they trip...and the whole system stops before worse things can happen.

In Haskell, types introduce useful brittleness.  They are compiler checked assertions about the program.  If the types don't match up, the system stops.  In Python, tests are a deliberately brittle point (when written regularly, run often and issues routinely addressed)[^optionalTypes]

The trick is to hit the _right_ amount of brittleness in the right places.  What constitutes "stress" to the system?  Where can breaking under stress prevent worse issues?

---
[^optionalTypes]: The introduction of types into python as *optionally* checked makes this parallel between types and test even more apparent.  The idea that types are a test-suite that the compiler checks has always resonated with me, though I don't think its the whole story of types.
