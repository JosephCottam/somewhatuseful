---
teaser: Some running notes on how I'm making this blog
layout: page
---
I write this blog in Obsidian and let github-pages generate the actual website using Jekyll.  You browse the source repo if you like. 

Here are some things I learned-the-hard-way about this workflow....

### The homepage:
- When I update a post, I (mostly) leave something at the location of the original page.  It is just a re-directing page but I didn't want them listed in my homepage list anymore...only the most-up-to-date.  I added an `in_listing` property to just those posts and set it to false.  EXCEPT, it took a very long time to discover that Obsidian doesn't have boolean-typed properties and instead of checking for `false` I needed to check for `'false'` in the homepage logic to get the filtering to work correctly. 
### Using Mermaid:
- I tried the 'jekyll-spaceship' plugin in `_config.yml` but this requires a more complex deploy on github pages (since the default [only allows specific plugins](https://pages.github.com/versions/)).
- Setting the header/footer scripts in `_include` worked much better.  I used [these scripts](https://jackgruber.github.io/2021-05-09-Embed-Mermaid-in-Jekyll-without-plugin/).  You also need to add `mermaid: true` as a property to the pages that use it.
- Using mathJax followed scripts on https://stackoverflow.com/questions/34347818/using-mathjax-on-a-github-page

### On posts:
- Post filenames MUST NOT have spaces in them. I had to work this out before I really understood how to debug a github workflow, so maybe its obvious when you know that.  It was very NOT obvious while I worked through it.