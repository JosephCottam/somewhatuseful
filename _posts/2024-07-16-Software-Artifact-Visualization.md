---
source: https://blog.sbensu.com/posts/demand-for-visual-programming/
title: Software Artifact Visualization
date: 2024-07-16
teaser: Visualizations to assist program undersanding
layotu: post
---
I recently read this blog post on "[visual programming](https://blog.sbensu.com/posts/demand-for-visual-programming/)".  The authors start by deriding syntax-based visualization (which is basically fair) and go on to argue for context and execution artifacts to support traditional programming.  

The article is called "visual programming" but it only treats construction-of-a-program-via-visual-means superficially.  The only type of visual programming examined is basically alternate-syntax.  The issues that they point out are fair, but it not really a look at visual programming.  

That being said, their argument to *support* traditional programming through visuals caught my attention.
## Context
How is this codebase structured now and how has it evolved?  What has been retained?  What is it related to?  Examples used include:
- SourceTrail: Sort of an automated UML-class diagram made automatically.  The strength the author points out is that it is presented NEXT TO the source code, so it is truly **context** for source editing.  Used in that way, and automatically generated, I think this is a good idea.
- Code retention diagram: How long has this been around?  Though the shelf-life of code itself is not one of my particular interests, looking at historical information in this way may help understand who to talk to or how careful to be with some code regions. 
- Computer-network/service-topology: For slices of a network, I have found these valuable in my work.  For small networks they solve a navigation problem that is hard to trace out the json files they come from: What is interconnected? (I don't think they scale to large networks well, I've never tried!)
- Treemaps of source code:  This is at the bottom of my list because I don't find these diagrams terribly readable or compelling.  The observations being made are usually achievable by sorting a data table appropriately and reading the top 3(+/-2) lines.  

## Execution Artifacts

How does the codebase achieve its intended result?  NOT the result itself, but the process that led up to that result. Examples used included: 
- protocol visualizations
- "swim-lane" diagrams
- State machines
- Memory layout (but not global, for select data structures)
- Memory traces (but not global, for selected values)
- Value ownership

I think this is an important area.  The amount time/mental-effort it often takes to move program-execution trace information to a visual often exceeds the time/mental-effort to use a step debugger (except in parallel/distributed systems).  Reducing the effort to cross this bridge is a recurring daydream...but then I need to go do my actual job.
