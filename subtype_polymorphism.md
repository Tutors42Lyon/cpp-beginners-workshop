
# Subtype Polymorphism, Abstract Classes, and Interfaces (C++98)


## Table of contents

- [The virtual keyword](#the-virtual-keyword)
- [Object slicing](#object-slicing)
- [Subtype polymorphism (examples)](#subtype-polymorphism)
- [Abstract classes](#abstract-classes)
- [Interfaces in C++98 (pattern)](#interfaces-in-c98-pattern)
- [Design and best practices](#design-and-best-practices)
- [Edge cases and safety](#edge-cases-and-safety)
- [Sources](#sources)

## The `virtual` keyword

The `virtual` keyword is the mechanism that enables runtime polymorphism in C++. Here are the main points to understand:

- Declaring a member function `virtual` in the base class allows derived classes to `override` it. Calls to that function through a base pointer or reference will dispatch to the most derived override at runtime.
- Always provide a virtual destructor in base classes intended for polymorphic use so that deleting through a base pointer calls the derived destructor.


Example showing `virtual` dispatch and importance of virtual destructor:

```cpp
#include <iostream>

class Base {
public:
    Base() {
        std::cout << "Base constructed\n";
    }
    virtual ~Base() {
        std::cout << "Base destroyed\n";
    }
    virtual void who() const {
        std::cout << "I am Base\n";
    }
};

class Derived : public Base {
public:
    Derived() {
        std::cout << "Derived constructed\n";
    }
    ~Derived() {
        std::cout << "Derived destroyed\n";
    }
    void who() const {
        std::cout << "I am Derived\n";
    }
};

int main() {
    Base* p = new Derived();
    p->who();
    delete p;
    return 0;
}
```

**Expected output:**

```cpp
Base constructed
Derived constructed
I am Derived
Derived destroyed
Base destroyed
```

If `Base`'s destructor were not virtual, `delete p;` would only call `Base`'s destructor leading to resource leaks or undefined behavior if `Derived` manages resources.

## example showing `virtual` dispatch and importance of virtual destructor

```cpp
class Base {
public:
    Base() {
        std::cout << "Base constructed\n";
    }
    ~Base() { // non-virtual destructor compiler error  with flags
        std::cout << "Base destroyed\n";
    }
    virtual void who() const {
        std::cout << "I am Base\n";
    }
};

int main() {
    Base* p = new Derived();
    p->who();
    delete p; // Undefined behavior: only Base destructor called
    return 0;
}
```

notes:

- Object slicing: storing a derived object by value in a base object slices off derived parts virtual dispatch requires pointers or references.
- Virtual table (vtable) and runtime cost: virtual calls have a small runtime cost (indirection) acceptable in most designs but consider it in hot inner loops.

## Why Use `virtual`

The `virtual` keyword is at the core of runtime polymorphism in C++. Here’s why and when to use it:

- **Runtime polymorphism:** `virtual` allows calls made through a pointer or reference to the base class to be dispatched to the most derived implementation at runtime. This is the mechanism that enables dynamic behavior.
- **Proper resource management:** Declaring a `virtual` destructor in a polymorphic base class ensures that destruction through a base pointer correctly invokes derived destructors, preventing memory leaks and undefined behavior.
- **Contract and extensibility:** Making methods virtual allows derived classes to extend or override behavior without changing existing consumers.

### Quick Best Practices

- Always declare a **`virtual` destructor** in any base class intended for polymorphic use.
- Only use `virtual` when you actually need **runtime polymorphism** — don’t add it unnecessarily.

**In summary:**
`virtual` is the tool that enables dynamic and safe behavior between related classes —
to be used with awareness of its contract, cost, and the rules of construction/destruction.

## Object Slicing

When you pass or assign polymorphic objects by value (not by pointer or reference), C++ copies only the base class members. All derived class data and behavior are lost.

**Example of object slicing:**

```cpp
#include <iostream>

class Base {
public:
    void speak() const {
        std::cout << "Base speaking" << std::endl;
    }
};

class Derived : public Base {
public:
    void speak() const {
        std::cout << "Derived speaking" << std::endl;
    }
};

void makeSound(Base b) {
    b.speak();
}

int main() {
    Derived d;
    makeSound(d); // Object slicing occurs here

    Base b = d; // Object slicing occurs here
    b.speak();
    return 0;
}
```

**Expected output:**

```cpp
Base speaking
Base speaking
```
**Summary:**

- Slicing loses all derived class data and polymorphic behavior.

- It happens silently without compiler warnings in most cases.

- Always use pointers or references for polymorphic types.


## Subtype Polymorphism

### Concept

Subtype polymorphism allows a pointer or reference to a base class to invoke behavior implemented by derived classes. Resolution happens at runtime using virtual functions.

### Simple example of subtype polymorphism

```cpp
#include <iostream>

class Animal {
public:
    virtual ~Animal() {}
    virtual void speak() const {
        std::cout << "Animal sound" << std::endl;
    }
};

class Dog : public Animal {
public:
    void speak() const {
        std::cout << "Woof" << std::endl;
    }
};

class Cat : public Animal {
public:
    void speak() const {
        std::cout << "Meow" << std::endl;
    }
};

int main() {
    Animal* a1 = new Dog();
    Animal* a2 = new Cat();

    a1->speak(); // resolves to Dog::speak() at runtime
    a2->speak(); // resolves to Cat::speak() at runtime

    delete a1;
    delete a2;
    return 0;
}
```

**Expected output:**

```
Woof
Meow
```

## Abstract Classes

### Concept

An abstract class declares at least one pure virtual function. It cannot be instantiated. Derived classes must implement the pure virtual methods to become instantiable.

**Example of pure virtual function declaration:**

```cpp
virtual void myFunction() = 0; // pure virtual function
```

### Example: abstract geometric shape

```cpp
#include <iostream>

class Shape {
public:
    virtual ~Shape() {}
    // pure virtual function → abstract class
    virtual double area() const = 0;
    void info() const {
        std::cout << "This is a shape." << std::endl;
    }
};

class Rectangle : public Shape {
private:
    double w;
    double h;
public:
    Rectangle(double _w, double _h) : w(_w), h(_h) {}
    double area() const {
        return w * h;
    }
};

class Circle : public Shape {
private:
    double r;
public:
    Circle(double _r) : r(_r) {

    }

    double area() const {
        return 3.14159265 * r * r;
    }
};

int main() {
    Shape* s1 = new Rectangle(3.0, 4.0);
    Shape* s2 = new Circle(1.0);

    std::cout << "Rectangle area: " << s1->area() << std::endl;
    std::cout << "Circle area: " << s2->area() << std::endl;
    s1->info();
    s2->info();

    delete s1;
    delete s2;
    return 0;
}
```

**Expected output:**

```
Rectangle area: 12
Circle area: 3.14159
This is a shape.
This is a shape.
```

### Non-virtual base methods in derived classes
If the base class defines a non-virtual method, derived classes inherit and can call it directly. However, non virtual methods are not dispatched polymorphically through base pointers/references. If a derived class defines a method with the same name and signature, it hides the base method.

Attempting to instantiate `Shape` directly will result in a compile-time error like this:
```cpp
int main() {
Shape abstractShape; // cannot instantiate abstract class compile error
}
```

Attempting to define a derived class without implementing all pure virtual functions will also result in a compile-time error:

```cpp
#include <iostream>

class Shape {
public:
    virtual ~Shape() {}
    // pure virtual function → abstract class
    virtual double area() const = 0;
};

class Rectangle : public Shape {
private:
    double w;
    double h;
public:
    Rectangle(double _w, double _h) : w(_w), h(_h) {}
    //compile error if area() not implemented
};
```

## Interfaces in C++98 (pattern)

### Concept

In C++98 there is no `interface` keyword. You simulate an interface with a class that contains only pure virtual functions and a virtual destructor. Derived classes implement these methods.

### Example: a `Drawable` interface

```cpp
#include <iostream>

class Drawable {
public:
    virtual ~Drawable() {}
    // three pure virtual functions to form an interface
    virtual void draw() const = 0;
    virtual void resize(int w, int h) = 0;
    virtual const char* description() const = 0;
};

class Button : public Drawable {
private:
    const char* label;
    int width;
    int height;
public:
    Button(const char* _label, int w, int h) : label(_label), width(w), height(h) {

    }
    void draw() const {
        std::cout << "[Button: " << label << " (" << width << "x" << height << ")]" << std::endl;
    }
    void resize(int w, int h) {
        width = w;
        height = h;
    }
    const char* description() const {
        return "A clickable button";
    }
};

class Label : public Drawable {
private:
    const char* text;
    int width;
    int height;
public:
    Label(const char* _text, int w, int h) : text(_text), width(w), height(h) {

    }
    void draw() const {
        std::cout << "Label: " << text << " (" << width << "x" << height << ")" << std::endl;
    }
    void resize(int w, int h) {
        width = w;
        height = h;
    }
    const char* description() const {
        return "A text label";
    }
};

void render(const Drawable& d) {
    // Accept any Drawable by reference
    std::cout << "Rendering (" << d.description() << "): ";
    d.draw();
}

int main() {
    Button b("OK", 12, 3);
    Label l("Hello world", 30, 2);

    render(b);
    render(l);

    Drawable* p = &b;
    p->resize(20, 4);
    render(*p);

    return 0;
}
```

**Expected output:**

```
Rendering (A clickable button): [Button: OK (12x3)]
Rendering (A text label): Label: Hello world (30x2)
Rendering (A clickable button): [Button: OK (20x4)]
```

## Why Use an Interface / Benefits

Here are some practical reasons why one might choose to use an interface (or a pure abstract class):

- **Decoupling:** An interface separates the *“what”* from the *“how.”* Code that depends on the interface doesn’t know the concrete implementation, making it easier to replace or evolve components without modifying their consumers.
- **Interchangeable implementations:** As long as the interface contract is respected, multiple implementations can be substituted for one another, making the system more flexible.
- **Reusability and composition:** Several classes can implement the same interface to reuse generic algorithms that operate on that interface.

### When to Prefer an Interface Over an Abstract Class with State

- Use an **interface** (a purely abstract class without state) when you only need to declare behaviors, not shared data — this ensures a clear separation between interface and implementation.
- Prefer an **abstract class** (with non-virtual methods or shared state) when you want to provide default implementations or share common code/state among several derived classes.

**In summary:**
Interfaces are a tool for achieving modular, testable, and replaceable code,
while abstract classes are used when you want to share a common foundation or provide partial behaviors.


## Design and best practices

- Always declare a virtual destructor in a class intended to be used polymorphically.
- Do not add non intuitive operators respect the base class contract.
- For pure interface patterns, do not add member state. Otherwise it is no longer a strict interface.
- An interface should define what can be done, not how it's stored. Adding state violates the separation between interface and implementation

## Edge cases and safety

- Calling virtual methods from constructors/destructors: in C++98 virtual calls resolve to the current class being constructed/destructed (not the derived one)  avoid depending on derived implementations.
- Ensure derived classes implement all pure virtual functions if they need to be instantiated.
- Self assignment is not specific to polymorphism but remains important when classes manage resources.

## Conclusion

Subtype polymorphism (via virtual functions), abstract classes and interface patterns are central to OOP design in C++. In C++98 follow these rules virtual destructor, and be careful with virtual calls during construction/destruction.

---

## Sources:

#### Interfaces Classes
- https://www.tutorialspoint.com/cplusplus/cpp_interfaces.htm

#### Subtype Polymorphism
- https://www.learncpp.com/cpp-tutorial/pointers-and-references-to-the-base-class-of-derived-objects/
- https://catonmat.net/cpp-polymorphism

#### Virtual Functions
- https://www.learncpp.com/cpp-tutorial/virtual-functions/
- https://en.cppreference.com/w/cpp/language/virtual.html
- https://en.cppreference.com/w/cpp/language/derived_class.html