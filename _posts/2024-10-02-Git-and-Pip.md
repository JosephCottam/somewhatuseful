---
title: Git Repos for Pip install
teaser: Personal packages are useful
layout: post
date: 2024-10-02
---
I use a small collection of python utilities I developed across several projects. Today, I started an experiment sharing those utilities between projects using pip to install a utilities package from a git repo.

I used to just copy my utilities and modify them in place to my hearts content BUT they have largely stabilized (so they change slowly) and most changes are now bug-fixes (so they need to be propagated to other projects). 

For example, I have a tool that lets me edit JSON files using python lists and dictionaries in a path-like syntax.  I find it more convenient than using [jsonpath ](https://en.wikipedia.org/wiki/JSONPath)for queries AND it lets me edit things in a way similar to [glom](https://glom.readthedocs.io/en/stable/) but covering a very important case that [I could not get glom to do](https://stackoverflow.com/questions/66377718/glom-assign-using-a-check-filter).  In the process of making my edit-tool, I DID NOT cover a lot of cases that glom does...so this is very much a tool-for-me...but it is a tool I'm using in 5 different projects now.

Pretending my tool is called `json_tool` and pretending that its git repo url is `ssh://git@fancyserver.org:somewhatuseful/json_tool.git`...

The repo structure follows the current practice of a `pyproject.toml` file and the package source files in a directory with the same name as the project (`json_tool` today).
```
root
├─ json_tool
|  ├─ __init__.py
|  ├─ find.py 
|  └─ edit.py
└─ pyproject.toml
```

The package source directory (and all other directories under it...if they existed) has an `__init__.py` to indicate they are [regular packages](https://docs.python.org/3/reference/import.html#regular-packages) (I did not test with namespace packages).

To install, `pip install git+<normal-repo-checkout-url>`.  If the package name (specified as `name=<package-name>` in `pyproject.toml`) does not match repository name, you *might* use  at the end of the URL used in `pip install git+<URL>#egg=<package-name>` for the install. (This fragment fixed a problem...that I couldn't reproduce using the fragment-free URL after it was fixed once.)

To update after bug-fixes, I use `pip install --force-reinstall --no-deps git+<normal-repo-checkout-url>`.  This forces the re-install of my custom package, but does not stomp on the other dependencies in my environment.  (The `--no-deps` might be removed in the future as I learn more about proper control of dependencies in `pyproject.toml` or if I-learn-how/conda-gains-the-ability to install from git in a way that isn't just calling pip under the covers...)