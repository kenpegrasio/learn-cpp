---
title: Inheritance
---

# Inheritance

Recall that by defining a member function to be pure virtual, we require classes that are derived from the class to define the function. Then, the question is how to define a class that derives another class. This is where inheritance comes into play.

## Syntax

To define that a class is derived from another class, the syntax follows this format: `class derived_class_name : mode parent_class_name {}`. There can be three types of inheritance modes: `public`, `protected`, and `private`. An example for this is as follows.

```c++
class Base {};
class Derived : public Base {};
```

## Inheritance Modes: `public` vs `private` vs `protected`

In inheritance, all non-private (public and protected) members of the base class will be inherited by the derived class. However, the mode of the inherited class members will be different according the chosen type of inheritance modes. The following shows how it is differed from each other:

1. When the inheritance mode is `public`, all `public` parent class members (class members include member functions and variables) will be inherited as `public` and all `protected` parent class members will be inherited as `protected`.
2. When the inheritance mode is `protected`, all `public` and `protected` parent class members will be inherited as `protected`.
3. When the inheritance mode is `private`, all `public` and `protected` parent will be inherited as `private`.

## `override`

After properly defining the inheritance relationship, we can then define the pure virtual functions by overriding it. A good practice when overriding function is to verbosely write the `override` keyword. This is beneficial because it allows the compiler to catch declaration mistake.

## Example

Now, using inheritance, we can define the derived class and assign it to the reference of the base class. The code is as follows:

```c++
class Base {
public:
    virtual void do_things() = 0;
};

class Derived : public Base {
public:
    void do_things() override {}
};

int main() {
	Base obj; // ERROR! abstract class cannot be instantiated
	Derived obj1; // OK! Concrete class can be instantiated
	Base* obj2; // OK!
	Base& obj3 = obj1; // OK! Reference must be initialized!
}
```
