---
title: "2019 06 12 Log Directory of Macos"
date: 2019-06-12T00:37:50+09:00
draft: true
categories: [studylog]
tags: [macos,osx,log,tip]
---

맥 App 에서는 로그 파일을 어디에 기록해야 할까?

<!--more-->

지금까지 $HOME/Library/Application\ Supports/&lt;App Name&gt; 에 놓으면 되지 않을까?
하고 생각했었는데, 바로 옆에 위치한 $HOME/Library/Logs/&lt;App Name&gt; 가 있었다.

어떤 장점이 있을까? [여기](https://github.com/pypa/pip/issues/182) 내용에 따르면 
Console.app 에서 로그를 볼수 있다고 한다.

```
OS X comes with a log viewer, Console.app, that looks for logs in ~/Library/Logs/, 
/Library/Logs/, and /var/log/ by default. 
```

해당 경로는 어떻게 접근할수 있을까? 
단순 무식하게 `~/Library/Logs/&lt;App Name&gt;/` 로 접근해도 되지만, 간지가 안나니까. 
좀더 있어보이는 방법을 사용해 보도록 하자.

```objc
```

예전엔 왜 몰랐을까? 라고 생각해보니, $HOME/Library/Logs 경로를 가리키는 enum 값이 없어서 였지 않을까 싶다. :P

