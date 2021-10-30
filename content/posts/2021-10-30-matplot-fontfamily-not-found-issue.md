---
title: "Font family not found issue on matplot"
date: 2021-10-30T15:25:05+00:00
draft: false
categories: [howtolog]
tags: [tip,python,matplot,font]
---

파이썬으로 matplot 를 사용할때, 한글 폰트가 다음과 유사한 에러로 정상 출력되지 않는 현상이 발생하는 경우가 있습니다.

```sh
$ python3 ~/hello.py 
findfont: Font family ['NanumGothic'] not found. Falling back to DejaVu Sans.
/home/seungrye/.local/lib/python3.9/site-packages/matplotlib/backends/backend_agg.py:240: RuntimeWarning: Glyph 49340 missing from current font.
  font.set_text(s, 0.0, flags=flags)
...
```

사용한 코드는 다음과 같습니다.
```python
import matplotlib.pyplot as plt

# Pie chart, where the slices will be ordered and plotted counter-clockwise:
labels = '개구리', '고양이', '강아지', '비둘기'
sizes = [15, 30, 45, 10]
explode = (0, 0.1, 0, 0)  # only "explode" the 2nd slice (i.e. 'Hogs')

plt.rc('font', family='NanumGothic')

fig1, ax1 = plt.subplots()
ax1.pie(sizes, explode=explode, labels=labels, autopct='%1.1f%%',
        shadow=True, startangle=90)
ax1.axis('equal')  # Equal aspect ratio ensures that pie is drawn as a circle.

plt.show()
```

이는 matplot 의 font 설정이 적절한 폰트를 찾지 못해 발생하는 현상이며, 제 경우는 python 및 matplot 패키지가 설치된 이후 하나글 폰트를 설치해서, matplot 의 폰트 정보가 갱신되지 않은것이 이유로 보입니다.

폰트 정보가 담겨있는 파일은 다음의 경로에서 찾을 수 있습니다.
```sh
$ pwd
/home/seungrye/.cache/matplotlib
$ ls
fontlist-v330.json
```

해당 파일을 삭제 (안전을 위해 백업) 후, 코드를 다시 돌려보면 정상적으로 한글이 출력되는것을 확인 할 수 있습니다.
```sh
$ mv fontlist-v330.json fontlist-v330.json_
$ python3 ~/hello.py
$ ls
fontlist-v330.json fontlist-v330.json_
```

### Tip
참고로 폰트 목록은 다음과 같이 확인할 수 있습니다.
```python
from matplotlib import font_manager

manager.fontManager.ttflist:
for i in font_manager.fontManager.ttflist:
    print(i.name, i.fname)
```

## Ref
* [Basic pie chart](https://matplotlib.org/stable/gallery/pie_and_polar_charts/pie_features.html)
* https://studioplug.tistory.com/333