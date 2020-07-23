---
title: "Convert nested dict to ordered dict on Python"
date: 2020-07-23T16:56:10+09:00
draft: true
toc: false
images:
tags: [collections, OrderedDict, dict, python, tip, code]
---

다음과 같이 충첩된 dict 가 있다고 할때,

```py
dict_ = {
    'a': 1,
    'b': [2, 3, 4],
    'c': {
            'd': 5
    },
    'd': '6789'
}
```

ordered dict 로 변환하기 위해서는 다음과 같이 재귀 함수를 만들어서 변환할 수 있다.

```py
from collections import OrderedDict
def to_ordereddict(item):
    od = OrderedDict()
    for k, v in sorted(item.items()):
            if isinstance(v, dict):
                    od[k] = to_ordereddict(v)
            else:
                    od[k] = v
    return od
```

결과는 다음과 같다.

```py
>>> dict_ = {
...     'a': 1,
...     'b': [2, 3, 4],
...     'c': {
...             'd': 5
...     },
...     'd': '6789'
... }
>>> odict_ = to_ordereddict(dict_)
>>> odict_
OrderedDict([('a', 1), ('b', [2, 3, 4]), ('c', OrderedDict([('d', 5)])), ('d', '6789')])
```

괜찮죠?
