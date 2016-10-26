---
date: "2016-10-25T23:10:04+01:00"
draft: false
title: "Nimble: running task before/after install "
layout: post
tags: ["nim", "nimble", "hook"]
---

When you install a package through [nimble](https://github.com/nim-lang/nimble), it performs an action called `install`.
If you are the author of a nimble package, you may want to have nimble run a task before or after your package is installed, for example
to `git clone` a dependency, `echo` some debug information or whatever.
To do this, you can make use of two hooks that nimble provides when using the `nimscript` format:

- `before`: to do something before installing
- `after`: to do something after installing

They are normally used with [tasks](https://github.com/nim-lang/nimble#the-new-nimscript-format), but they work
with `install` too.

Example:

```
before install:
  echo("Cloning repo..")
  exec("git clone <repolink>")
```