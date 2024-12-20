---
title: Pointers and classes
tags: [CS223, data structures, presentation]

---

# Pointers and classes

---

## Pointers to objects

- Can create objects in heap `new OrdNum(5)`
- Can store those in pointer:
    - `OrdNum * op = new OrdNum(5)`
- Dot is stronger than pointer, need parentheses:
    - `(*op).capacity`, `(*op).add(7)`
    - `*op.add(7)` won't work: `op` has no `add`
- Shorthand notation:
    - `op->capacity`, `op->add(7)`

---

## this pointer

- Present whenever member functions called
- Points to the current object
    - `(*this).capacity`, `this->capacity`
- C++ often lets us omit it: `capacity`

---

```cpp=
// Not proper add: no checks, no order
void OrdNum::add(int n) {
    this->numbers[size] = n; 
    (*this).size += 1;
}
OrdNum ord(5);
ord.add(4);
ord.add(3);
OrdNum ord2(ord);
ord = ord2;
```