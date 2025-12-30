---
title: Destructors and Constructors
---

# Destructors and Constructors

## Destructors

### Definition

According to [_cppreference_](https://en.cppreference.com/w/cpp/language/destructor.html), destructor is a special member function that is called when an object's lifetime ends. Its primary responsibility is to **release resources** acquired by the objects during its lifetime, like dynamically allocated memory.

### Destructors in Polymorphic Class

In polymorphic class, destructors require special attention. Recall that `virtual` functions enable **dynamic binding**, which allows a base-class pointer to point to an object of a derived class. A derived class may allocate **additional resources** during its lifetime that the base class is unaware of. Therefore, it is crucial for the derived class to have a proper destructor to clean these resources.

However, defining a destructor in the derived class is not sufficient on its own. The destructor of the base class must be declared virtual. This ensures that when an object is deleted through a base-class pointer, the destructor corresponding to the object’s dynamic type is invoked.

<!-- Original
However, even though you have defined a proper destructor, you still need to make the base destructor virtual. This is because the base object pointer may point to the object of its derived class. Even though it is a pointer to the base class, the derived class destructor still need to be called.
-->

Consider the following example!

```c++
class Base {
public:
    ~Base() {
        cout << "Base destructor is called" << endl;
    }
};

class Derived: public Base {
public:
    ~Derived() {
        cout << "Derived destructor is called" << endl;
    }
};

int main() {
    Base* base = new Derived();
    delete base;
}
```

The following program outputs `Base destructor is called`. Even though `base` points to a `Derived` objects, only the `Base` destructor is invoked. Without declaring the destructor to be `virtual`, the `Derived` destructor will not be called. This happens because without `virtual` keyword, **static binding** is employed, which causes it to only call the `Base` destructor.

Observe that memory leak may occurs if `Derived` acquires additional resources. To fix this, as already mentioned, the base class destructor must be declared as `virtual` as follows.

```c++
class Base {
public:
    virtual ~Base() {
        cout << "Base destructor is called" << endl;
    }
};

class Derived: public Base {
public:
    ~Derived() {
        cout << "Derived destructor is called" << endl;
    }
};

int main() {
    Base* base = new Derived();
    delete base;
    /* Output:
        Derived destructor is called
        Base destructor is called
    */
}
```

By declaring the base destructor as `virtual`, the `Derived` constructor will now be triggered, ensuring that no memory leak will occur.
