---
title: 'Copy constructors, operator overloading'
tags: [CS223, data structures, presentation, notes]

---

# Constructors, overloading

---

## First, "using"

- A "namespace" provides prefix for things
- E.g. `std::cout`
- `using namespace std`
    - makes it available without prefix
    - But makes it ALL available
    - Can lead to typos
    - Always avoid in `.h` files 

---

```cpp
#include <iostream>
#include <sstream>
using namespace std;

int main() {
    ostringstream out;
    cout << "where does this go?";
}
// without using
int main() {
    std::ostringstream out;
    cout << "compile error!";
}
```

---

Can create your own namespaces

```cpp
namespace mine {
   int cout = 5;
}
// Elsewhere
mine::cout //   <--- 5
```

---

## Static variables

- All objects share the same value
- If public, can be accessed via `Class::x`

```cpp
class Math {
public:
    static const double PI;
    static int x;
    int y;
}
const double Math::PI = 3.14159;
int Math::x = 10;

Math m1, m2;
m1.x = 5; m1.y = 10;
cout << m2.x   // 5
cout << m2.y  // ???
```

---

## Static functions

- Can be called without an object
- Don't have access to member variables
    - unless object passed as parameter

```cpp
class Math {
public:
    static double square(double n) { return n * n; }
}

Math::square(22.5);
```

---

## Friend functions

- They need to access internals of objects
- But could not be member functions
- Common for `operator<<` and `operator>>`
- `cout.operator<<(x)` same as `cout << x`
- So `operator<<` is member function for stream class
- But needs to access object internals

---

## Assignment, copy constructor

```cpp
class Temp {
    double value;
    char unit;
    Temp(double v, char u); // Normal constructor
    Temp(const Temp & t); // Copy constructor
    Temp& operator=(const Temp& t); // assignment 
}
Temp t1(25.4, 'c');
Temp t2 = t1;   // Calls copy constructor
Temp t2(t1);    // Calls copy constructor
Temp t2;
t2 = t1;        // Calls operator=
t2 = t2;        // Calls operator= Careful!  
```

---

### Simple implementations

```cpp
// Copy
Temp::Temp(const Temp& t) {
    value = t.value;
    unit = t.unit;
}
// operator=
Temp& Temp::operator=(const Temp& t) {
    value = t.value;
    unit = t.unit;
    return *this;
}
```

---

### Careful when allocating memory

- If class holds pointer to heap memory
- Default copy behavior copies address
- Must instead create a copy of the data
- Assignment must watch for `x=x` situation

---

### Move semantics

- rvalues: Don't have dedicated address
- are about to "disappear"
- e.g. the 4+5 in `f(4 + 5)`
- the result `f(x)` in `y = f(x)`
- Usually don't have to think about them
- For objects: extra copies, heap allocations
    - followed by immediate deallocations
- Move constructor/assignment
    - They transfer resources to "new owner"

---

### Move constructor

```cpp
class T {
    int *n;
    T() { n = new int; }
    T(const T& t) { //copy
        delete t;
        n = new int;
        *n = *(t.n);
    }
    T(T&& t) { //move
        int * temp = n;
        n = t.n;    // steal from t
        t.n = temp; // dump our data on t
    }
}
```

---

## Conversions

- Fraction class
- Fraction from integer (convert constructor)
- Add/subtract/etc fractions
- double from a fraction (conversion operator)
- Note: all different operations:
    - `f++`, `++f`, `f += 1`, `f = f + 1`

---

```cpp=
// Want to write
Fraction f(2, 3);
f = 5          // convert int/long 5 to fraction
               // followed by copy-assignment
double d = f;  // convert fraction to double
```

`f = 5` would:

- Look for assignment with int parameter
- Else look for convert constructor with int parameter