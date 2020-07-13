---
title: "Howto list shared libraries that object file uses under MacOS commandline"
date: 2020-07-13T19:24:15+09:00
draft: true
toc: false
categories: 
  - studylog
tags: 
  - tip
  - otool
  - macos
  - osx
  - commandline
---

MacOS 상에서, 실행파일이 사용하는 라이브러리 목록을 알고 싶을때 다음과 같이 otool 명령을 사용하여 확인할수 있습니다.

```sh
HackCat:~ Songsangjun$ otool -L /bin/ls

/bin/ls:
/usr/lib/libutil.dylib (compatibility version 1.0.0, current version 1.0.0)
/usr/lib/libncurses.5.4.dylib (compatibility version 5.4.0, current version 5.4.0)
/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1225.1.1)
```

위 예제는 [s0ngsari](https://s0ngsari.tistory.com/entry/otool-object-tool) 에서 발췌했습니다.
개인적으로 MacOS 를 사용하고 있지 않아서, 직접 명령을 치고, 출력 내용을 긁어올 수 없었습니다. o(TヘTo)
