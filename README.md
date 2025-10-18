# ℹ️ Sources :
- [ISO CPP]( https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-interfaces)
- [C++ Classes and Objects (GeeksForGeeks)](https://www.geeksforgeeks.org/cpp/c-classes-and-objects/)
- [Stroustrup.com](https://www.stroustrup.com/)
- [cppreference](https://cppreference.com/)

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

> ℹ️ See [Classes](https://en.cppreference.com/w/cpp/language/classes.html) (cppreference.com)

### Construct and destruct an instance

To create an **instance** of a given `class`, you will have to define a **[public](#access-specifiers)** ***constructor*** and a ***destructor*** in order to *construct* and *destruct* a class **instance** outside its own scope.

#### Constructor 

A **constructor** is a special [member function](#members-attributes--methods) executed when an object is created, initializing the object’s **state**, [members and base class](#inheritence) : calling their respective constructors.

- C++ allows a class to have **multiple constructors**, each with different parameter lists (**overloading**).

- A constructor without any parameter become the ***default*** constructor. It will be automatically called at the instance declaration.

- If no constructors are declared, the compiler automatically generates a default constructor which is qualified as ***trivial*** : it **only** performs member and base class initializations.

- If any constructor is declared, but none is a default constructor, the compiler **does not** generate one.

- If none is provided, compiler automatically generates one : it **only** call members and base class destructors.

```cpp
className(/* optionnal parameters */);
```

#### Destructor

The **destructor** is run when an object’s lifetime ends (when it goes out of scope or after a `delete` call), releasing resources.

- **Only one destructor** is permitted per class.

- Overloading destructors is not allowed (can never take arguments or return a value).

- Can be virtual (should be for polymorphic bas classes)

```cpp
~className(); // No overloading
```

#### Example

```cpp
// Rectangle.hpp
class Rectangle
{
    public:
        Rectangle();                        // default
        Rectangle(int width, int height);
        ~Rectangle();
   // ...  
};
```

![img_class&object](./assets/Class_Object_example.webp)


> ℹ️ See 
>- [Members Attribtes & Methods](#members-attributes--methods)
>- [Access Specifiers](#access-specifiers)
>- [Orthodox Canonical class form](#members-attributes--methods)

### Classes vs Structs

Both classes and structs are user-defined types that can contain attributes and functions.
Meanwhile, classes allows you to set up **access specifiers** (private by default) to build **complex** data-types, in an [OOP](https://en.wikipedia.org/wiki/Object-oriented_programming)-way with desidered **accessibility and behavioral restrictions**.

#### POD
Sometimes you will not need a `class`. Programming in C++ does not mean putting some classes everywhere.
***POD*** stands for **Plain Old Data**, it describes types that have **simple**, **C-compatible** memory layout and behavior.

A type is *POD* if it satisfies two requirements:
- [Trivial](#constructor) (simple construction/destruction)
- Standard layout (C-compatible memory layout)

To check if the `class` you created is POD (and then should -*in most cases*- be a struct), you can use the [UnaryTypeTrait](https://en.cppreference.com/w/cpp/named_req/UnaryTypeTrait.html) (class [template](https://www.geeksforgeeks.org/cpp/templates-cpp/)) `std::is_pod<T>` (⚠️ C++11) as follows:

```cpp
std::cout << std::is_pod<Class>::value << std::endl; 
```

> ℹ️ See 
>- [Passive data structure](https://en.wikipedia.org/wiki/Passive_data_structure) (wikipedia.org) and [Plain Old Object (C++)](https://en.wikipedia.org/wiki/Plain_Old_C%2B%2B_Object) (wikipedia.org)
>- [std::is_pod](https://cplusplus.com/reference/type_traits/is_pod/) (cplusplus.com) or [std::is_pod](https://en.cppreference.com/w/cpp/types/is_pod.html) (cppreference.com)
>- [Template](https://www.en.cppreference.com/w/cpp/language/templates.html) (cppreference.com) or [Templates in C++](https://www.geeksforgeeks.org/cpp/templates-cpp/) (geeksforgeeks.org)

#### Choose the adequate datatype

|   Use Case                            |  Prefer `struct` |   Prefer `class`  |
|   ----------------------------------- | ---------------  |   --------------- |
|   Passive data grouping               |  Yes             |   No              |
|   Complex invariants/behavior         |  No              |   Yes             |
|   Encapsulation required              |  No              |   Yes             |
|   C compatibility                     |  Yes             |   No              |
|   Methods controlling the data        |  No              |   Yes             |
|   Real life object abstraction        |  No              |   Yes             |

> ℹ️ See 
>- [Invariant](https://www.geeksforgeeks.org/cpp/what-is-class-invariant) (geeksforgeeks.org)
>- [Classes and class hierarchy](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rc-struct) (isocpp.github.io)

### Quiz

// QUIZZZZZZZZZZZZZZZZZZZ

***

## Objects

An object is an entity created as an **instance** of a `class`.
There can be as many objects of a class as desired.

```cpp
// main.cpp
ClassName  objectName;
```

> *C++ programs create, destroy, refer to, access and manipulate objects.* 
>
> ℹ️ See [Object](https://en.cppreference.com/w/cpp/language/object.html) (cppreference.com)


### Objects can be sorted in differents categories...
- Fundamental type objects

```cpp
int x = 42;
```

- Pointer objects :

```cpp
int *ptr;
```

- Array objects :

```cpp
int array[42];
```

- Class-type objects

```cpp
std::string str;
```

### ...and they have differents key characteristics.

- **Type**: Each determined at compile-time (C++ is a statically typed language).

```cpp
typeid(variable).name()
```

- **Storage**: Objects occupy memory with specific size.

```cpp
sizeof(variable)
```

- **Lifetime**: Objects have well-defined creation and destruction points.
    - **Memory allocation**: The compiler reserves memory for the object. For **stack** objects, memory is allocated when execution enters the block while for **heap** objects (via `new`), memory is allocated in the available memory space. 
    - **Constructor execution**: Class-type objects invoke a [constructor](#construct-and-destruct-an-instance) that initializes **member** variables and *may acquire resources*.
        Constructors can be **default** (no parameters), **parameterized**, or **copy** constructors (cf. [Orthodox Canonical class form](#orthodox-canonial-form)).
        During this phase, ***base class*** (!= derived) constructors run first (for inheritance), then member objects' constructors, and finally the enclosing class's constructor itself.
    
    - **Lifetime begins**: Once construction is complete, the object becomes *usable*. 

    - **Destructor execution**: When the object's lifetime ends (either automatic [scope](#scope) exit or `delete` for dynamic objects), its [destructor](#construct-and-destruct-an-instance) runs. The **destructor** is responsible for releasing resources.
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

***

## Members: attributes & methods

### Encapsulation

Encapsulation is the object-oriented principle of bundling data ([state](#objects)) and behavior (methods) into a class and restricting direct access to some of the object’s components. This enforces **modularity**, **maintainability**, and **robustness**.

- **Information hiding**: Prevent external code from depending on internal representations.
- **Maintain invariants**: Control how **state changes** so the object remains in a valid condition.
- **Improve compilation efficiency**: **Minimize dependencies between modules**.
- **Increase robustness**: Prevent **misuse** of an object’s **internal data**.

### Attributes

Attributes are variables (often called *data members*) that belong to the class and **define the properties** of the objects instanciated from this class.

### Methods

Methods are functions (often called *member functions*) that belong to a determined class. They are used to **manipulate the class's data** members.

### Access specifiers

In C++, you can restrict the visibility and the accessibility to determined class members. C++ provides three access specifiers :

- **`public`** : accessible from **anywhere** in the code. May be useful notably for **public constants**, **constructors and destructors**.
- **`private`** : only accessible within the class. Core-concept of [encapsulation](#encapsulation). A well-designed class keep private a maximum amount of data, while having some ***getters/setters*** to respectively read or modify them.
- **`protected`** : accessible within [base and derived](#inheritence) classes. Useful to build *in-class sub-restrictions*.

|   Keyword         |   *Within* the class  |   From *derived* classes  |   From *outside*  the class    |
|   --------------- | --------------------- | ------------------------- |   ---------------------------- |
|   **`public`**      |   yes               |   no                      |   yes                          |
|   **`private`**     |   yes               |   no                      |   no                           |
|   **`protected`**   |   yes               |   yes                     |   no                           |

By default, each member declared inside a class is **private**, which means that only an instance of the class can access it.

### Example
/////////////////////////////////////////////TO REBUILD ADDING class Shape to show protectd
```cpp
// Rectangle.hpp
class Rectangle
{
    public:                                         // accessible outside the class
        Rectangle();                                // default constructor
        ~Rectangle();                               // default destructor
        resizeRectangle(int width, int height);     // method
    
    private:                                        // accessible ony inside the class (default)
        int _width;                                 // attribute
        int _height;                                // attribute
};
```
### `static` and `const` keywords

***

## Scope

***

## Namespaces

***

## Streams

***

## Init lists

***

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
