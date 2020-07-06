---
title: "How to replace string with regex on vscode"
date: 2020-07-06T18:45:00+09:00
draft: false
categories: [howtolog]
tags: [vscode, tip, regex, replace]
---

vscode 에서 다음과 같은 문자열을 치환 할때, regex 를 사용해서 치환하는 방법을 알아 봅시다.

`$self->{item_name};` => `self.item_name`

<!-- more -->

![ReplaceViaRegex](/posts/resources/_gen/images/2020-07-06 185226.png)

* () : 그룹. 추후 $1, $2 등의 변수처럼 사용 가능.
* \w+ : 1글자 이상.
* $ 및, {, } 등은 특수문자 취급되므로, excape (\) 처리함.

참...쉽죠? o(*￣▽￣*)ブ
