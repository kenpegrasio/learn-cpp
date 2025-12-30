---
title: Class Member Functions
---

# Class Member Functions

In C++, **member functions** are the functions that are declared as a member of a class. There are many different distinctions of member functions: `static` vs `non-static`, `const` vs `non-const`, etc.

## Const vs Non-const Member Functions

Constant member functions are functions that promise not to change the values of the data members in the class. To make a member function constant, `const` keyword is added after the function prototype.

Constant member functions can be called by both `non-const` and `const` objects. For example, in the code below, calling `nonConstWidget.supply()` and `constWidget.supply()` will both return `0`.

```c++
#include<iostream>

class Widget {
public:
	int supply() const {
		return 0;
	}
};

int main() {
	Widget nonConstWidget;
	std::cout << nonConstWidget.supply() << std::endl; // 0
	const Widget constWidget;
	std::cout << constWidget.supply() << std::endl; // 0
}
```

On the other hand, non-const member functions **can** only be called by non-const object. Constant object **cannot** call non-const member functions.

```c++
#include<iostream>

class Widget {
public:
	int supply() {
		return 0;
	}
};

int main() {
	Widget nonConstWidget;
	std::cout << nonConstWidget.supply() << std::endl; // 0
	const Widget constWidget;
	std::cout << constWidget.supply() << std::endl; // error
}
```

For example, the code below will produce the following error:

```
[Error] passing 'const Widget' as 'this' argument discards qualifiers [-fpermissive]
```

This is indeed intuitive because you cannot change value of constant object once it is initialized. Calling non-const function may result in changes of value, so it is not allowed.

### Interview Implication 1

**Question**: What is the difference between constant and non constant member functions?

**Sample Answer**: Constant member functions are the functions declared inside a class that promise not to change the value of the class members. Non-constant is just the opposite, changing class members' value is allowed.

## Static Member Functions

Static member functions are functions that belong directly to the class, not to any object of the class. As a consequence, static member functions are independent to any object instance.

Because it is independent from any object instance, it **can be called without creating an object**.

```c++
#include<iostream>

class Widget {
public:
	static void print() {
		std::cout << "print is called" << std::endl;
	}
};

int main() {
	Widget::print();
}
```

For example, the code above will result in `print is called` being output.

Other consequence of this independency is that static member functions **can only access static data members or other static member functions**. This is indeed intuitive because static data members of a class are required to be independent to any class object. So, accessing static data members do not require instantiating the class, i.e., creating class instance.

## Inline (Member) Functions

Member functions are inlined by default. If request is granted, inline function simply means that the compiler will expand the code during compilation. This is aimed to reduce **function-call overhead**.

![Inlining Example](/assets/images/inline_functions.png)

It is important to note that inlining a function is only a **request** to the compiler; the **compiler may ignore** inlining the function if the function is too complex, for example, containing loops or recursive.

### Multiple Definition of Inline Functions

Interestingly, there can be more than one definition of inline function, as suggested by number 13.3 in the following [link](https://timsong-cpp.github.io/cppwp/n4861/basic.def.odr#13).

Therefore, the following example is valid and it will print `2` when `gcc test3.cpp test4.cpp -o test` is ran.

```c++
// test3.cpp
#include<stdio.h>

inline int add(int x, int y) {
    return x + y;
}

int main() {
    printf("%d", add(1,1));
}
```

```c++
// test4.cpp
#include<stdio.h>

inline int add(int x, int y) {
    return x + y;
}
```

However, as given in number 13.8, each such definition shall consist of the same sequence of tokens! If the two definitions of the inline functions are not the same (possibly doing two different things), as a consequence, the program is **ill-formed no diagnostic** (IFNDR), i.e., the program will run in an undefined behaviour, but the compiler will not report anything to you. For further reference, you can refer to this [link](https://stackoverflow.com/questions/64537486/inline-functions-with-same-type-but-different-body-in-cpp-files).

### Multiple Definition of Non-inline Functions

On the other hand, when function is not inlined as written below, it will cause multiple-definition errors.

```c++
// test3.cpp
#include<stdio.h>

int add(int x, int y) {
    return x + y;
}

int main() {
    printf("%d", add(1,1));
}
```

```c++
// test4.cpp
#include<stdio.h>

int add(int x, int y) {
    return x + y;
}
```

As mentioned, when `gcc test3.cpp test4.cpp -o test` is ran, the following error will be produced.

```
/usr/bin/ld: /tmp/cchVHrlD.o: in function `add(int, int)':
test4.cpp:(.text+0x0): multiple definition of `add(int, int)'; /tmp/ccmUMdb2.o:test3.cpp:(.text+0x0): first defined here
collect2: error: ld returned 1 exit status
```
