---
title: "Git 의 Tracking branch 변경하기"
date: 2021-01-16T19:08:20+09:00
categories: [howtolog]
tags: [git,git-branch,howto]
draft: false
---

git 이 tracking 하는 branch 가 잘못되어 있을때, 수정하는 방법을 알아 봅시다.

<!--more-->

현재 `git pull` 했을때 발생하는 에러
{{<highlight bash>}}
$ git pull
Your configuration specifies to merge with the ref 'refs/heads/master'
from the remote, but no such ref was fetched.
{{</highlight>}}

----
현재 working directory repo 의 branch 이름
{{<highlight bash>}}
$ git branch
* master
{{</highlight>}}

----

현재 master 브랜치가 tracking 해야하는 remote repo 의 branch 이름
{{<highlight bash>}}
$ git branch -r
origin/main
{{</highlight>}}

----

tracking branch 변경
{{<highlight bash>}}
$ git branch master --set-upstream-to origin/main
{{</highlight>}}

Note. 기억에 의존해 적는거라 중간 과정 (출력 내용) 및 git pull 정상 동작시의 (출력) 내용이 빠져있습니다. :(
