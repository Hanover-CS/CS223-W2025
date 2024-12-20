---
title: Arrays
tags: [CS223, data structures, notes, presentation]

---

# Arrays

---

## From last time

- A function that keeps values in order:
- `inOrder(a, b)`

---

## Basics of arrays

- Contiguous chunk memory
- Elements of the same type
- Fixed number of elements
- Declaration: `int hours[5]`
- Access element using offset: 
    - `hours[0]`, `hours[1]`
    - `hours[2] = 3`
- Initialize: `int hours[3] = { 3, 2, 4 }`
- size in memory = size of type * num elements

---

Example:

Write the squares of the first 10 numbers in array

---

Try:

Ask user how many numbers to store, then make an array and write the squares of that many numbers

---

## Differences with Python lists

- Must hold the same type of element
- Can hold a fixed max number of elements
    - And known ahead of time
- No protection against accessing out of range
- Cannot "insert", "remove"
- We will see how to do "lists" in C++ later

---

## Traversing arrays

- `for (int i = 0; i < n; i++) { ... }`
    - Can choose where to start/end
- `for (double el : hours) { ... }`
    - Will go through all
- Can use references

---

## Array patterns: Accumulation

```cpp
// arr: array of size n
int sum = 0;
for (int i = 0; i < n; i++) {
    sum += arr[i];
}
```

---

## Array patterns: Filtering

```cpp
// arr: array of size n
for (int i = 0; i < n; i++) {
    if (arr[i] % 2 == 1) {
        ...
    }
}
```

---

## Array patterns: Searching

```cpp
// arr: array of size n
for (int i = 0; i < n; i++) {
    if (arr[i] % 2 == 1) {
        return true;
    }
}
return false;
```

---

## Array patterns: Looping from end

```cpp
// arr: array of size n
for (int i = n - 1; i >= 0; i--) {
    ...
}
return false;
```

---

Example:

- Read up to 5 positive numbers from the user
- Stop if user types negative number
- Print the numbers, and their sum

---

## Arrays in parameters

- When passing array to function, it is NOT a copy
- Changing the array entries will change original values
- If will not change the array, set `const`

---

## Different specifiers in parameters

```
f(int x)        // can do x = ... But local
f(const int x)  // cannot do x = ...
f(int & x)      // can set x = ... and reference
f(const stream & out)
```

---

## 2-dimensional arrays

- Two indices: row, column
- Declared: `int a[4][5]`
- Access: `a[3][1]`
- in terms of memory allocation, same as an `int a[4 * 5]`
- Typical iteration: Nested for loop

---

```cpp
// arr: n x m 
for (int row = 0; row < n; row++) {
    for (int col = 0; col < m; col++) {
        arr[row][col]
    }
}
```

---

Example: Crazy scientist game

- 3 tubes. Each tube can fit 4 balls.
- We can represent a ball by a character.
- The whole set can be `char[3][4]`

```
RB__
GGR_
B___
```

---

## Typedefs

- Typedef: Another name (alias) for a type
- Allows us to express ourselves
- E.g.: `typedef char color;`
- `color c = 'G';`
- `typedef int points;`
- `points p = 5;`

---

- Tube is array of 4 chars:
- `typedef char tube[4];`
- `typedef tube tube_set[3];`
- Same as `typedef char tube_set[3][4]`
- `f(tube t)` instead of `f(char t[4])`