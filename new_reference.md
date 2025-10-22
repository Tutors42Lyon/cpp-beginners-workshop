# CPP Beginner

## New - delete

### Single Object Allocation

```cpp
int* ptr = new int(10);  // Allocate and initialize

delete ptr;              // Deallocate
```

### Array Allocation

```cpp
int* arr = new int[5];   // Allocate array

delete[] arr;            // Note: delete[] for arrays
```

```cpp
delete[] arr;            // Undefined behavior: double free
```

### Another Array Allocation

```cpp
int* arr2 = new int[5];  // Allocate another array

delete arr2;             // Undefined behavior - compilation error with flags
```

### Advantages of new/delete over malloc/free

**Type Safety**: Unlike `malloc()` which returns `void*` requiring explicit casting, `new` automatically returns appropriately typed pointers. This eliminates casting errors.

**Constructor/Destructor Integration**: The most crucial distinction lies in object lifecycle management. `new` automatically calls constructors during memory allocation, while `malloc()` provides uninitialized memory. Similarly, `delete` ensures proper cleanup by calling destructors, whereas `free()` simply deallocates memory without cleanup.

**Automatic Size Calculation**: When using `new`, you don't need to calculate object sizes manually, as the compiler handles this automatically.

**Exception on Failure**: The `new` operator throws a `std::bad_alloc` exception on allocation failure, which can only be caught with `try-catch` blocks.

### Class Array Example

```cpp
#include <iostream>

class test {
    public:
        test(): n(3) {
            nbs = new int[100];
            std::cout << "Constructor called" << std::endl;
        };

        ~test() {
            std::cout << "Destructor called" << std::endl;
            delete[] nbs;
        };

        void setValue(int nb) {
            n = nb;
        };

        int getValue() {
            return n;
        };

    private:
        int n;
        int* nbs;
};


int main() {

    test* tab = new test[4];
    tab[1].setValue(5);
    tab[2].setValue(10);
    tab[3].setValue(20);

    for (int i = 0; i < 4; i++) {
        std::cout << "Value of " << i << " = " << tab[i].getValue() << std::endl;
    }

    delete[] tab;
}
```

**Expected Output**

```
Constructor called
Constructor called
Constructor called
Constructor called
Value of 0 = 3
Value of 1 = 5
Value of 2 = 10
Value of 3 = 20
Destructor called
Destructor called
Destructor called
Destructor called
```

## Pointer vs Reference

### Reference Declaration

**Valid reference declaration**

```cpp
int x = 10;
int& ref = x;      // Valid: reference initialized with variable
```

**Invalid reference declaration**

```cpp
int& ref;          // Compilation error: reference must be initialized
```

**Reference reassignment**

```cpp
int a = 10;
int b = 20;
int& ref = a;      // ref refers to a
ref = b;           // This copies the value of b into a, doesn't rebind ref to b
```

**Null reference**

```cpp
int& ref = NULL;    // Compilation error: cannot bind reference to null
```

### Function Prototypes with References

```cpp
// Pass by value (creates a copy)
void modifyValue(int value);

// Pass by reference (modifies original)
void modifyReference(int& value);

// Pass by const reference (read-only, no copy)
void readValue(const int& value);

// Return by reference
int& getElement(std::vector<int>& vec, int index);

// Const reference return (read-only access)
const std::string& getName() const;
```

### Comparison Table

|                     | Pointers                                                      | References                                                    |
|:--------------------|:--------------------------------------------------------------|:--------------------------------------------------------------|
| Syntax              | `*`                                                           | `&`                                                           |
| NULL assignment     | Possible                                                      | Not possible                                                  |
| Reassignment        | Possible                                                      | Not possible                                                  |
| Address of variable | Stores the address (pass by reference) - `int *p = &a`        | Refers to the address (pass by value) - `int &p = a`          |
| Indirection level   | Multiple levels (pointer to pointer...)                       | Single level of indirection                                   |

### Advantages of References

**Cleaner Syntax**: References provide a more intuitive syntax compared to pointers. You can use them like regular variables without dereferencing operators `*`, making code more readable and less error-prone.

**Guaranteed Initialization**: References must be initialized when declared, preventing dangling or null references. This compile-time safety eliminates a common source of runtime errors that occur with uninitialized pointers.

**No Null References**: Unlike pointers which can be null, references always refer to a valid object. This eliminates the need for null checks and reduces defensive programming overhead.

**Immutable Binding**: Once a reference is bound to an object, it cannot be rebound to another object. This immutability provides clarity about what the reference points to throughout its lifetime.

**Function Parameter Safety**: When passing by reference, you get the performance benefits of pointer passing (no copying) with the safety and simplicity of value semantics, making function cleaner and safer.

### Example with Access - std::cout

```cpp
void pointersVsReferences() {

    int x = 42;

    int* ptr = &x;
    std::cout << "Original x: " << x << std::endl;
    std::cout << "Pointer value: " << *ptr << std::endl;
    std::cout << "Pointer address: " << ptr << std::endl;

    // Reference
    std::cout << std::endl;
    int& ref = x;
    std::cout << "Reference value: " << ref << std::endl;
    std::cout << "Reference address: " << &ref << std::endl;
    std::cout << "Original x address: " << &x << std::endl;

    // Modifying through pointer
    *ptr = 100;
    std::cout << "\nAfter *ptr = 100:" << std::endl;
    std::cout << "x = " << x << ", ref = " << ref << std::endl;

    // Modifying through reference
    ref = 200;
    std::cout << "\nAfter ref = 200:" << std::endl;
    std::cout << "x = " << x << ", *ptr = " << *ptr << std::endl;

}
```
**Expected Output**

```cpp
Original x: 42
Pointer value: 42
Pointer address: 0x7fff29426e9c

Reference value: 42
Reference address: 0x7fff29426e9c
Original x address: 0x7fff29426e9c

After *ptr = 100:
x = 100, ref = 100

After ref = 200:
x = 200, *ptr = 200
```
### Function Parameters: Pass by Value vs Reference vs Pointer

```cpp
void passByValueVsReference(int value, int& reference, int* pointer) {
    value++;        // Doesn't affect original
    reference++;    // Affects original
    (*pointer)++;   // Affects original
}

void functionParameters() {

    int a = 10, b = 10, c = 10;
    std::cout << "Before: a=" << a << ", b=" << b << ", c=" << c << std::endl;

    passByValueVsReference(a, b, &c);

    std::cout << "After:  a=" << a << " (unchanged)" << std::endl;
    std::cout << "        b=" << b << " (changed via reference)" << std::endl;
    std::cout << "        c=" << c << " (changed via pointer)" << std::endl;
}

```

**Expected Output**

```cpp

Before: a=10, b=10, c=10
After:  a=10 (unchanged)
        b=11 (changed via reference)
        c=11 (changed via pointer)
```