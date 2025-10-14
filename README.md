# Sources :
- [ISO CPP]( https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-interfaces)
- [Stroustrup.com](https://www.stroustrup.com/)
- [CPP Reference](https://cppreference.com/)

***

# C++ Context, Philosophy, and Compilation
## Philosophy
## Compilation

# Basic stuff
## Objects

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
### ...and have differents key characteristics.

- **Type**: Each determined at compile-time (statically typed language)

```c++
typeid(variable).name()
```

- **Storage**: Objects occupy memory with specific size and alignment requirements.

```c++
sizeof(variable)
```

- **Lifetime**: Objects have well-defined creation and destruction points.
    - ***Memory Allocation***: The compiler reserves memory for the object. For **stack** objects, memory is allocated when execution enters the block while for **heap** objects (via `new`), memory is allocated in the available memory space. 
        > See [3. Memory allocation, pointers & references](#memory-allocation,-pointers-&-references).

    - ***Constructor Execution***: Class-type objects invoke a **constructor** (special member function sharing the class name) that initializes **member** variables and *may acquire resources*. Constructors can be **default** (no parameters), **parameterized**, or **copy** constructors (cf. *Orthodox Canonical class form*). During this phase, base class (≠ derived) constructors run first (for inheritance), then member objects' constructors, and finally the enclosing class's constructor itself.
        > See [5. Inheritence](#inheritence).
    
    - ***Lifetime Begins***: Once construction is complete, the object becomes fully usable. For automatic variables, lifetime lasts within their block scope; for dynamic objects, as long as the pointer is valid.
    
    - ***Destructor Execution***: When the object's lifetime ends (either automatic scope exit or delete for dynamic objects), its destructor runs. This special member function (~ClassName) is responsible for releasing resources—files, memory, sockets, etc. For class hierarchies, destructors run in reverse: the enclosing class destructor first, then member objects' destructors, and finally base class destructors.
        > See [5. Inheritence](#inheritence).
    
    - ***Memory Deallocation***: Memory is released back to the system, either automatically for stack objects or manually for heap objects with delete.
        > See [3. Memory allocation, pointers & references](#memory-allocation,-pointers-&-references).

- **Identity**: Objects have addresses in memory (except for bit-fields and register variables).
- **State**: Objects maintain internal data that can change over time.
- **Value**: The current contents of the object's storage.

### Quiz
- Is a class an object ? [Y/N] (N)

## Namespaces
## Classes
## Members functions/attributes
## Streams
## Init lists
## Static/Const
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
