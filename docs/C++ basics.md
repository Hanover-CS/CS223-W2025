---
title: C++ basics
tags: [CS223, data structures, notes]

---

# C++ basics

---

## How to stay on track

- Frequent small sessions, space out learning
- Talk to folks about stuff! (students, tutors, me)
- some things are hard!
- Try, fail, try again
- Put the time aside in your schedule

---

## Compilation

- Python is an interpreted language
- C++ is a compiled language
- Source code
- Preprocessor (`#include`)
- Compiler produces "object code"
- Linking results in an executable

---

## Differences with Python

- Indentation doesn't matter (but nice to have!)
- Newlines don't matter (but very nice to have!)
- Curly braces, semicolons
- variables must be declared first
- A variable has a "declared type"

---

## Integer data types

- (Possibly) different number of bytes
    - short int
    - int
    - long int
    - long long int
- Machine-specific
- Unsigned vs not, what is going on?
    - assigning signed to unsigned
- Binary representations
- `1 << 5`

---

## Floating point types

- float, double, long double
- Just use double

---

## Integer arithmetic

- Operation on integers always gives integers!
- Division gives only quotient
    - `7 / 3 = 2`
- Modulus gives remainder
    - `7 % 3 = 1`
    - Useful for "clock arithmetic"
    - `(6 + 8) % 12 = 2`