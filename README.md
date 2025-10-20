# ℹ️ Sources :
- [ISO CPP]( https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-interfaces)
- [C++ Classes and Objects (GeeksForGeeks)](https://www.geeksforgeeks.org/cpp/c-classes-and-objects/)
- [Stroustrup.com](https://www.stroustrup.com/)
- [cppreference](https://cppreference.com/)

***

# C++ Context, Philosophy, and Compilation
## Philosophy
[Bjarne Stroustrup](https://www.stroustrup.com/), the creator of C++, designed the language with a philosophy centered on providing **flexibility** without sacrificing **efficiency or performance**.
- **General-purpose**: C++ should be usable for low-level system programming, as well as for high-level abstractions.
- **Multi-paradigm**: Support for procedural, object-oriented, and generic programming paradigms.
- **Control over Resources**: Features like constructors, destructors, and RAII (Resource Acquisition Is Initialization) provide systematic and safe resource management.
- **Extensibility and Compatibility**: Designed to extend C without breaking compatibility, allowing programs to grow from C to C++ smoothly.
- **Type Safety with Flexibility**: Strong static typing that supports user-defined types while allowing low-level access when needed.

### Some applications

- **Adobe Systems** : All major applications are developed in C++:
    - Photoshop & ImageReady,
    - Illustrator,
    - Acrobat,
    - InDesign,
    - GoLive,
    - Frame (mostly C, some C++) 

- **Apple** :
    - Finder
    - IOKit device drivers. (IOKit is the only place where we use C++ in the kernel, though.)

- **Dassault Systems** :
    - Catia v5 (CAD) on which was notably conceived all recent Airbus planes.

- **Microsof** t: Literally everything at Microsoft is built using recent flavors of Visual C++ (using older versions would automatically cause an application to fail the security review). The list would include major products like:
    - Windows XP, Vista, System 7
    - Windows NT (NT4 and 2000)
    - Windows 9x (95, 98, Me)
    - Microsoft Office (Word, Excel, Access, PowerPoint, Outlook)
    - Internet Explorer (including Outlook Express)
    - Visual Studio (Visual C++, Visual Basic, Visual FoxPro) (Some parts of Visual Studio like the Base Class Libraries that ship with the .NET Framework were written using C# but the C# compiler itself is written in C++.)
    - Exchange
    - SQL 
    
- **KDE** (K Desktop Environment) : powerful Open Source graphical desktop environment for Unix workstations. It is one of the leading desktop environments for Linux. It consists of about 300 different packages written in C++, including an office suite, a browser, development tools, games, and multimedia apps.

> ℹ️ See [Applications](https://www.stroustrup.com/applications.html) (stroustrup.com)

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
    public:
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

- If any constructor is declared, but **none is a default constructor**, the compiler **does not** generate one.

- If **none** is provided, compiler automatically generates one : it **only** call members and base class destructors.

```cpp
className(/* optionnal parameters */);
```

#### Destructor

The **destructor** is run when an object’s lifetime ends (when it goes out of scope or after a `delete` call), releasing resources.

- **Only one destructor** is permitted per class.

- Overloading destructors is not allowed (can never take arguments or return a value).

- Can be virtual (should be for [polymorphic](#polymorphism) base classes)

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
        Rectangle(int width, int height);   // overloaded
        ~Rectangle();                       // default
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

To check if the `class` you created is POD (and then should -*in most cases*- be a struct), you can use the [UnaryTypeTrait](https://en.cppreference.com/w/cpp/named_req/UnaryTypeTrait.html) ([class template](https://www.en.cppreference.com/w/cpp/language/class_template.html)) `std::is_pod<T>` (⚠️ C++11) as follows:

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
>- [Classes and class hierarchies](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#c-classes-and-class-hierarchies) (isocpp.github.io)

### Quiz

// QUIZZZZZZZZZZZZZZZZZZZ

***

## Objects

An object is an entity.

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

Encapsulation is the object-oriented principle of bundling **data** ([state](#objects)) and **behavior** ([methods](#methods)) into a class and **restricting direct access** to some of the object’s components. This enforces **modularity**, **maintainability**, and **robustness**.

>- **Information hiding**: Prevent external code from depending on internal representations.
>- **Maintain invariants**: Control how **state changes** so the object remains in a valid condition.
>- **Improve compilation efficiency**: **Minimize dependencies** between modules.
>- **Increase robustness**: Prevent **misuse** of an object’s **internal data**.

### Attributes

Attributes are variables (often called *data members*) that belong to the class and **define the properties** of the objects instanciated from this class.
A good practice is to have a maximum amount of `private` or `protected` data.

```cpp
int height;  
```

### Methods

Methods are functions (often called *member functions*) that belong to a determined class. They are used to **manipulate the class's data** members.
We often use simple mono use function to *get* or *set* private attributes, we call them respectively **getters** and **setters**.

```cpp
int getHeight(void);  
```

### Access specifiers

In C++, you can restrict the visibility and the accessibility to determined class members. C++ provides three access specifiers :

- **`public`** : accessible from **anywhere** in the code. May be useful notably for **public constants**, **constructors and destructors**.
- **`protected`** : accessible within [base and derived](#inheritence) classes. Useful to build *in-class sub-restrictions*.
- **`private`** : only accessible within the class. Core-concept of [encapsulation](#encapsulation).

> 💡 **Good practices tips**
>- A well-designed class keep private a maximum amount of data, while having some ***[getters/setters](#methods)*** to respectively read or modify them.
>- When building a `class`, start setting all members as `private`, then you will set as `public` only the needed members.

|   Keyword         |   *Within* the class  |   From *derived* classes  |   From *outside*  the class    |
|   --------------- | --------------------- | ------------------------- |   ---------------------------- |
|   **`public`**    |   yes                 |   no                      |   yes                          |
|   **`protected`** |   yes                 |   yes                     |   no                           |
|   **`private`**   |   yes                 |   no                      |   no                           |

By default, each member declared inside a class is **private**, which means that only an instance of the class can access it.

/////////////////////////////////////////// EXEMPLE

> ℹ️ See [Inheritence](#inheritence)

### Some other keywords

#### `static`

A `static` data member is useful for maintaining a shared data among all instances of the class. It is declared in the **`class` declaration**

- **Only one copy** of that member is created for all instances of a given `class`.
- It is initialized **before any object** of this `class`.
- Its lifetime is the **entire program**.

#### `const`

#### `mutable`

***

## Scope


### Prequisites

Before starting digging into C++ scopes, we have to understand these three critical properties and their relationships :

- **Scope** is a fundamental compile-time concept that defines the *region of the program* where an identifier name can be used to **refer to its entity**.
    > *Where can I use this name ?* 
    >
    > ℹ️ See [Scope](https://en.cppreference.com/w/cpp/language/scope.html) (cppreference.com)

- **Lifetime** (or *storage duration*), determines when an object exists in memory : the time spent between memory allocation and deallocation. The concept of **lifetime** differs from the **scope**: a variable may go out of scope while its memory persists.
    > *When does this name exists ?* 
    >
    > ℹ️ See :
    >- [Objects](#objects)
    >- [Memory allocation, pointers and references](#memory-allocation-pointers--references)

- **Visibility** concerns whether an identifier can actually be accessed within a particular scope, during its lifetime, which may be **restricted** by **[access specifiers](#access-specifiers)**, **name hiding** or **linkage**.
    > *Can I actually access this ?* 
    >
    > ℹ️ See [Variable shadowing (name hiding)](https://www.learncpp.com/cpp-tutorial/variable-shadowing-name-hiding/) (leancpp.com)
    >> 💡 **Good to know**
    >>
    >> Linkage refers to the **visibility** of variables/functions **across different translation units**.
    >>
    >> ℹ️ See :
    >>- [Translation units](https://en.wikipedia.org/wiki/Translation_unit_%28programming%29) (wikipedia.org)
    >>- [Translation units and linkage](https://learn.microsoft.com/en-us/cpp/cpp/program-and-linkage-cpp?view=msvc-170) (learn.microsoft.com) and [Language Linkage](https://en.cppreference.com/w/cpp/language/language_linkage.html) (wikipedia.org)
    >>- [C++ Linkage explained](https://cppscripts.com/cpp-linkage) (cppscripts.com)



### Fundamental Scopes

C++ defines **fundamental scope categories**, each with distinct rules governing the identifier **[visibility](#prequisites)** and **behavior**.

#### Global Scope (Namespace Scope/File Scope)

**Global scope** (or **namespace scope** in C++, **file scope** in C), encompasses declarations made outside any function, class or explicit [namespace](#namespaces).

An identifier declared at **global scope** is **visible** from is declaration to the end of the **translation unit** (source file and all included headers after preprocessing).

There is exactly one instance of each global variable throughout program execution (unless declared with **internal linkage**).

Global identifiers have external linkage by default, making them visible across translation units. The static keyword gives them internal linkage, restricting visibility to the current translation unit:
```cpp
// linkage.cpp
int external_var = 1;        // external linkage (visible across files)
static int internal_var = 2; // internal linkage (local to this file)
```
> ℹ️ See :
>- [Translation units](https://en.wikipedia.org/wiki/Translation_unit_%28programming%29) (wikipedia.org)
>- [Translation units and linkage](https://learn.microsoft.com/en-us/cpp/cpp/program-and-linkage-cpp?view=msvc-170) (learn.microsoft.com) and [Language Linkage](https://en.cppreference.com/w/cpp/language/language_linkage.html) (wikipedia.org)
>- [C++ Linkage explained](https://cppscripts.com/cpp-linkage) (cppscripts.com)

```cpp
 // globalScope.cpp
 int g_value = 42;  // g_value's scope begins here

 void foo()
 {
     // g_value is visible here
 }

 void bar()
 {
     // g_value still visible here
 }
 // ...
 // g_value's scope ends at the end of the translation unit
 ```

In C++, *global identifiers* technically reside within the **implicit global namespace**. You can refer to ***global names*** using the **[scope resolution operator `::`](#operator-overloading)** whith no prefix `::name`.

> ⚠️ Local variables can hide global names, here comes the **scope resolution operator** :
>
> ```cpp
> // globalScope.cpp
> int   value = 42;
> 
> int   main(void)
> {
>   int value = 21;
>   
>   std::cout << value << std::endl;    // displays 21 (local)
>   std::cout << ::value << std::endl;  // displays 42 (global)
>
>   return (0);
> }
> ```

#### Local Scope (Block Scope)

**Local scope** or **Block Scope** encompasses any identifier declared within a compound statement delimited by curly braces `{}`, from the declaration.

A block is created by any pair of braces `{}`, including :
- function bodies
- loop bodies
- conditionnal statements
- standalone braces

Variables declared within a block have **automatic storage duration** by default : they are **created** when execution reaches their declaration and **destroyed** when the block exits.

> ⚠️ You cannot use a variable before its declaration point within the same block, even though the entire block constitutes its scope.

```cpp
// localScope.cpp
void    example(void)
{
    int x = 42;     // x's scope begins
    {               // standalone brackets
        int y = x;  // y's scope begins
    }               // y's scope ends
}                   // x's scope ends
```
> ⚠️ Controls structures (`if`, `while`, `for`, `switch`...) create their own scopes with specific rules.
>
> Example :
>- In a `for` loop if the *iterator* is declared into the statement, it is not accessible outside of the loop and cannot be redeclared in the loop body.
>   ```cpp
>   for (int i = 0; i < 42; ++i) // i's scope begins
>   {
>       // i cannot be redeclared
>       std::cout << i << std::endl;
>   } // i's scope ends
>   ```
>- In a `switch` statement, a **single scope** is created, encompassing all case labels and their statements.
>   ```cpp
>   switch (value)
>   {
>       int x;      // x's scope extends through entire switch
>       case (1):
>           x = 10; // assignment (not initialization)
>           break;
>       case (2):
>           break; // x is in scope but may be uninitialized
>   }
>   ```
>   Initialization within case statements requires careful consideration. You cannot initialize a variable in one case **if subsequent cases can access it without that initialization occurring**, as this would violate initialization safety.
>   
>   ℹ️ See [Switch fallthrough and scoping](https://www.learncpp.com/cpp-tutorial/switch-fallthrough-and-scoping/) (learncpp.com)

#### Function Parameters Scope

**Function parameters scope** is the **most limited** scope type, applying only to parameter names in function declaration.

> ```cpp
> // functionParametersScope.cpp
> void invalid(int x, int x);   // duplicate parameter name
> void valid(int x, int y);
> ```

#### Namespace

**Namespaces** provide a method for preventing name conflicts in large projects. All entitity that are declared inside a namespace are automatically placed in a [namespace scope](#namespace-scope) which prevents them from being mistaken for **indentically-named entities** in other scopes.
Otherwise, entities that are declared outside a namespace belong to the [global scope (Namespace Scope/File Scope)](#global-scope-namespace-scope-file-scope).

- **Scope Management**: Namespaces define where an identifier is **visible**.
- **Modularity**: Code can be **grouped logically**.
- **Name Collision Avoidance**: Identical identifiers in different namespaces are **distinct**.

A **namespace** is defined as follows :

```cpp
namespace Foo 
{
    int value = 42;
    int func(void)
    {
        return (42);
    }
}
```

You can access namespace members from different ways:

- **Fully Qualified Members** : Access each member using the **complete namespace path**.

```cpp
Foo::value++;
Foo::func();
```

- **Using Declaration**: Introduce a **single member** into the current scope.
```cpp
using Foo::func;

Foo::value++;
func();
```

- **Using Directive**: Introduce **all members** into the current scope. **(⚠️ NOT SAFE)**

```cpp
using namespace Foo;

value++;
func();
```
##### Example

```cpp
// namespaceScope.cpp
int value = 42;
int func(void) { return (4); }

namespace   Foo
{
    int value = 21;
    int func(void)
    {
        return (2);
    }
}

namespace   Bar
{
    int value = 84;
    int func(void)
    {
        return (8);
    }
}

namespace   Muf = Foo;

int	main(void)
{
    std::cout   << Foo::value << std::endl      // displays 21 (Foo's namespace scope) 
                << Foo::func() << std::endl;    // displays 2   (Foo's namespace scope)

    std::cout   << Bar::value << std::endl      // displays 84 (Bar's namespace scope)
                << Bar::func() << std::endl;    // displays 8 (Bar's namespace scope)

    std::cout   << Muf::value << std::endl      // displays 21 (Muf(= Foo)'s namespace scope)
                << Muf::func() << std::endl;    // displays 2 (Muf(= Foo)'s namespace scope)

    std::cout   << ::value   << std::endl       // displays 42 (global)
                << ::func() << std::endl;       // displays 4 (global)
}
```
##### Quiz

1. According to the previous example, what would be outputed by the following code ?
```cpp
std::cout   << value << std::endl
            << func() << std::endl;
```

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
## Polymorphism
***

# Inheritence

***

# Subtype Polymorphism, Abstract Classes, and Interfaces

***
