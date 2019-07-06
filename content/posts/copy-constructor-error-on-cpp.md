---
title: "What's the problem on this cpp code?"
date: 2019-07-03T20:49:33+09:00
draft: true
categories: [studylog]
tags: [cpp,c++,constructor,copy constructor,error]
---

Trying to dig the error on following c++ code.

<!--more-->
```cpp
#include <iostream>

struct NoImplicitCopy {
	NoImplicitCopy() = default;
	explicit NoImplicitCopy(const NoImplicitCopy& ) = default;
};

NoImplicitCopy foo()
{
	NoImplicitCopy n;
	return n;
}

void bar(NoImplicitCopy n)
{ }

int main(void)
{
	NoImplicitCopy  n;
	NoImplicitCopy x(n);

	n = foo();

	bar(n);

    return EXIT_SUCCESS;
}
```

from (Explicit copy-constructor)[https://en.wikibooks.org/wiki/More_C%2B%2B_Idioms/Non-copyable_Mixin#Solution_and_Sample_Code]


When I compile above code, I got following error message. and I don't understand why the error occurred.
Even if I read the `Explicit copy-constructor` web page. :(

What a dumb head and dumber my intelligence.
Anyway... the error message is like followings.

```shell
[enjian@localhost build]$ g++ main.cpp
main.cpp: In function ‘NoImplicitCopy foo()’:
main.cpp:11:9: error: no matching function for call to ‘NoImplicitCopy::NoImplicitCopy(NoImplicitCopy&)’
   11 |  return n;
      |         ^
main.cpp:4:2: note: candidate: ‘constexpr NoImplicitCopy::NoImplicitCopy()’
    4 |  NoImplicitCopy() = default;
      |  ^~~~~~~~~~~~~~
main.cpp:4:2: note:   candidate expects 0 arguments, 1 provided
main.cpp: In function ‘int main()’:
main.cpp:23:7: error: no matching function for call to ‘NoImplicitCopy::NoImplicitCopy(NoImplicitCopy&)’
   23 |  bar(n);
      |       ^
main.cpp:4:2: note: candidate: ‘constexpr NoImplicitCopy::NoImplicitCopy()’
    4 |  NoImplicitCopy() = default;
      |  ^~~~~~~~~~~~~~
main.cpp:4:2: note:   candidate expects 0 arguments, 1 provided
main.cpp:14:25: note:   initializing argument 1 of ‘void bar(NoImplicitCopy)’
   14 | void bar(NoImplicitCopy n)
      |          ~~~~~~~~~~~~~~~^
[enjian@localhost build]$
```

Let's find out what is the problem.

...few days later...

Figure out the source of error message. But still don't know the reason. :(
To compile, just remove `explicit` keyword. but why? why?? why???
```cpp
 struct NoImplicitCopy {
        NoImplicitCopy() = default;
-       explicit NoImplicitCopy(const NoImplicitCopy& ) = default;
+       NoImplicitCopy(NoImplicitCopy&) = default;
 };
```

The result is.
```shell
[enjian@localhost build]$ g++ main.cpp
[enjian@localhost build]$
```

Let's found out what the `explicit` keyword doing.

{TBD}
