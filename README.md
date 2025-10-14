# Sources :
- [ISO CPP]( https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-interfaces)
- [Stroustrup.com](https://www.stroustrup.com/)
- [CPP Reference](https://cppreference.com/)

***

# 1.  C++ Context, Philosophy, and Compilation
## A. Philosophy
## B. Compilation

# 2.  Namespaces, classes, member functions, stdio streams, initialization lists, static, const, and some other basic stuff
## A. Objects

C++ programs create, destroy, refer to, access and manipulate objects.

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

- Class type objects

```c++
std::string str;
```
### ...and have differents key characteristics

- **Type**: Each determined at compile-time (C++ is statically typed)

```
typeid(variable).name()
```

- **Storage**: Objects occupy memory with specific size and alignment requirements.

```c++
sizeof(variable)
```

- **Lifetime**: Objects have well-defined creation and destruction points.
    - ***Storage Allocation***: The compiler reserves memory for the object. For stack objects, memory is allocated when execution enters the block; for heap objects (via new), memory is allocated in the free store.

    - ***Constructor Execution***: Class-type objects invoke a constructor (special member function sharing the class name) that initializes member variables and may acquire resources. Constructors can be default (no parameters), parameterized, or copy constructors. During this phase, base class constructors run first (for inheritance), then member objects' constructors, and finally the enclosing class's constructor itself.
    
    - ***Lifetime Begins***: Once construction is complete, the object becomes fully usable. For automatic variables, lifetime lasts within their block scope; for dynamic objects, as long as the pointer is valid.
    
    - ***Destructor Execution***: When the object's lifetime ends (either automatic scope exit or delete for dynamic objects), its destructor runs. This special member function (~ClassName) is responsible for releasing resources—files, memory, sockets, etc. For class hierarchies, destructors run in reverse: the enclosing class destructor first, then member objects' destructors, and finally base class destructors.
    
    - ***Storage Deallocation***: Memory is released back to the system, either automatically for stack objects or manually for heap objects with delete.

- **Identity**: Objects have addresses in memory (except for bit-fields and register variables).
- **State**: Objects maintain internal data that can change over time.
- **Value**: The current contents of the object's storage.

### Quiz
- Is a class an object ? [Y/N] (N)

## A. Namespaces
## B. Classes
## C. Members functions/attributes
## D. Streams
## E. Init lists
## F. Static/Const
## G. std::string/std::stringstream

***

# 3.  Memory allocation, pointers to members, references and switch statements
## A. New/Delete
## B. Pointers/References
## C. Switch statememts

***

# 4.  Ad-hoc polymorphism, operator overloading and the Orthodox Canonical class form

***

# 5.  Inheritence

***

# 6.  Subtype Polymorphism, Abstract Classes, and Interfaces

***
