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
Anyway... the error message is like this.

```shell
[enjian@localhost build]$ make
Scanning dependencies of target copy_constructor
[ 97%] Building CXX object copy_constructor/CMakeFiles/copy_constructor.dir/main.cpp.o
/home/seungrye/copy_constructor/main.cpp: In function ‘NoImplicitCopy foo()’:
/home/seungrye/copy_constructor/main.cpp:10:9: error: no matching function for call to ‘NoImplicitCopy::NoImplicitCopy(NoImplicitCopy&)’
   10 |  return n;
      |         ^
/home/seungrye/copy_constructor/main.cpp:4:2: note: candidate: ‘constexpr NoImplicitCopy::NoImplicitCopy()’
    4 |  NoImplicitCopy() = default;
      |  ^~~~~~~~~~~~~~
/home/seungrye/copy_constructor/main.cpp:4:2: note:   candidate expects 0 arguments, 1 provided
/home/seungrye/copy_constructor/main.cpp: In function ‘int main()’:
/home/seungrye/copy_constructor/main.cpp:23:7: error: no matching function for call to ‘NoImplicitCopy::NoImplicitCopy(NoImplicitCopy&)’
   23 |  bar(n);
      |       ^
/home/seungrye/copy_constructor/main.cpp:4:2: note: candidate: ‘constexpr NoImplicitCopy::NoImplicitCopy()’
    4 |  NoImplicitCopy() = default;
      |  ^~~~~~~~~~~~~~
/home/seungrye/copy_constructor/main.cpp:4:2: note:   candidate expects 0 arguments, 1 provided
/home/seungrye/copy_constructor/main.cpp:13:25: note:   initializing argument 1 of ‘void bar(NoImplicitCopy)’
   13 | void bar(NoImplicitCopy n)
      |          ~~~~~~~~~~~~~~~^
make[2]: *** [src/platforms/any/copy_constructor/CMakeFiles/copy_constructor.dir/build.make:63: src/platforms/any/copy_constructor/CMakeFiles/copy_constructor.dir/main.cpp.o] Error 1
make[1]: *** [CMakeFiles/Makefile2:921: src/platforms/any/copy_constructor/CMakeFiles/copy_constructor.dir/all] Error 2
make: *** [Makefile:84: all] Error 2
```

Let's find out what is the problem.


{TBD}
