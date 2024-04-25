---
title: Don't Abstract Vega Names
teaser: Systems express opinions on what they make easy & hard.  Vega makes it hard to abstract over names.  This isn't necessarily bad, but keep it mind when you make your schemas.
tags:
  - visualization
  - vega
---
#TODO: Get the reference for 'use opinionated tools'

[Vega](https://vega.github.io/vega/) provides a way to construct visualizations from a JSON schema.  I am often tempted to re-use schemas between projects, which leads to trying to make some generalized schemas that I can use in many different cases.[^vegalite]  

[^vegalite]: This is sort of like a "charting library".  I am aware that [vega-lite](https://vega.github.io/vega-lite/) is actually charting library built on vega. This is certainly adjacent, though not as invasive as vega-lite (which defines a new grammar that is in some ways at odds with vega's grammar).  I am also aware that this might be a foolish goal.

I was recently working on a graph-based schema and tried to abstract away the name of the attribute used in labels.  This proved to be difficult because of a few features of vega: (1) There is no way to get a field-name from a signal  and (2) expressions are just strings.  The two combined make it difficult to create a schema that abstracts out the names of fields.

I should not have been surprised by this, though...there are a LOT of fixed names in vega itself.  `fx` and `fy` have special meaning in the [force transform](https://vega.github.io/vega/docs/transforms/force/).  `id` is integral to the way the tables work.  `path` always written by [linkpath](https://vega.github.io/vega/docs/transforms/linkpath/)[^caveat]

This means when I work in vega, names are [opinionated](https://www.imkylelambert.com/articles/opinionated-software)[^opinion]. Cool, now I know.  I will now try to do some abstraction at the *presentation* level rather than the *data* level...

----

[^caveat]: To be fair, not all names are the subject of such strong opinions.  For example, linkpath lets you set the field name to pull source & target from.  However, sometimes vega asserts its opinions strongly and there is no way to change them!

[^opinion]: The part I keyed off of in the linked article is "Direction over Choice".  Vega gives choice in a lot of areas, but in other cases it gives direction.  You are directed in how to handle names.  You can choose how you get colors.
