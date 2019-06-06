---
title: "Rust 로 작성된 Read File 코드 이해하기"
date: 2019-06-06T17:00:12+09:00
categories: [studylog]
tags: [rust, code, explain]
draft: true
comments: true
---

rust 언어를 통해서 파일을 라인단위로 읽는 프로그램 코드에서 쉽게 
이해되지 않는 부분에 대해 살펴보도록 합니다.

<!--more-->

```rust
use std::fs::File;
use std::io::{BufRead, BufReader, Result};

fn main() -> Result<()> {
	let file = File::open("file.txt")?;
	for line in BufReader::new(file).lines() {
		println!("{}", line?);
	}

	Ok(())
}
```

## `?` 연산자
https://m4rw3r.github.io/rust-questionmark-operator 내용에 따르면, 
`?` 연산자는 `a unary suffix operator which can be placed on an 
expression to unwrap the value on the left hand side of '?' while 
propagating any error through an early return` 라고 합니다.

에러를 전파하는 역할을 하는 연산자라고 하니, 위 코드에서 임의로 
에러를 발생시켜 보면 다음과 같은 출력을 얻을 수 있습니다.

```diff
-    let file = File::open("file.txt")?;
+    let file = File::open("file.text")?;
```

