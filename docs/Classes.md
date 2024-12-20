---
title: Classes
tags: [CS223, data structures, notes, presentation]

---

# Classes

---

## Abstract Data Types

- Specify what operations can be performed
- Not how it is implemented
- Example: Temperature
    - Can be stored in C or F (or K)
    - `temp.inC()`, `temp.inF()`
    - `temp1 > temp2`
- Example: The tubes
    - `tube.is_full()`, `tube.is_empty()`
    - `tube.remove_ball()`
    - Main didn't need to know the details

---

ADTs come in 3 flavors:

- Language builtins: `double`, `char`, `bool`
- Standard library provided: `ostream`, `istream`
- Programmer code: classes, objects

---

## Classes, objects

- In C++, ADTs implemented via classes
- Classes are blueprints for objects
- Object: data bundled with the functions for the data
    - Others can interact only by calling functions, not directly accessing the data
    - This is called "encapsulation"

---

- Example: ordered list of numbers
- Data: `capacity`, `size`, `numbers` array
- Could have used different representation, different data (linked list)
- Functions: `get_size`, `insert_number`, `remove_number`, `print_numbers`
- All someone needs to interact with the list is these 4 functions
- Note: we have helper functions. Not part of the ADT

---

Key concepts:

- data bundled together (record)
- functions that act on the data also bundled in the same file (encapsulation)
- no other way to act on the data (hiding)

---

## Bundling data

```cpp
class Temp {
  double value;
  char unit;
}
Temp t1;
Temp t2;
t1.value = 23.0;
t1.unit = 'F';
t2.value = 5.0;
t2.unit = 'C';
```

---

## Object terminology:

- Member variables / fields
- Member functions / methods
- private / public
- accessors / mutators

---

```cpp
class Temp {
  private:
    double celc;
  public:
    double to_c() { return celc; }
    void set_c(double c) { celc = c; }  
    double to_f() {
      return celc * 9.0 / 5.0 + 32;
    }
    void set_f(double f) {
        celc = (f - 32) * 5.0 / 9.0;
    }
}
```

---

```cpp
Temp t1, t2;
t1.set_c(5.0);
t2.set_f(23.0);
cout << t1.to_c() << endl; // 5.0
cout << t1.to_f() << endl; // 41.0
```

---

## Practice

Person class: 

- first and last name
- methods to change first/last name
- method to get full name (`first last`)
- method to get "formal" name (`last, first`)

---

Time class:

- hours, minutes (24-hour-based)
- can set the hours or minutes
- to string military `17:24`
- to string normal `5:24pm`
- can add a number of hours
- can add a number of minutes

---

## Inline functions

- functions defined within `class { ... }` are "inlined".
- Their code is literally copied to each use place.
- Only do this for small functions!
- Define outside with:
    - `double Temp::to_C() { }`
    - need prototype within `class {...}`

---

## Constructors / Destructor

- special function. Named after the class
- does NOT return
- its job: initialize the member variables
- default constructor that does nothing
- destructor called when object "destroyed"
- default destructor that does nothing

---

```cpp
Temp::Temp(double value, char ch) {
    if (ch == 'c' || ch == 'C') {
        celc = value;
    } else { // ch is F
        setF(value);
    }
}
```

---

- `.h` "header", "interface" files
    - Contain declarations (functions, classes)
    - Meant to be included in other files
    - Need `ifndef` guard
    - What others should know
- `.cpp` "code", "implementation" files
    - Contain implementations
    - Compiled into "object" files
- `.o`, `.obj` "object" files
    - Compiled code, almost ready for CPU
    - Linked together to build executable

---

```cpp
class OrdNums {
private:
    int capacity;
    int size;
    int * numbers;
public:
    int size() const { return size; }
    int get(unsigned int i) const { 
        return numbers[i]; 
    }
    void add(int n);
    void remove(int n);
    OrdNums(int cap = 10);
    ~OrdNums();
}
```

---

```cpp
OrdNums::OrdNums(int cap) {
    capacity = cap;
    size = 0;
    numbers = new int[capacity];
}

OrdNums::~OrdNums() {
    delete [] numbers;
}
```

---

Workspace example

- working with different files
- using OrdNum in main
- passing it as parameter

---

Will this work?
```cpp
void getAndAddNumber(OrdNums ord) {
    int n;
    cin >> n;
    ord.add(n);
}
```

---

## Structs vs classes

- Everything public in structs
- Structs basically hold data together
- Useful when the data structure should be exposed