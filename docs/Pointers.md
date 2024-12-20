---
title: Pointers
tags: [CS223, data structures, presentation, notes]

---

# Pointers

---

## Memory addresses

- Every variable/parameter has a memory address where it is stored
- Can use `&x` for the address of `x`
- Example in codespace 
- Problem: How can a function create and return an array?

---

## Pointers

- New data type
- Stores memory addresses
- set to `nullptr` when you don't have an address yet
- Declare as `int *p`, or `int* p`, or `int * p`

---

## Dereference operator

- Can be used for addresses. Returns the value there.
- Denoted: `*p`
- It's basically the converse of the address operator `&`

---

```cpp
int x = 5;
int y = 10;
int * p = &x;  // p points to x location
int * q = &y;  // q points to y location
cout << "p: " << p << endl;
cout << "&p: " << &p << endl;
cout << "*p" << *p << endl;
*p = *q;  // Set value where p points
p = q;    // Change where p points
```

---

## Pointers vs references

```cpp
int x = 5;
int * p = &x;
int & y = x;
y = 10;   // changes x
*p = 20;  // changes x
p = &z;   // p no longer points to x
p = null_ptr; // p points to nothing
```

---

## Pointer arithmetic

- Can add/subtract ints from pointer
- Moves to corresponding address
- Address goes up by the size of the values
- Codespace example

---

```cpp
int arr[5] = { 23, -2, 4, 5 };
int * p1 = arr;             // 4 bytes each
short * p2 = (short *) p1;  // 2 bytes each
cout << *(p2);       // first half of 23
cout << *(p2 + 1);   // second half of 23
cout << *(p2 + 2);   // first half of -2
cout << *(p2 + 3);   // second half of -2
```
