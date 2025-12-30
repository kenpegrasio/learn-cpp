---
title: Virtual Functions
---

# Virtual (Member) Functions

An intuitive follow-up question is that how if we remove `= 0` from pure virtual functions. Indeed, the functions will then become virtual functions. Not like pure virtual functions, having virtual functions will **not make a class abstract**; however, having at least 1 virtual function will make it a polymorphic class. Not like pure virtual functions, virtual functions **can be implemented**.

## Static vs Dynamic Binding

A question is that if virtual functions can have implementation, then what is the difference between virtual functions and non-virtual functions?

The difference is as follows. Virtual functions is used to support **dynamic binding** or **runtime polymorphism**, whereas non-virtual functions will use **static binding**.

In static binding, the compiler will link the function call and the function definition during the compilation time (only for non-virtual functions). Because compiler does not know the runtime type of the object, the compiler will use the type that we explicitly give, the compile-time type, to choose the appropriate function. To make sense of this, observe the following example.

```c++
Base* obj = new Derived1();
obj = new Derived2();
```

In this example, during runtime, `obj` can have the type of `Derived1`, which then changed to `Derived2`. The compiler have no idea what happen during the runtime. The only thing it knows is that `obj` is given the type of `Base`, the one that we explicitly give. Therefore, it will link non-virtual functions according to `Base` type.

In fact, this is how function is chosen among **overloaded functions**, i.e., function overloading. Function overloading uses static binding.

On the other hand, dynamic binding happens during runtime. This is why it is called _runtime_ polymorphism because it happens in the runtime. Different than static binding, the function that is chosen is based on the runtime type of the object, not the compile-time type. This is achieved with the help of `vTable` and `vPtr` that will be explained afterwards.

Observe the following example to understand the difference between static and dynamic binding.

```c++
class Base {
public:
	virtual void display() {
		cout << "Display in Base" << endl;
	}
};

class Derived : public Base {
public:
	void display() override {
		cout << "Display in Derived" << endl;
	}
};

void do_things(Base& obj) {
	cout << "Do things Base" << endl;
	obj.display();
}

void do_things(Derived& obj) {
	cout << "Do things Derived" << endl;
	obj.display();
}

int main() {
	Derived derived;
	Base& base = derived;
	do_things(base);
}
```

The following code will output:

```
Do things Base
Display in Derived
```

Observe that `base` has a runtime type of `Derived` and a compile-time type of `Base`. Choosing between overloaded functions will use static binding, i.e., the compile type. This is why `Do things base` is printed. However, `obj.display()` will output `Display in Derived` because the runtime type is `Derived`; it will choose the `display` function from the runtime type, which is `Derived`.

## `vTable` and `vPtr`

To support dynamic binding, as mentioned in the previous chapter, the compiler will implement `vTable` and `vPtr` underneath the hood. `vTable` and `vPtr` will only be implemented for polymorphic class (class that has at least one virtual function); in other words, class with no virtual function will not have `vTable` and `vPtr`.

The `vTable`, virtual function table, is essentially a table of pointers to function. Each virtual function in the class will be given an entry in the `vTable`. Each entry is a pointer to the **most derived function** that the class should call; if the class has no child, the entry will point to its own implementation. It is also important that `vTable` is generated during **compile time** for each polymorphic class in the program.

Now, with each polymorphic class has its own `vTable`, during object construction, the compiler will then implement a pointer to point to the `vTable` of the class it is assigned to. This pointer is called `vPtr`, and the initialization of `vPtr` happen during **runtime**. For example,

```c++
Base* obj = new Derived1();
obj = new Derived2();
```

First, when `obj` is being assigned to object of type `Derived1`. `vPtr` of object `obj` will be set to `Derived1`'s `vTable`. Afterwards, when `obj` is being assigned to object of type `Derived2`, `vPtr` of object `obj` will be set to `Derived2`'s `vTable`.

## Object Slicing

It is also crucial to note that even though polymorphic class can be instantiated, dynamic binding can only be activated when pointer or reference is used. This is because: when being instantiated without using pointer or reference, the type is known for certain _thus_ static binding will be used.

However, if you still insist assigning derived class object to the base object, object slicing will happen. This means that the derived class additional attributes will be cut off to generate the base class object.

Why object slicing happen? This happen because when object is instantiated, for example: `Base base`, it has a static type of `Base`. When you assign it to `derived`, an object of type `Derived`, the compiler will perform implicit type conversion such that the type will change to `Base`. It does not make sense to have an object of `Base`, but the content is `Derived`; this is the same as having `int` but the content is `double`. That is why it slices the additional attributes and uses static binding to call the perform the function call.

Why object slicing does not happen when pointer or reference is used? This is because pointer or reference is an address. `Base*` and `Derived*` both are addresses; the difference is just how we view the content of the addresses as. `Base*` means we view the content as object `Base`, while `Derived*` means we view the content as object `Derived`. When we assign `Derived*` to `Base*`, it is just changed the way we view the object as, which is further supported by `vPtr` and `vTable`.
