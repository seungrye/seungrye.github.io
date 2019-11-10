---
title: "SHA256 has File via Python"
date: 2019-11-10T19:34:25+09:00
categories: [howtolog]
tags: [python,howto,tip,sha256,hash]
draft: false
---

When check hash code of file with python.

<!--more-->

```python
import glob
import os
import hashlib

for filename in glob.iglob(os.path.join('.', '*.rar')):
    sha256_hash = hashlib.sha256()

    print("working on {file}".format(file=filename))
	    with open(filename, "rb") as f:
	        for byte_block in iter(lambda: f.read(1024 * 64), b""):
	            sha256_hash.update(byte_block)
	    
    print(">>>> hash : {hash}".format(file=filename, hash=sha256_hash.hexdigest()))
```

Omitted the detailed description.

