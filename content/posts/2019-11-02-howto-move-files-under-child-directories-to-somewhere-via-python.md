---
title: "Howto Move Files Under Child Directories to Somewhere via Python"
date: 2019-11-02T02:13:42+09:00
draft: false
tags: [python, howto, tip]
---

When I need to move all `.zip` files to `/sharedfolders`, how should I do?

<!--more-->

```
/sharedfolders/Archives/A/A.zip
/sharedfolders/Archives/A/B/AB.zip
/sharedfolders/Archives/B/B.zip
/sharedfolders/Archives/A/C/C.zip
/sharedfolders/Archives/A/D.zip
... (hundreds of subdirectories)

----

$ pwd
/sharedfolders
```

Manually move file is not a good solution :P
Do it with python.

```
import os
import glob
import shutil

path = """/sharedfolders/Archives"""
files = [f for f in glob.glob(path + "/**/*.zip", recursive=True)]
for file in files:
	file_name = os.path.basename(file)
	shutil.move(file, path+"/"+file_name)
```
