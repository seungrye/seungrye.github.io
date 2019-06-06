---
title: "Git 의 원격 저장소의 브랜치 삭제하기"
date: 2019-06-02T21:35:23+09:00
categories: [howtolog]
tags: [git,git-push,howto]
draft: false
---

git 의 원격 저장소에 있는 branch 를 삭제하는 방법을 알아 봅시다.

<!--more-->

현재 remote 브랜치 목록을 확인해 봅시다.

{{<highlight bash>}}
$ git branch -r
  origin/1-cmake
  origin/5-asio-read-std-vector-buffer-append
  origin/HEAD -> origin/master
{{</highlight>}}

 ----

 remote 브랜치를 삭제합니다.

{{<highlight bash>}}
$ git push origin --delete 5-asio-read-std-vector-buffer-append 
To www.enjian.ml:seungrye/teach-yourself-cpp.git
 - [deleted]         5-asio-read-std-vector-buffer-append
{{</highlight>}}

 ----

현재 remote 브랜치 목록을 확인해 봅시다.

{{<highlight bash>}}
$ git branch -r
  origin/1-cmake
  origin/HEAD -> origin/master
{{</highlight>}}
