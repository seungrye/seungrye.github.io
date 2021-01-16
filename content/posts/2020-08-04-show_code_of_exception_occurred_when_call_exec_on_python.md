---
title: "Show code of exception occurred when call exec on python"
date: 2020-08-04T15:29:30+09:00
draft: false
toc: false
images:
tags: [python, exec, Exception, line, code, tip]
---

python 에서 `exec` 함수를 통해 임의의 코드를 수행시킬때, 예외가 발생했다면, 예외가 발생한 부분을 정확히 알고 싶을때 어떻게 할 수 있을까요@.@?

다음과 같이 traceback 을 서식화 하여 출력할때, `File "<string>", line 4` 처럼 출력되어서 정확한 위치를 알기 귀찮습니다.

```py
import traceback

codes = []

codes.append("""\
print("hello", end='')
print(" ", end='')
print("world")
""")

codes.append("""\
def add(a, b):
    return a + b

print('2 + 3 => {}'.format(add(2, 3)))
r = addd(4, 5)
print('4 + 5 => {}'.format(r))
""")

try:
    for code in codes:
        exec(code)
except Exception as _:
    print(traceback.format_exc())
```

위 코드를 실행한 결과는 다음과 같습니다.
```txt
hello world
2 + 3 => 5
Traceback (most recent call last):
  File "<stdin>", line 3, in <module>
  File "<string>", line 5, in <module>
NameError: name 'addd' is not defined
```

코드가 짧을 경우, 문제가 발생한 부분을 짚어내기 어렵지 않지만, 수십, 수백라인의 코드를 `exec` 호출을 통해서 실행중 예외가 발생한 경우 문제가 발생한 코드를 찾아내는것은 쉽지 않을 수 있습니다.

이를 쉽게 처리하기 위해서, `code` 에서 문제가 발생한 라인의 코드를 추출하면 됩니다.

말을 길게 했지만, 단순히 다음과 같이 처리하면 됩니다. :P

```py
import traceback

codes = []

codes.append("""\
print("hello", end='')
print(" ", end='')
print("world")
""")

codes.append("""\
def add(a, b):
    return a + b

print('2 + 3 => {}'.format(add(2, 3)))
r = addd(4, 5)
print('4 + 5 => {}'.format(r))
""")

execode = None

try:
    for code in codes:
        execode = code  # save current running code
        exec(code)
except Exception as e:
    tbf = traceback.format_exc()
    print(tbf)

    if 'File \"<string>\"' in tbf:  # find `File "<string>"` 
        found = re.search(r'File "<string>", line (\d+)', tbf)  # search exception occurred line
        if found is not None:
            lineno = int(found.group(1))
            print(
                "<string> => {{\n" + 
                "\n".join(['{:03d} {}'.format(i+1, x) for (i, x) in enumerate(execode.splitlines()) if i >= (lineno-2) and i < (lineno+1)]) + 
                "\n}}"
            )
```

위와 같이 하면, 다음처럼 예외가 발생한 라인 근처의 코드를 포함하여 출력합니다. :)

```txt
hello world
2 + 3 => 5
Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
  File "<string>", line 5, in <module>
NameError: name 'addd' is not defined

<string> => {{
004 print('2 + 3 => {}'.format(add(2, 3)))
005 r = addd(4, 5)
006 print('4 + 5 => {}'.format(r))
}}
```

쓸만한진 모르겠지만, 나름 나쁘진 않지 않아영@.@?
