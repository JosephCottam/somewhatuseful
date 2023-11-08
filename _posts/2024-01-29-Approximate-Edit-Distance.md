---
teaser: A notion of edit distance using the standard library
layout: post
title: Edit Distance in Python
---
I've occasionally needed to compare strings to get an idea of how similar they are.  Python has several libraries ([editdistance](https://pypi.org/project/editdistance/), [edit-distance](https://pypi.org/project/edit-distance/), [python-Levenshtein](https://pypi.org/project/python-Levenshtein/)...probably more) that do this, but sometimes I get a sense of "yet another install....*sigh*".  

Recently (after such a *sigh*), I was looking at the built-in [difflib](https://docs.python.org/3/library/difflib.html) to solve a related problem and found the [ratio](https://docs.python.org/3/library/difflib.html#difflib.SequenceMatcher.ratio) method of `SequenceMatcher` (and its siblings `quick_ratio` and `real_quick_ratio`).   What I've discovered is that using `SequenceMatcher(a=string1, b=string2, autojunk=False).ratio()` gives a good approximation of how many characters are the same in each position...with corrections for insertions/deletions.  

Notes on use:
- I found `autojunk=False` is **essential** or it chunks the data in unexpected ways based on character frequency.
- `quick_ratio` was near-instant while `ratio` took several seconds.  The result was a value difference of less than 0.0004 on average for my strings of 7-10k characters.  Its worth a try if you want speed...
 
I'm happy to have a way to measure this more robustly than counting character occurrences without installing another library!