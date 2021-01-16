---
title: "Iterate over multiple list in a `for` loop"
date: 2020-07-08T11:45:14+09:00
draft: false
categories: [studylog]
tags: [tip,python,howto,itertools,list,loop]
---

`python` (python 3) 에서 다수의 list 가 있을때, 이 list 들을 한번의 loop 로 모두 순회하려면 다음과 같이 `itertools` 를 사용하면 됩니다.

```py
#!/usr/bin/python3

import itertools

list_a = ['1', '2', '3']
list_b = ['7', '8', '9']
list_c = ['a', 'b', 'c']

for v in itertools.chain(list_a, list_b, list_c):
    print(v, end =' ')  # 라인끝 처리를 공백(' ') 으로 대체
```


출력은 다음과 같습니다.

```
1 2 3 7 8 9 a b c
```

참 쉽쪙? (*￣3￣)╭


```py
# note. print 함수에 end 파라메터는 다음과 같이 정의되어 있음
print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)
```
