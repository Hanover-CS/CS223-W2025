---
title: 'Class example: NumbersArray'
tags: [CS223, data structures, handout]

---

# Class example: OrdNum

```cpp=
class OrdNum {
private:
    unsigned int capacity;
    unsigned int size;
    int * numbers;
public:
    OrdNum(int cap = 10);
    OrdNum(const OrdNum & other);             // copy constructor
    OrdNum(OrdNum && other);                  // move constructor
    OrdNum & operator=(const OrdNum & other); // copy assignment
    OrdNum & operator=(OrdNum && other);      // move assignment
    unsigned int getSize() const;
    void add(int n);
};

// Normal constructor
OrdNum::OrdNum(int cap) {
    size = 0;
    capacity = cap;
    numbers = new int[capacity];
}
// Copy constructor
OrdNum::OrdNum(const OrdNum & other) {
    capacity = other.capacity;
    size = other.size;
    numbers = new int[capacity];
    copy_values(other.numbers, numbers, size);
}
// Move constructor
OrdNum::OrdNum(OrdNum && other) {
    capacity = other.capacity;
    size = other.size;
    numbers = other.numbers;    // steal their numbers!
    other.numbers = nullptr;    // make sure they can't delete the numbers
}

// copy assignment
OrdNum & OrdNum::operator=(const OrdNum & other) {
    if (this == &other) return *this;
    delete [] numbers;
    capacity = other.capacity;
    size = other.size;
    numbers = new int[capacity];
    copy_values(other.numbers, numbers, size);
    return *this;
}

// move assignment
OrdNum & OrdNum::operator=(OrdNum && other) {
    if (this == &other) return *this;
    capacity = other.capacity;
    size = other.size;
    int * temp = numbers;
    numbers = other.numbers;   // steal their numbers!
    other.numbers = temp;      // let them clean up our numbers
    return *this;    
}
```

