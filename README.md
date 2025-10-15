# Sources :
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


## Objects
`className  objectName;`

![img_class&object](./assets/Class_Object_example.webp)
> *C++ programs create, destroy, refer to, access and manipulate objects.* 
>
> See [Object](https://en.cppreference.com/w/cpp/language/object.html)

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

- **Type**: Each determined at compile-time (statically typed language)

```c++
typeid(variable).name()
```

- **Storage**: Objects occupy memory with specific size and alignment requirements.

```c++
sizeof(variable)
```

- **Lifetime**: Objects have well-defined creation and destruction points.
    - ***Memory allocation***: The compiler reserves memory for the object. For **stack** objects, memory is allocated when execution enters the block while for **heap** objects (via `new`), memory is allocated in the available memory space. 
        > See [3. Memory allocation, pointers & references](#memory-allocation-pointers--references).

    - ***Constructor execution***: Class-type objects invoke a **constructor** (special member function sharing the class name) that initializes **member** variables and *may acquire resources*. Constructors can be **default** (no parameters), **parameterized**, or **copy** constructors (cf. *Orthodox Canonical class form*). During this phase, base class (≠ derived) constructors run first (for inheritance), then member objects' constructors, and finally the enclosing class's constructor itself.
        > See [5. Inheritence](#inheritence).
    
    - ***Lifetime begins***: Once construction is complete, the object becomes *usable*. 

    - ***Destructor execution***: When the object's lifetime ends (either automatic scope exit or delete for dynamic objects), its destructor runs. This special member function (~ClassName) is responsible for releasing resources—files, memory, sockets, etc. For class hierarchies, destructors run in reverse: the enclosing class destructor first, then member objects' destructors, and finally base class destructors.
        > See [5. Inheritence](#inheritence).
    
    - ***Memory deallocation***: Memory is released back to the system, either automatically for stack objects or manually for heap objects with delete.
        > See [3. Memory allocation, pointers & references](#memory-allocation-pointers--references).

- **Identity**: Each object has a unique address in memory (except for [bit-fields](https://en.wikipedia.org/wiki/Bit_field) and [register variables](https://en.wikipedia.org/wiki/Register_(keyword))).

    An object is addressable if:

    - ***Occupies Memory***: Has a location in the program's address space

    - ***Byte-Aligned***: Can be referenced by a byte address (eg. bit-field)

    - ***Observable***: Not completely optimized away by the compiler

- **State**: Objects maintain internal data that can change over time (eg. register variables)
    
    An object's state is the **complete set of values held by all its member variables** (data members), at anytime during runtime.
    The state represents the object's "*configuration*" that distinguishes one instance from another of the same type.

    !Static members belong to the class as a whole, not an individual object's state.

    ```cpp
    class Rectangle
    {
        int width;
        int height;
    };

    Rectangle rect;

    rect.width = 4;
    rect.height = 2;
    ```
    The **state** of `rect` (instance from the `Rectangle` class) is, at a given instant during runtime: `{width = 4; height = 2;}`.

- **Value**: An object's value generally refers to the *abstract* interpretation of the **object's state as a single meaningful entity** used in computations or comparisons.

    ```

    ```

### Quiz
- Is a class itself an object ? [Y/N] (N)

## Members functions/attributes
## Static/Const
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
