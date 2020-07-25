---
title: "Initialize nested dict on Python"
date: 2020-07-21T10:37:57+09:00
authors: ["안승례"]

categories: [studylog]
tags: [tip, python, dict, collections, defaultdict, perl]

isCJKLanguage: true
draft: false
toc: false
---

```perl
#!/usr/bin/perl

my %dict_ = ();

$dict->{'hello'}->{'world'} = 'PERL';

print "dict_ : $dict \n";
print "hello : $dict->{'hello'} \n";
print "world : $dict->{'hello'}->{'world'} \n";
```

출력 결과는 다음과 같습니다.

```text
dict_ : HASH(0x5587f28af740) 
hello : HASH(0x5587f28c7530) 
world : PERL 
```

파이썬에서 동일하게 코드를 구성하면 어떻게 동작할까영@.@?

```py
#!/usr/bin/python3

dict_ = {}

dict_['hello']['world'] = 'PYTHON'

print("dict_ : ", dict_)
print("hello : ", dict_['hello'])
print("world : ", dict_['hello']['world'])
```

출력 결과는 다음과 같습니다.
```text
Traceback (most recent call last):
  File "./Playground/file0.py", line 5, in <module>
      dict_['hello']['world'] = 'PYTHON'
      KeyError: 'hello'
```
Key 가 없을 경우, Access 에러가 발생합니다.

PERL 과 유사하게 동작하도록 할수는 없을까영@.@?

다음과 같이 하면 됩니다.

```py
#!/usr/bin/python3

from collections import defaultdict

nested = lambda: defaultdict(nested) # nested dictionary factory

dict_ = nested()

dict_['hello']['world'] = 'PYTHON'

print("dict_ : ", dict_)
print("hello : ", dict_['hello'])
print("world : ", dict_['hello']['world'])
```

출력 결과는 다음과 같습니다.

```text
dict_ :  defaultdict(<function <lambda> at 0x7f311f8851f0>, {'hello': defaultdict(<function <lambda> at 0x7f311f8851f0>, {'world': 'PYTHON'})})
hello :  defaultdict(<function <lambda> at 0x7f311f8851f0>, {'world': 'PYTHON'})
world :  PYTHON
```

Key check 나 length check 는 일반적인 dictionary 사용과 같습니다.

```py
#!/usr/bin/python3

from collections import defaultdict

nested = lambda: defaultdict(nested) # nested dictionary factory

dict_1 = nested()  # empty dict
dict_2 = {}  # empty dict

if 'key' in dict_1:
    print('nested dict has a key')
else:
    print('nested dict has no key')

if 'key' in dict_2:
    print('dict has a key')
else:
    print('dict has no key')

print('length of nested dict => ', len(dict_1))
print('length of dict => ', len(dict_2))
```

출력 내용은 다음과 같습니다.

```text
nested dict has no key
dict has no key
length of nested dict =>  0
length of dict =>  0
```

위 내용은 [StackOverflow](https://stackoverflow.com/a/23036575) 내용을 참고하여 작성했습니다.

사실, 나중에 내가 보려고 작성함. (. ❛ ᴗ ❛.)

