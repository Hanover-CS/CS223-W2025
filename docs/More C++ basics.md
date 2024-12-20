---
title: More C++ basics
tags: [CS223, data structures, notes, presentation]

---

# More C++ basics

---

- Where to look stuff up?
    - [cprogramming.com](https://www.cprogramming.com/)
    - [cplusplus.com](https://cplusplus.com/)
    - [cppreference.com](https://en.cppreference.com/w/)
- Lots of complicated answers out there
- Use the book and notes first

---

## Sources of confusion

- Lack of knowledge (new syntax/concept)
- Lack of information (what does this do?)
- Lack of processing power (too many things at once)

---

## How to remember things?

- Use flash cards

---

## Needless complications

- `cin >> first >> second`
- `a = b = c = 2`
- Just avoid these things!
- Don't do more than one thing at a time

---

## Odds and ends

- `\n` or `endl`?
- reassigning a variable's value

---

## Number conversion rules

- numbers converted to common type to operate 
- automatic conversion towards:
    - larger numbers (#bytes)
    - integers -> floating point
    - signed -> unsigned
- Avoid depending on these rules!
- Explicit conversion: `static_cast<type>(val)`
- C-style: `(double)5`

---

## Overflow

- When result of operation doesn't fit
- Usually can't detect when it happens
- Use a data type that is big enough to avoid it
- Know what numbers you'll be dealing with

---

## Named constants

- Naming things (variables)
- Improves code readability
- But we want to avoid people changing things accidentally
- `const` prevents the variable from changing in the future
- `const` variables must be initialized right away!
- Two kinds:
    - Key constants (use all caps)
    - Intermediate values/Explanatory variables (use normal naming)
- Example: Compute area of door with "tympanum".

---

```
double compute_area(double base, double height) {
    const double PI = 3.14159;
    const door_area = base * height;
    const tympanum_area = base * PI * PI / 2;
    return door_area + tympanum_area;
}
```

---

## Output formatting

- I/O Manipulators
- Instructions that help control output.
- Some number-specific, most work for strings
- Require `#include <iomanip>`
- Apply to the next entry
- `setw(...)` specify (minimum) width
- `setprecision(...)` number of significant digits
- `fixed + setprecision` specify digits after dot
- `showpoint` to always show dot (`.0`)
- `left/right`
