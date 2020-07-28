---
title: "NestedDefaultOrderedDict on Python"
date: 2020-07-28T13:53:25+09:00
draft: false
toc: false
images:
tags: [dict, OrderedDict, python, class, code, source]
---

[python 3.7 부터는 dict 의 order 가 보장](https://mail.python.org/pipermail/python-dev/2017-December/151283.html)되지만, 낮은 버전의 python 에서는 order 이 보장되지 않을 수 있습니다. (3.5 에서는 order 가 보장되지 않음. OrderedDict 를 사용해야 함.)

그리고, 다음과 같이 nested dict 를 초기화 하기위해서는 `nested = lambda: collections.defaultdict(nested)` 와 같은 선언 해야 했습니다.

```py
# a = []
a = nested()
a['a']['b'] = 'c'
```

nested dict 같은 초기화도 되면서, OrderedDict 처럼 order 보장이 되는 dict 구현체는 없나? 같은 필요가 생기면서 찾다보니 [stackoverflow 답변](https://stackoverflow.com/questions/6190331/how-to-implement-an-ordered-default-dict)중 구현체가 있어서 기록겸 내용을 남겨둡니다.

```py
from collections import OrderedDict, Callable

class DefaultOrderedDict(OrderedDict):
    # Source: http://stackoverflow.com/a/6190500/562769
    def __init__(self, default_factory=None, *a, **kw):
        if (default_factory is not None and
           not isinstance(default_factory, Callable)):
            raise TypeError('first argument must be callable')
        OrderedDict.__init__(self, *a, **kw)
        self.default_factory = default_factory

    def __getitem__(self, key):
        try:
            return OrderedDict.__getitem__(self, key)
        except KeyError:
            return self.__missing__(key)

    def __missing__(self, key):
        if self.default_factory is None:
            raise KeyError(key)
        self[key] = value = self.default_factory()
        return value

    def __reduce__(self):
        if self.default_factory is None:
            args = tuple()
        else:
            args = self.default_factory,
        return type(self), args, None, None, self.items()

    def copy(self):
        return self.__copy__()

    def __copy__(self):
        return type(self)(self.default_factory, self)

    def __deepcopy__(self, memo):
        import copy
        return type(self)(self.default_factory,
                          copy.deepcopy(self.items()))

    def __repr__(self):
        return 'OrderedDefaultDict(%s, %s)' % (self.default_factory,
                                               OrderedDict.__repr__(self))

def NestedDict():
    return DefaultOrderedDict(NestedDict)
```

사용법은 생각했던 대로, 다음과 같이 쓰면 됩니다.

```py
a = NestedDict()
a['a']['b'] = 'c'
```

참..쉽죠@.@?


