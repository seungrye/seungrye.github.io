---
title: "No default toolchain configured error when run rustc"
date: 2019-06-17T20:33:49+09:00
draft: false
categories: [studylog]
tags: [tip,rust,roolchain,rustup,error,howto,fix]
---

How to fix the `error: no default toolchain configured` error on rust.

<!--more-->

I got following error when run rls or rustc

```shell
$ rustc
error: no default toolchain configured
```

The answer is fairly simple. just instll stable and set default. that's it.

```shell
$ rustup install stable
info: syncing channel updates for 'stable-x86_64-unknown-linux-gnu'
info: latest update on 2019-05-23, rust version 1.35.0 (3c235d560 2019-05-20)
info: downloading component 'rustc'
 88.4 MiB /  88.4 MiB (100 %)   7.8 MiB/s in 12s ETA:  0s
info: downloading component 'rust-std'
 59.1 MiB /  59.1 MiB (100 %)   6.9 MiB/s in  8s ETA:  0s
info: downloading component 'cargo'
info: downloading component 'rust-docs'
 10.4 MiB /  10.4 MiB (100 %)   6.5 MiB/s in  1s ETA:  0s
info: installing component 'rustc'
 88.4 MiB /  88.4 MiB (100 %)  14.4 MiB/s in  6s ETA:  0s
info: installing component 'rust-std'
 59.1 MiB /  59.1 MiB (100 %)  17.3 MiB/s in  3s ETA:  0s
info: installing component 'cargo'
info: installing component 'rust-docs'
 10.4 MiB /  10.4 MiB (100 %)   6.9 MiB/s in  1s ETA:  0s

  stable-x86_64-unknown-linux-gnu installed - rustc 1.35.0 (3c235d560 2019-05-20)

$ rustup default stable
info: using existing install for 'stable-x86_64-unknown-linux-gnu'
info: default toolchain set to 'stable-x86_64-unknown-linux-gnu'

  stable-x86_64-unknown-linux-gnu unchanged - rustc 1.35.0 (3c235d560 2019-05-20)

$ rustc
Usage: rustc [OPTIONS] INPUT

Options:
    -h, --help          Display this message
```

simple. isn't it?

