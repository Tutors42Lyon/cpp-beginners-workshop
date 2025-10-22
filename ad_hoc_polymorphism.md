# Ad-hoc Polymorphism, Operator Overloading, and the Orthodox Canonical Class Form

## Table of contents

- [Ad-hoc Polymorphism](#ad-hoc-polymorphism)
- [Operator Overloading](#operator-overloading)
- [Why Use Operator Overloading](#why-use-operator-overloading)
- [The Orthodox Canonical Class Form (OCCF)](#the-orthodox-canonical-class-form-occf)
- [Why Follow the Canonical Class Form](#why-follow-the-canonical-class-form)
- [Deep Copy vs Shallow Copy](#deep-copy-vs-shallow-copy)
- [Notes on Safety and Edge Cases](#notes-on-safety-and-edge-cases)
- [Conclusion](#conclusion)
- [Sources](#sources)

## Ad-hoc Polymorphism

### Concept

Ad-hoc polymorphism  is the ability for functions or operators to behave differently depending on the types of their operands. This is achieved with function overloading and operator overloading. The choice of which function to call is resolved at compile time.

### Example: Function Overloading



```cpp
#include <iostream>

void print(int x) {
    std::cout << "Integer: " << x << std::endl;
}

void print(double x) {
    std::cout << "Double: " << x << std::endl;
}

int main() {
    print(10);      // Calls print(int)
    print(3.14);    // Calls print(double)
    return 0;
}
```
**Expected Output:**
```
Integer: 10
Double: 3.14
```

## Operator Overloading

Operator overloading lets user defined types behave syntactically like built in types when using operators (+, -, <<, etc.). Use operator overloading to make APIs expressive and intuitive, but always respect conventional operator semantics. //WIP

### Example: Overloading


Cannot overload operators for primitive types only

```cpp
int operator+(int a, int b) {  // Won't compile
    return a * b;
// At least one operand must be a user defined type
}
```

Cannot change number of operands
```cpp
class Point {
    // - As member function: must take exactly 1 parameter
    // - As non-member function: must take exactly 2 parameters
    Point operator+(int a, int b) {  // Won't compile - too many params
        return *this;
    }
};
```

### Example: Overloading
```cpp
#include <iostream>

class Point {
public:
    int x;
    int y;

    Point(int _x , int _y ) : x(_x), y(_y) {

    }

    Point operator+(const Point& other) const {
		std::cout << "Hello  from ovelorloading +" << std::endl;
        return Point(x + other.x, y + other.y);
    }
};

std::ostream& operator<<(std::ostream& os, const Point& p) {
	os << "Hello from ovelorloading << " << std::endl;
    os << "x: " << p.x << "\n" << "y: " << p.y;
    return os;
}

int main() {
    Point p1(1, 2);
    Point p2(3, 4);
    Point p3 = p1 + p2; // Uses overloaded +
    // is equivalent to do: Point p3 = p1.operator+(p2);
    std::cout << p3 << std::endl; //  operator<<(std::cout, p)
    return 0;
}
```

**expected Output:**
```
Hello  from ovelorloading +
Hello from ovelorloading <<
x: 4
y: 6
```


### Operator Overloading: Best Practices

- Keep operator meaning intuitive.
- Prefer non-mutating `operator+` returning a new object.
- Overload related operators consistently. (if overload +, consider overloading += same with < and <=)
- Avoid confusing overloads

## Why Use Operator Overloading

Operator overloading allows user-defined types to behave naturally with the language’s syntax. Here’s why it’s useful:

- **Expressiveness:** Objects can be manipulated using familiar operators (`+`, `-`, `<<`), making the code more readable and closer to the problem domain. //WIP
- **Ergonomics:** A well-designed class that overloads operators can offer an intuitive interface — for example, adding points or concatenating strings.


## The Orthodox Canonical Class Form (OCCF)

### Concept

The OCCF is a set of rules to write well-behaved classes  provide a destructor, copy constructor, and copy assignment operator when your class manages resources (the Rule of Three). This prevents resource leaks and double-free errors.

### Example: MyString implementing OCCF


```cpp
#include <iostream>
#include <cstring>

class MyString {
private:
    char* data;
    size_t length;

public:
    // Constructor
    MyString(const char* str) : length(std::strlen(str)) {
        data = new char[length + 1];
        std::strcpy(data, str);
    }

    // Destructor
    ~MyString() {
        delete[] data;
    }

    // Copy constructor
    MyString(const MyString& copy) : length(copy.length) {
        data = new char[length + 1];
        std::strcpy(data, copy.data);
		std::cout << "Hello from copy function" << std::endl;
    }

    // Copy assignment operator
    MyString& operator=(const MyString& assign) {
        if (this != &assign) { // without self check could crash
            delete[] data; // because we delete existing data
            length = assign.length;
            data = new char[length + 1]; // and try to have access to deleted memory
            std::strcpy(data, assign.data);
			std::cout << "Hello from assign function" << std::endl;
        }
        return *this;
    }

    const char* c_str() const {
		return data;
	}
};

int main() {
    MyString s1("Hello");
    MyString s2 = s1; // copy constructor because s2 is being created
	MyString s3(s2); // copy constructor
	MyString s4("test");
    s4 = s1; // assignment operator

    std::cout << "s1: " << s1.c_str() << std::endl;
    std::cout << "s2: " << s2.c_str() << std::endl;
    std::cout << "s3: " << s3.c_str() << std::endl;
	std::cout << "s4: " << s4.c_str() << std::endl;
    return 0;
}
```

**Expected Output:**

```
Hello from copy function
Hello from copy function
Hello from assign function
s1: Hello
s2: Hello
s3: Hello
s4: Hello
```

## Why Follow the Canonical Class Form

Following the *Orthodox Canonical Class Form* (the *Rule of Three*) means explicitly managing construction, copy, and destruction operations when your class owns managed resources (such as memory, files, or handles).

### Benefits

- **Memory safety:** Prevent leaks and double frees by controlling copy and destruction behavior.
- **Predictability:** Copy and assignment behavior is explicit and clear to users of the class.

### In Practice

Always implement **at least** the destructor, copy constructor, and copy assignment operator if your object allocates resources dynamically.


## Deep Copy vs Shallow Copy

Shallow copy only copies the pointer values, not the data they point to. This leads to multiple objects pointing to the same memory, causing double free crashes and data corruption.

```cpp
#include <iostream>
#include <cstring>

class BadString {

private:
    char* data;
public:
    BadString(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }

    BadString(const BadString& copy) : data(copy.data) {

    }

    ~BadString() {
        delete[] data;
    }
};

int main() {
    BadString s1("Hello");
    BadString s2 = s1;  // SHALLOW COPY  both point to same memory
    // When s2 and s1 go out of scope:
    // s2 destructor: delete[] data (OK)
    // s1 destructor: delete[] data (CRASH! Already deleted!)
}
```

### Notes on Safety and Edge Cases

- Self-assignment guard in `operator=` prevents deleting data and copying from an already freed buffer.
- Prefer strong exception safety: allocate new resources before deleting old ones (not shown here for simplicity). In C++98 you can implement a copy-and-swap idiom for strong safety.
- Object doesn't exist yet  = → Copy constructor
- Object already exists = → Assignment operator
- Both copy constructor and operator= need to handle deep copying
- But operator= must ALSO handle cleanup of existing resources

## Conclusion

Ad-hoc polymorphism (via overloading), operator overloading, and following the OCCF (Rule of Three) are foundational. Together they let you write expressive, efficient, and safe classes when you manage resources manually.

## Sources:

#### ad-hoc polymorphism
- https://www.geeksforgeeks.org/cpp/cpp-polymorphism/
- https://stungeye.github.io/Programming-1-Notes/docs/11-pointers/05-pointers-and-polymorphism.html
- https://en.cppreference.com/w/cpp/language/overload_resolution.html
- https://www.learncpp.com/cpp-tutorial/introduction-to-function-overloading/

#### overloading operators
- https://www.geeksforgeeks.org/cpp/operator-overloading-cpp/
- https://en.cppreference.com/w/cpp/language/operators.html
- https://www.learncpp.com/cpp-tutorial/introduction-to-operator-overloading/

#### Shallow vs Deep Copy
- https://www.educative.io/answers/difference-between-shallow-copy-and-deep-copy-in-cpp
- https://www.geeksforgeeks.org/cpp/shallow-copy-and-deep-copy-in-c/

#### Orthodox Canonical Class Form
- https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#S-ctor
- https://riceset.com/C++/The-Orthodox-Canonical-Class-Form

---