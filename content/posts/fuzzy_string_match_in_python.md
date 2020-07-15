---
title: "Fuzzy string match in Python"
date: 2020-07-15T19:15:37+09:00
authors: ["안승례"]

categories: []
tags: [tip, fuzzywuzzy, python, fuzzy, module]

isCJKLanguage: true
draft: true
toc: false
---

다음과 같이, 유사한 내용이 (순서가 다르게) 포함된 두개의 파일을 비교할때, 하나의 파일을 기준으로 내용을 정렬해 두면 비교하기 편할것 입니다.

```txt
name: fileA
1. Haste makes waste
2. A stitch in time saves nine
3. Ignorance is bliss
4. Mustn't cry over spilled milk.
5. You can catch more flies with honey than you can with vinegar.
```

```txt
name: fileB
Haste makes waste.
Ignorance is bliss.
A stitch in time saves nine.
Must not cry over spilled milk.
You can catch more flies with honey than you can with vinegar.
```

위 두 파일은, 내용이 거의 유사하지만, 동일하지는 않습니다. 이 경우, 단순히 string compare 만으로는 비교가 어렵습니다.
유사도를 확인할 수 있는 fuzzywuzzy 모듈을 사용하면, 어렵지 않게 해결할 수 있습니다.

[github](https://github.com/seatgeek/fuzzywuzzy) 에 적혀있는 설명대로, 다음과 같이 fuzzywuzzy 모듈을 설치할 수 있습니다.
```sh
$ pip3 install fuzzywuzzy
Collecting fuzzywuzzy
  Downloading https://files.pythonhosted.org/packages/43/ff/74f23998ad2f93b945c0309f825be92e04e0348e062026998b5eefef4c33/fuzzywuzzy-0.18.0-py2.py3-none-any.whl
  Installing collected packages: fuzzywuzzy
  Successfully installed fuzzywuzzy-0.18.0

$ pip3 install python-levenshtein
Collecting python-levenshtein
  Downloading https://files.pythonhosted.org/packages/42/a9/d1785c85ebf9b7dfacd08938dd028209c34a0ea3b1bcdb895208bd40a67d/python-Levenshtein-0.12.0.tar.gz (48kB)
  Installing collected packages: python-levenshtein
    Running setup.py install for python-levenshtein ... done
  Successfully installed python-levenshtein-0.12.0
```

`fuzzywuzzy` 는 설치에 문제가 없는데, `python-levenshtein` 의 경우, 설치에 문제가 있을수 있습니다. 
Note. 제 환경에서는, pip 로 설치시, 빌드가 진행됐는데 빌드 에러가 발생했었습니다. 보통은 컴파일러/라이브러리가 없는 경우일테니, 재주껏 에러를 해결해 봅시다. (；`)

다음과 같이, fileA 를 기준으로 fileB 를 정렬해서 fileB_ 로 생성해서, fileA 와 fileB_ 를 비교해 볼 수 있습니다.
```py
```

fileA 와 fileB 를 비교했을때
{image}

fileA 와 fileB_ 를 비교했을때
{image}

파일 내용이 수천줄이라면 도움이 꽤 될겁니다. :)
