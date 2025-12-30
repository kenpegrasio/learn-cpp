---
title: Abstract vs Concrete Class
---

# Abstract vs Concrete Class

## What is Abstract Class?

Abstract classes are classes that acts as a **contract** for other classes. Because it is a contract, abstract classes will contain a **set of required behaviours** (at least one requirement) as a foundation for the other classes to obey.

In C++, these required behaviours are implemented as `pure virtual functions`. Therefore, technically speaking, abstract classes are the classes that have at least 1 pure virtual member function.

The following syntax is how we will define member functions as pure virtual:

```c++
class Widget {
public:
	virtual void do_things() = 0;
}
```

By defining a member function to be pure virtual, we will not provide the implementation of the class; however, we require classes that are derived from the class, in this case: `Widget`, to define the function.

## What is Concrete Class?

On the contrary, when a class does not have any pure virtual function, we can say that the implementation of the class, i.e., the representation is complete or fully defined. In this case, we will call the class as concrete classes.

## Instantiation

Another important distinction between these two types of classes is related to **instantiation**. In concrete class, since the implementation is completely known and all class behaviours are well-defined, objects of concrete classes can be instantiated.

On the other hand, because pure virtual functions in abstract class does not have implementation. This makes the behaviour of the class incomplete, so consequently we cannot instantiate it. The only way to define an abstract class is by using pointer or reference; this is valid because pointer and reference do not create objects, it is just an address. However, when defining abstract class through reference, it requires the object to be initialized. For example,

```c++
class Widget {
public:
	virtual void do_things() = 0;
};

int main() {
	Widget obj; // ERROR! abstract class can't be instantiated
	Widget* obj1; // OK!
	Widget& obj2; // ERROR! Reference must be initialized
}
```
