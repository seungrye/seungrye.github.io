---
title: "Git 의 원격 저장소 주소 변경하기"
date: 2019-06-02T20:21:46+09:00
categories: [howtolog]
tags: [git,git-fetch,git-remote,howto]
draft: false
---

git 의 원격 저장소 주소가 변경되었을떄 대처 방법을 알아 봅시다. 

<!--more-->

현재 `git-fetch` 했을때 발생하는 에러
{{<highlight shell>}}
$ git fetch
fatal: repository 'http://handmade.iptime.org/seungrye/teach-yourself-cpp.git/' not found
{{</highlight>}}

----

현재 repo 의 remote 주소
{{<highlight shell>}}
$ git remote -v
origin	http://handmade.iptime.org/seungrye/teach-yourself-cpp.git (fetch)
origin	http://handmade.iptime.org/seungrye/teach-yourself-cpp.git (push)
{{</highlight>}}

----

remote 변경
{{<highlight shell>}}
$ git remote set-url origin git@www.enjian.ml:seungrye/teach-yourself-cpp.git 
{{</highlight>}}

----

변경된 remote 확인
{{<highlight shell>}}
$ git remote -v
origin	git@www.enjian.ml:seungrye/teach-yourself-cpp.git (fetch)
origin	git@www.enjian.ml:seungrye/teach-yourself-cpp.git (push)
{{</highlight>}}

----

현재 `git-fetch` 했을때 결과
{{<highlight shell>}}
$ git fetch
remote: Enumerating objects: 28, done.
remote: Counting objects: 100% (28/28), done.
remote: Compressing objects: 100% (17/17), done.
remote: Total 21 (delta 12), reused 2 (delta 2)
오브젝트 묶음 푸는 중: 100% (21/21), 완료.
www.enjian.ml:seungrye/teach-yourself-cpp URL에서
 * [새로운 브랜치]   5-asio-read-std-vector-buffer-append -> origin/5-asio-read-std-vector-buffer-append
{{</highlight>}}

