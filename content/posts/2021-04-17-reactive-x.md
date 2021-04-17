---
title: "Reactive X"
date: 2021-04-17
toc: false
tags: [java, reactivex, rx, lambda, presentation, code, tip]
marp: true
pagination: true
theme: gaia
draft: true
---

# 목표
* Rx 가 무엇인지 알아 봅니다.
* 왜 Rx 를 사용하는지 알아 봅니다.
* 동작 데모를 코드를 통해 알아 봅니다.

----

# ReactiveX


    "ReactiveX is a library for composing asynchronous and event-based programs 
    by using observable sequences.​

    It extends the observer pattern to support sequences of data and/or events 
    and adds operators that allow you to compose sequences together declaratively
    while abstracting away concerns about things like low-level threading,
    synchronization, thread-safety, concurrent data structures, 
    and non-blocking I/O."​

                                                        - From ReactiveX - Intro

----

## Observer Pattern

![height:400px](/post/resources/_gen/images/W3sDesign_Observer_Design_Pattern_UML.jpg)

----

## Observer Pattern

```javascript
var Subject = {
  _state: 0,
  _observers: [],
  attach: function(observer) { this._observers.push(observer); },
  getState: function() { return this._state; },
  setState: function(value) {
      this._state = value;
      for (var i = 0; i < this._observers.length; i++) { this._observers[i].update(this); }
  }
};

var Observer = {
  update: function(subject) { console.log(subject.getState()); }
};

Subject.attach(Observer);
Subject.setState(10);
```

----

# Imperative vs Declarative paradigm

* Imperative
  * uses statements that change a program's state
* Declarative
  * expresses the logic of a computation **without describing its control flow**

----

# Java 8 Lambda

```java
interface StringFunction {
    String apply(String str);
}

public void main() {
    StringFunction exclaim = (s) -> s + "!";
    System.out.println(exclaim.apply("Hello"));

    UnaryOperator<String> ask = (String s) -> s+"?";
    System.out.println(ask.apply("Hello"));
}
```

----

* Functional interfaces provide target types for lambda expressions and method references.

| Interface | Function Descriptor | Abstract method |
|:----------|:--------------------|:----------------|
| Predicate<**T**>      | (**T**) -> boolean | boolean test(**T** t); |
| Consumer<**T**>       | (**T**) -> void    | void accept(**T** t);  |
| Function<**T**,__R__> | (**T**) -> __R__   | __R__ apply(**T** t);  |
| Supplier<**T**>       | () -> **T**        | **T** get();           |
| UnaryOperator<**T**>  | (**T**) -> **T**   | **T** apply(**T** t);  |

* https://docs.oracle.com/javase/8/docs/api/index.html

[comment]: <> ( 출처: https://tourspace.tistory.com/6 [투덜이의 리얼 블로그] )


----

# Observable

* Cold Observable
  * Data driven : 파일내용 읽기
* Hot Observable
  * Event driven : 마우스 클릭 이벤트 받기

----

# Cold Observable

----

# Hot Observable


---



# References

* [ReactiveX](http://reactivex.io/)
* [투덜이의 리얼 블로그](https://tourspace.tistory.com/6)
* [Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern)
