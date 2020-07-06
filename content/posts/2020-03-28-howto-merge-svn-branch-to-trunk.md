---
title: "How to merge branch to trunk"
date: 2020-03-28T12:34:57+09:00
draft: false
categories: [howtolog]
tags: [svn, merge, commandline]
---

To merge branch to trunk, remember following command.

```cmd
> svn switch http://trunk_url
> svn merge http:://branch_url
> svn commit -m "blah blah..."
```

<!-- more -->

1. Change current workspace to trunk.
2. Merge branch's changelog to current workspace (trunk).
3. Commit it with some commit message.
