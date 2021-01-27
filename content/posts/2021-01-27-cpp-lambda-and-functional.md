---
title: "C++ 에서 Lambda 와 Functional Language feature"
date: 2021-01-27
draft: false
toc: false
tags: [c++, lambda, functional, syntax, presentation, code, tip]
marp: true
pagination: true
theme: gaia
---

# 목적
* 람다를 어떻게 '쓰는지' 에 대해 설명합니다.
* 람다가 '뭔지', '어디에' 쓰는지에 대해 설명하지 않습니다.
  * 스스로 찾아보도록 합시다.
  * 나도 모름 ¯\\_( ͡° ͜ʖ ͡°)_/¯

----

# C++ 에서 Lambda syntax

```
[=] ( ) mutable ->int { ... }
——— ——— ——————— ————— ———————
 1   2     3      4      5
```

1. Capture
2. Parameter 목록
3. Mutable specifier (Optional)
4. Return type (Optional)
5. 람다 본문

----

## E~~~asy Example

```c++
void main() {
    int a = 1;
    auto add = [=](int b)->int{ return a+b; };
    std::cout<<"1+2:"<<add(2);
}
```

출력

```
1+2:3
```

----

# Capture

* **[&]** : lambda 함수 외부 변수를 참조로 가져옴 (call-by-ref)
* **[=]** :  lambda 함수 외부 변수를 값으로 가져옴 (call-by-val)
* **[x]** : 변수 x 를 값으로 가져옴
* **[=, &x]** : lambda 함수 외부 변수를 값으로 가져오되, 변수 x 는 참조로 가져옴
* **[x, &y, &z]** : 변수 x 는 값으로, y, z 는 참조로 가져옴
----

# Capture Demo

```c++
    int x(5),y(6),z(7);
    int* p = &z;
    [=, x, &y, p](){
        printf("%d %d %d %d\n", x, y, z, *p);
        // x=15, z=17;  // call-by-val can not modified
        y=16, *p=27;    // p point to z
    }();
    printf("%d %d %d %d\n", x, y, z, *p);
```

출력

```
5 6 7 7
5 16 27 27
```

----

# Capture Quiz

```c++
    int a = 0;
    int* p = &a;
    [=, &p](){
        *p = 1;
        std::cout<<" inside lambda> a:"<<a<<", p:"<<*p<<std::endl;
    }();
    std::cout<<"outside lambda> a:"<<a<<", p:"<<*p<<std::endl;
```

출력

```
 inside lambda> a:?, p:?
outside lambda> a:?, p:?
```

----

# Parameter list

```c++
auto pplus = [](int a, int b){
    printf("%d+%d=%d\n",a, b, a+b);
};

pplus(2, 3);
```

출력
```
2+3=5
```

----

# Mutable specifier

```c++
int a=1, b=2,c=0;
[=](){c=a+b; std::cout<<"c : "<<c<<std::endl;}();
//출력
//> error: cannot assign to a variable captured by copy in a non-mutable lambda
//> [=](){c=a+b; std::cout<<"c : "<<c<<std::endl;}();
//>       ~^
```

```c++
int a=1, b=2,c=0;
[=]() mutable {c=a+b; std::cout<<"c : "<<c<<std::endl;}();
//출력
//> 3
```

----

# Return type

* 💦 Under construct

```c++

```

----

# Lambda body

* 이걸 굳이 설명할 필요가... (￣ ‘i ￣;)

![height:400px](./resources/_gen/images/3944540494575102317111-1315200466783997952.jpg)

----

# Lambda 의 유래 (?)
* Under construct 💦
----
# Functional language

* 함수를 1급 객체로 취급합니다.

## 1급 객체 (first class citizen) 란?

1. All items can be the actual parameters of functions
2. All items can be returned as results of functions
3. All items can be the subject of assignment statements
4. All items can be tested for equality. (💦)

Note. Primitive 변수는 어떤 언어에서나 1급 객체입니다.

----

## 1급 객체 on C++

```c++
auto plus2factory = [](std::function<int(int)> f)->std::function<int(int)>{
    return [=](int y)->int{ return f(y) + 2; };
};

auto plus1 = [](int x)->int{ return x+1; };
auto plus2 = plus2factory(plus1);
std::cout<<"5+1+2 => "<<plus2(5)<<std::endl;
// 출력
// 5+1+2 => 8
```

```
// std::functional( return_type <params, ..> ) // Example
std::functional(int<char,float,void*>) fn = [](char a, float b, void* c)->int{
    printf("%c, %f, %p", a, b, c);
};
```

----

# References
* [MSDN C++의 람다 식](https://docs.microsoft.com/ko-kr/cpp/cpp/lambda-expressions-in-cpp?view=msvc-160)
* [MSDN 람다식의 예](https://docs.microsoft.com/ko-kr/cpp/cpp/examples-of-lambda-expressions?view=msvc-160)
* [First-class citizen](https://en.wikipedia.org/wiki/First-class_citizen)
* [함수형 프로그래밍 요약](https://velog.io/@kyusung/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EC%9A%94%EC%95%BD)