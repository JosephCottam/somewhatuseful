---
source: http://www.incompleteideas.net/IncIdeas/BitterLesson.html
title: The Bitter Lesson
layout: post
date: 2024-04-25
teaser: The "bitter lesson" is that complexity is ultimately limiting.
source2: https://rodneybrooks.com/a-better-lesson/
---
> "...general methods that leverage computation are ultimately the most effective, and by a large margin."
> --Rich Sutton (March 13, 2019)

[The Bitter Lesson](http://www.incompleteideas.net/IncIdeas/BitterLesson.html) is a short essay by Rich Sutton.  It's worth a read, but  I'll summarize the last two paragraphs here:
- Observation S1: Attempts to inject "knowledge" leads to early advances but rapid plateaus while search and learning compound.
- Observation S2: There are two things that scale easily with computation that drive AI broadly: Search and Learning.   
- Implication S1: General-purpose methods (like search & learning) that scale with computation are the most valuable 
- Implication S2: Minds are complex enough that seeking simplifications is ultimately a dead-end.

Sutton was talking specifically about how AI advances, and the past 6-ish years of deep learning punctuate his point.  Rodney Brooks posted a rebuttal [The Better Lesson](https://rodneybrooks.com/a-better-lesson/). I summarize Brook's points as this:
- Observation B1: Convolutional Neural Networks, neural network architectures and training regimes that preserve human-relevant factors are ways that humans still inject "knowledge"  
- Observation B2: People need less than computers to learn these sorts of things so "something" is still missing.
- Observation B3: The [free lunch is over](http://www.gotw.ca/publications/concurrency-ddj.htm) so don't rely on it to improve your naive algorithms.

Now for my (admittedly 5 years late) observations:
- Brooks makes a strong point that "something" is missing, but this point is weakened because the place and method of knowledge-injection is radically different from Sutton's.  Brook's phrasing is a **denial** of Sutton's thesis, when I see Brook's arguments as fundamentally supporting Sutton's point (that injecting knowledge at algorithm level is ultimately a limiting factor).  Brooks and Sutton BOTH are arguing that we should look for smarter, more scalable ways to introduce things that we have learned are important. (But they disagree on what 'scaleable means.)
- Insofar as AI is trying to model a mind (human or otherwise), Implication S2 is probably in the right direction (though perhaps a bit fatalist).  However, when AI is trying to model/estimate something it must be assessed on a case-by-case basis. For example, physics-informed machine-learning probably still benefits from injecting simplified models (albiet, used in a different way with deep learning systems than with others).
- Search & learning are general-purpose computational methods that scale for AI.  Does the observation hold for other parts of computer science?  I feel echos of RISC architectures, which under-pine the modern desktop systems...but do not feel as simple as they used to.  The scaling doesn't always play out like we think, though the quest for simplicity will often be beneficial.