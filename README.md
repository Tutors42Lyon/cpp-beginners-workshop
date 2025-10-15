# ℹ️ Sources :
- [ISO CPP]( https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-interfaces)
- [C++ Classes and Objects (GeeksForGeeks)](https://www.geeksforgeeks.org/cpp/c-classes-and-objects/)
- [Stroustrup.com](https://www.stroustrup.com/)
- [CPP Reference](https://cppreference.com/)

***

# C++ Context, Philosophy, and Compilation
## Philosophy
## Compilation

# Basic stuff
## Classes
### Concept
A `class` in C++ is a user-defined type that acts as a blueprint for creating [objects](#objects), grouping together related data ([attributes and functions (methods)](#members-attributes--methods)) into one single cohesive unit.

A `class` defines how objects (instances) of that type are **structured** and **behave**.

Classes allow you to model **complex entities** (like cars, accounts, or shapes) by encapsulating their attributes and behaviors in one place.

```cpp
// Rectangle.hpp
class Rectangle
{
    int width;
    int height;
};
```

> ℹ️ See [Classes](https://en.cppreference.com/w/cpp/language/classes.html)

### Construct and destruct an instance

To create an **instance** of a given `class`, you will have to define a **public** ***constructor*** and a ***destructor*** in order to *construct* and *destruct* a class **instance** outside its own scope.

They have to be respectively prototyped as follows:

```cpp
// constructor
className(/*parameters*/);

// destructor
~className(/*parameters*/);
```

```cpp
// Rectangle.hpp
class Rectangle
{
    public:
    Rectangle();    // constructor
    ~Rectangle();   // destructor
   // public member datas (attributes and methods)  

};
```
> 💡 Note that you can have multiple constructors/destructors for a same given class

> ℹ️ See 
>- [Members Attribtes & Methods](#members-attributes--methods)
>- [Access Specifiers](#access-specifiers)
>- [Orthodox Canonical class form](#members-attributes--methods)

![img_class&object](./assets/Class_Object_example.webp)

### Classes vs Structs

Both classes and structs are user-defined types that can contain attributes and functions.
Meanwhile, classes allows you to set up **access specifiers** (private by default) to build **complex** data-types, in an [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming)-way with desidered **accessibility and behavioral restrictions**.

> ℹ️ See 
>- [Members Attribtes & Methods](#members-attributes--methods)
>- [Access Specifiers](#access-specifiers)
>- [Inheritence](#inheritence)

## Objects

An object is an entity created as an **instance** of a `class`.
There can be as many objects of a class as desired.

```cpp
// main.cpp
ClassName  objectName;
```

> *C++ programs create, destroy, refer to, access and manipulate objects.* 
>
> ℹ️ See [Object](https://en.cppreference.com/w/cpp/language/object.html)

### Objects can be sorted in differents categories...
- Fundamental type objects

```c++
int x = 42;
```

- Pointer objects :

```c++
int *ptr;
```

- Array objects :

```c++
int array[42];
```

- Class-type objects

```c++
std::string str;
```
### ...and they have differents key characteristics.

- **Type**: Each determined at compile-time (C++ is a statically typed language).

```c++
typeid(variable).name()
```

- **Storage**: Objects occupy memory with specific size.

```c++
sizeof(variable)
```
- **Lifetime**: Objects have well-defined creation and destruction points.
    - **Memory allocation**: The compiler reserves memory for the object. For **stack** objects, memory is allocated when execution enters the block while for **heap** objects (via `new`), memory is allocated in the available memory space. 
    - **Constructor execution**: Class-type objects invoke a [constructor](#construct-and-destruct-an-instance) that initializes **member** variables and *may acquire resources*.
        Constructors can be **default** (no parameters), **parameterized**, or **copy** constructors (cf. [Orthodox Canonical class form](#orthodox-canonial-form)).
        During this phase, ***base class*** (!= derived) constructors run first (for inheritance), then member objects' constructors, and finally the enclosing class's constructor itself.
    
    - **Lifetime begins**: Once construction is complete, the object becomes *usable*. 

    - **Destructor execution**: When the object's lifetime ends (either automatic scope exit or delete for dynamic objects), its [destructor](#construct-and-destruct-an-instance) runs. The **destructor** is responsible for releasing resources.
        For class hierarchies, destructors run in reverse: the enclosing class destructor first, then member objects' destructors, and finally base class destructors.
    
    - **Memory deallocation**: Memory is released back to the system, either automatically for stack objects or manually for heap objects with delete.

    > ℹ️ See:
    >- [Memory allocation, pointers & references](#memory-allocation-pointers--references)
    >- [Classes](#classes)
    >- [Inheritence](#inheritence)

- **Identity**: Each object has a unique address in memory (except for [bit-fields](https://en.wikipedia.org/wiki/Bit_field) and [register variables](https://en.wikipedia.org/wiki/Register_(keyword))).

    An object is addressable if:

    - **Occupies Memory**: Has a location in the program's address space

    - **Byte-Aligned**: Can be referenced by a byte address (eg. bit-field)

    - **Observable**: Not completely optimized away by the compiler

- **State**: Objects maintain internal data that can change over time (eg. register variables).
    
    An object's state is the **complete set of values held by all its member variables** (data members), at anytime during runtime.
    The state represents the object's "*configuration*" that distinguishes one instance from another of the same type.

    > 💡 [Static members](#static--const-keywords) belong to the class as a whole, not an individual object's state.

    ```cpp
    // Rectangle.hpp
    class Rectangle
    {
        int width;
        int height;
    };
    ```

    ```cpp
    // main.cpp
    Rectangle rect;

    rect.width = 4;
    rect.height = 2;
    ```
    The **state** of `rect` (instance from the `Rectangle` class) is, at a given instant during runtime: `{width = 4; height = 2;}`.

- **Value**: An object's value generally refers to the *abstract* interpretation of the **object's state as a single meaningful entity** used in computations or comparisons.

    ```cpp
    rect.width = 4;
    ```

> 💡 **Distinction**
>
>```cpp
>// main.cpp
>Rectangle r1 = {4, 2};
>Rectangle r2 = {4, 3};
>```
>- **Identity**: `&r1 != &r2` (different memory locations)
>- **State**: `r1 = {width = 4, height = 2}` and `r2 = {width = 4, height = 3}` (different states)
>- **Value**: `r1.width == r2.width` (both have a height of 4)

### Quiz
- Is a class itself an object ? [Y/N] (N)

## Members: attributes & methods
## Static & Const keywords
## Namespaces
## Streams
## Init lists
## std::string/std::stringstream

***

# Memory allocation, pointers & references
## New/Delete
## Pointers/References
## Switch statememts

***

# Ad-hoc polymorphism, operator overloading and the Orthodox Canonical class form

***

# Inheritence

***

# Subtype Polymorphism, Abstract Classes, and Interfaces

***
