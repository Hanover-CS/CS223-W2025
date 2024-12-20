---
title: Templates
tags: [CS223, data structures, notes]

---

# Templates

---

## Template functions

- They are a "blueprint for a function"
- Called "generics" in other languages
- Use one or more "type parameter"
- `template <typename T> ...`
- When a function called, it will be matched against the templates
- Can force an instantiation: `f<string>(...)`
- Note: Compiler won't check that the operations are valid

---

- Example: getValueOrDefault
- Example: swap
- Example: print
- Example: array_copy

---

Practice:

- Function to find largest value in an array of given length. (assumes `>`)
- Function to print the entries in an array of given length.

---

Gotchas:

- Implement a silly ++
- Try to print something without a << method
- Try to print something with multiple conversions

---

## Class templates

- When the whole class depends on a type parameter
- Example: container classes like lists and arrays
- Individual member function implementations need to be templates

---

Example: Maybe class

```cpp!
template <typename T>
class Maybe {
  private:
    T* val;  
  public:
    T getVal() {
        if (val != nullptr) return *val;
        else throw EmptyException();
    }
    Maybe() { val = nullptr; }
    Maybe(T & value) { val = new T(value); }
    void setVal(const T & value) { val = new T(value); }
    bool isEmpty() const { return val == nullptr; }
    operator T() const { return getVal(); }
};

```

---

```cpp
template <typename T>
class List {
    class Node {
      public:
        T value;
        Node* next;
    }
    
  public:
    T getAt(int i);
    void insertStart(T v); 
    ...
}

template <typename T>
T List<T>::getAt(int i) {
    ...
}
```

---

Practice: Implement List class methods

- `insertStart`, `getAt`