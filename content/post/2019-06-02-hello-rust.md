---
title: "Hello Rust"
subtitle: "Hello in rust with cargo"
date: 2019-06-02T12:37:17+09:00
categories: [studylog]
tags: [rust,cargo]
draft: false
---

rust lang를 통해 'Hello World' 출력하는 예제를 작성해 봅니다.

인터넷에서 쉽게 찾을수 있는 내용이고, 이미 해봤던 내용이지만, 
정리차원에서 작성합니다.

<!--more-->

### Cargo 를 통해서 프로젝트 생성

{{<highlight shell>}}
$ cargo new hello_rust
    Created binary (application) `hello_rust` package

$ ls
hello_rust/

$ tree hello_rust 
hello_rust
├── Cargo.toml
└── src
    └── main.rs

1 directory, 2 files

$ cat hello_rust/Cargo.toml 
[package]
name = "hello_rust"
version = "0.1.0"
authors = ["Enji <blah@blah.blah>"]
edition = "2018"

[dependencies]

$
{{</highlight>}}

----

### 코드 수정 

변수에 문자열을 할당후, println! 을 통해서 포멧된 문자열을 출력하도록 처리.

{{<highlight rust>}}
 fn main() {
-    println!("Hello, world!");
+    let hello = String::from("Hello, rust!");
+    println!("{}", hello);
 }
{{</highlight>}}

* ref : https://doc.rust-lang.org/std/string/struct.String.html

----

### 빌드

의존성 문제등을 확인하는 `cargo-check` 를 통해서 문제가 없는지 먼저 확인합니다.
이는 `cargo-build` 보다 훨씬 빠르게 명령이 완료되기 때문제 권장됩니다.

{{<highlight shell>}}
$ cargo check
    Checking hello_rust v0.1.0 (/tmp/hello_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 0.16s

$ cargo build       
   Compiling hello_rust v0.1.0 (/tmp/hello_rust)
    Finished dev [unoptimized + debuginfo] target(s) in 0.46s
{{</highlight>}}

----

### 실행

`cargo-run` 을 통해서 빌드 및 실행해 볼수 있습니다.
혹은 빌드된 output 파일을 직접 실행해 볼수도 있습니다.

{{<highlight shell>}}
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.02s
     Running `target/debug/hello_rust`
Hello, rust!

$ ls 
Cargo.lock  Cargo.toml	src/  target/
$ ./target/debug/hello_rust 
Hello, rust!
{{</highlight>}}

