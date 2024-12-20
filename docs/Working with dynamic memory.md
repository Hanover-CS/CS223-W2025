---
title: Working with dynamic memory
tags: [CS223, data structures, presentation]

---

# Working with dynamic memory

---

- Pointers as parameters
- Pointers to constants vs const pointers
- Heap, new, delete
- Problems
- Returning pointers

---

## Avoid confusion

```cpp
x = 1 + * p;  // dereference
x = 2 * p;    // multiplication
*p = 5;       // dereference
p *= 5;       // multiplication
*p *= 5;      // woah!
*p *= *q;     // dude!
```

---

## Pointers as parameters

- Can pass pointers like any other value
- They are "passed by value": The address stored in the pointer is copied to the parameter location.
- Compare swap methods

---

## Pointers and constants

- `int * const p`: Cannot reassign `p`
- `const int * p`: Cannot change `*p`
- `const int * const p` (rare)

---

Which operations are valid?
```cpp
int x = 10;
const int y = 5;
int * const p = &x;
const int * q = &y;
x *= *q;
p = q;
p = &x;
*p *= y;
*p *= *p;
*q = *p;
```

---

## Heap, new, delete

- Heap: Space in memory that you can access on request.
- `int *p = new int`: pointer to a heap spot fitting 1 int.
- `int *p = new int[100]`: fits 100 ints
- You are now responsible for this chunk of memory!
- `delete`, `delete[]` when no longer needed.

---

Draw memory picture
```cpp
int *q = new int;
int *p = new int[4];
p[0] = 5; p[1] = 6;
*q = 10;
q[0] = 8;
// Consider each of the following separately
delete q;
delete p;
delete[] p;
p = nullptr;
p = new int[5];
```

---

## Dynamic allocation problems

- Dangling pointer
    - Points to memory you already deleted
    - Someone else might have written stuff there now
- Memory leak
    - You didn't delete
    - But the pointer is gone
    - System thinks you still use the space

