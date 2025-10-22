# Inheritance, Access Control, and the Diamond Problem (C++98)

## Table of contents

- [Inheritance: concept](#inheritance-concept)
- [Kinds of inheritance and access control](#kinds-of-inheritance-and-access-control)
- [Protected vs Private: example and differences](#protected-vs-private-example-and-differences)
- [Single inheritance: constructor order and virtual destructor](#single-inheritance-constructor-order-and-virtual-destructor)
- [Overriding vs hiding](#overriding-vs-hiding)
- [Multiple inheritance and the diamond problem](#multiple-inheritance-and-the-diamond-problem)
- [Composition vs inheritance](#composition-vs-inheritance)
- [Notes on safety and edge cases](#notes-on-safety-and-edge-cases)
- [Conclusion](#conclusion)
- [Sources](#sources)

## Inheritance: concept

Inheritance lets you create a new class (derived) that reuses and extends the functionality of an existing class (base). It models an "is-a" relationship: a derived object should be substitutable for its base.

Use inheritance when you want to share behavior or contracts across related types. Prefer composition when you only need to reuse implementation without exposing a subtype relationship.

## Kinds of inheritance and access control

C++ supports several access specifiers (public, protected, private) and inheritance modes (public/protected/private inheritance). These control how base members are seen by derived classes and outside code.

- public inheritance: public -> public, protected -> protected, private -> inaccessible (outside code sees derived as base).
- protected inheritance: public -> protected, protected -> protected
- private inheritance: public -> private, protected -> private

Inside a derived class, public and protected members of the base are accessible (private members are not). Outside, visibility depends on inheritance mode.

Example: what a derived class can access from Base

```cpp
#include <iostream>

class Base {
public:
    int pub;
protected:
    int prot;
private:
    int priv;
public:
    Base() : pub(1), prot(2), priv(3) {}
};

class PublicDerived : public Base {
public:
    void dump() {
        std::cout << "pub=" << pub << " prot=" << prot << std::endl; // ok
        // std::cout << priv << std::endl; // ERROR: priv is not accessible
    }
};

int main() {
    PublicDerived d;
    d.dump();
    std::cout << "Access from outside: pub=" << d.pub << std::endl; // ok: public remains public
    return 0;
}
```

**Expected output:**

```
pub=1 prot=2
Access from outside: pub=1
```

## Protected vs Private: example and differences

`protected` members are accessible to derived classes but remain inaccessible to outside code. `private` members are inaccessible to derived classes and to outside code. Use `protected` when you want derived types to access internal implementation details; prefer `private` when you want to fully encapsulate data and provide controlled access via functions.

Example:

```cpp
#include <iostream>

class Base {
protected:
    int prot; // visible to Derived
private:
    int priv; // hidden from Derived
public:
    Base() : prot(10), priv(20) {

    }
};

class Derived : public Base {
public:
    void show() {
        std::cout << "protected accessible: " << prot << std::endl;
        // std::cout << "private accessible: " << priv << std::endl; // ERROR: 'priv' is private in Base
    }
};

int main() {
    Derived d;
    d.show();
    // std::cout << d.prot << std::endl; // ERROR: 'prot' is protected, not accessible from here
    return 0;
}
```

**Expected output:**

```
protected accessible: 10
```

Notes:

- `protected` gives derived classes controlled access to internals but still hides those members from users of the class.
- Overuse of `protected` couples subclasses to base implementation; prefer `private` with protected or public accessor functions when possible.

## Single inheritance: constructor order and virtual destructor

When constructing an object of a derived class, base constructors run first (base → derived). On destruction, destructors run in reverse (derived → base). If you intend to delete derived objects through a base pointer, the base must declare a virtual destructor to ensure the derived destructor is called.

Example: constructor/destructor order and virtual destructor

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
    p->who(); // dynamic dispatch
    delete p; // calls Derived::~Derived then Base::~Base because Base::~Base is virtual
    return 0;
}
```

**Expected output:**

```
Base constructed
Derived constructed
I am Derived
Derived destroyed
Base destroyed
```

If `Base::~Base()` were not virtual, `delete p;` would only call `Base`'s destructor and `Derived`'s destructor would not run (resource leaks / undefined behaviour).

## Overriding vs hiding

- Overriding: a derived class provides a new implementation for a virtual base method with the same signature — calls through base pointers/responses dispatch to the derived implementation.
- Hiding: if a derived class declares a non-virtual method with the same name (or a different signature), it hides the base method for name lookup.

**Example: hiding vs overriding**

```cpp
#include <iostream>

class Base {
public:
    virtual void f(int x) {
        std::cout << "Base::f(int)=" << x << "\n";
        }
};

class Derived : public Base {
public:
    // overload/hide Base::f(int)
    void f(double x) {
        std::cout << "Derived::f(double)=" << x << "\n";
    }
    // to override Base::f(int) we must use same signature
    void f(int x) {
        std::cout << "Derived::f(int)=" << x << "\n";
    }
};

int main() {
    Derived d;
    Base* pb = &d;
    pb->f(10); // calls Derived::f(int) because it overrides
    pb->Base::f(50); // calls Base::f(int)
    d.f(3.14); // calls Derived::f(double)
    // if Derived only had f(double), pb->f(10) would call Base::f(int) because overload hid base but did not override
    return 0;
}
```

**Expected output:**

```
Derived::f(int)=10
Base::f(int)=50
Derived::f(double)=3.14
```

Tip: if you intentionally want to bring base overloads into scope, use `Base::f;` inside the derived class.

## Multiple inheritance and the diamond problem

C++ allows multiple inheritance: a class can have more than one direct base. This is powerful but introduces complexity, notably the diamond problem two base classes inherit from the same grand-base, producing two copies of that grand-base in the most-derived object unless you use virtual inheritance.

Example: diamond problem and virtual inheritance

```cpp
#include <iostream>

class Top {
public:
    Top() {
        std::cout << "Top constructed\n";
    }
    virtual ~Top() {
        std::cout << "Top destroyed\n";
    }
    void id() const {
        std::cout << "I am Top\n";
    }
};

class Left : virtual public Top {
public:
    Left() {
        std::cout << "Left constructed\n";
    }
    ~Left() {
        std::cout << "Left destroyed\n";
    }
};

class Right : virtual public Top {
public:
    Right() {
        std::cout << "Right constructed\n";
    }
    ~Right() {
        std::cout << "Right destroyed\n";
    }
};

class Bottom : public Left, public Right {
public:
    Bottom() {
        std::cout << "Bottom constructed\n";
    }
    ~Bottom() {
        std::cout << "Bottom destroyed\n";
    }
};

int main() {
    Bottom b;
    b.id(); // single Top sub-object because Left and Right used virtual inheritance
    return 0;
}
```

**Expected output:**

```
Top constructed
Left constructed
Right constructed
Bottom constructed
I am Top
Bottom destroyed
Right destroyed
Left destroyed
Top destroyed
```

If `Left` and `Right` inherited `Top` non-virtually, `Bottom` would contain two independent `Top` subobjects and calling `id()` would be ambiguous without an explicit qualification.

What goes wrong when there are two `Top` subobjects

When the diamond is non-virtual, the most-derived object contains *two separate copies* of the shared base. Consequences:

- Duplicated state: each branch has its own `Top` data members, so there is no single shared state. Changes through one path do not affect the other.
- Ambiguity: an unqualified call like `b.id()` is ambiguous because the compiler doesn't know which `Top` subobject you mean (the one from `Left` or the one from `Right`).
- Increased size and construction overhead: constructors and destructors for `Top` run once per subobject, which can be unexpected if `Top` manages resources.
- Logical bugs: code that assumes a single shared `Top` (for example a single configuration, single handle, or single counter) will misbehave because there are two independent copies.

Example (non-virtual inheritance):

```cpp
#include <iostream>

class Top {
public:
    Top() {
        std::cout << "Top constructed\n";
        }
    void id() const {
        std::cout << "I am Top\n";
        }
};

class Left : public Top {
public:
    Left() {
        std::cout << "Left constructed\n";
    }
};

class Right : public Top {
public:
    Right() {
        std::cout << "Right constructed\n";
    }
};

class Bottom : public Left, public Right {
public:
    Bottom() {
        std::cout << "Bottom constructed\n";
    }
};

int main() {
    Bottom b;
    // b.id(); // ERROR: ambiguous — which Top::id? Left::Top or Right::Top?

    // You must explicitly qualify which path you want:
    b.Left::id();  // calls the Top subobject from Left
    b.Right::id(); // calls the Top subobject from Right
    return 0;
}
```

**Expected output:**

```
Top constructed
Left constructed
Top constructed
Right constructed
Bottom constructed
I am Top
I am Top
```

Note the two `Top constructed` lines — two distinct `Top` subobjects were created. If `Top` had important state (a resource handle or a unique identifier), having two copies could lead to resource duplication, inconsistent program state, or hard to find bugs.

Prefer virtual inheritance only when you truly need a single shared base subobject; otherwise reorganize the design (composition, interfaces) to avoid the diamond.

## Composition vs inheritance

- Inheritance models an "is a" relationship and provides polymorphic substitution.
- Composition models a "has a" relationship and is often safer: it avoids exposing implementation details and prevents tight coupling.

Prefer composition when you only need to reuse implementation or when the relationship is not truly "is-a".

## Notes on safety and edge cases

- Always declare a virtual destructor in a class intended to be a polymorphic base.
- Be careful calling virtual functions from constructors/destructors: calls resolve to the current class, not the most derived object.
- Watch for object slicing when passing derived objects by value to functions expecting base objects.
- Multiple inheritance increases complexity; prefer interfaces (pure abstract classes) when combining orthogonal behaviors.
- Use `virtual` inheritance only when necessary (diamond problem). Virtual inheritance has runtime cost and complicates construction order.

## Conclusion

Inheritance is a fundamental tool in C++ for code reuse and polymorphism.Prefer clear public interfaces, virtual destructor for polymorphic bases, and composition when a clear "has-a" relation suffices.

## Sources:

- https://en.cppreference.com/w/cpp/language/derived_class
- https://en.cppreference.com/w/cpp/language/virtual
- https://www.learncpp.com/cpp-tutorial/introduction-to-inheritance/
- https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines
