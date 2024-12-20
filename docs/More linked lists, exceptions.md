---
title: 'More linked lists, exceptions'
tags: [CS223, data structures, presentation]

---

# More linked lists, exceptions

---

## Exceptions

- Indicate "something unexpected" happened
- You can "throw" exceptions
- You can "catch" thrown exceptions
- Can be any value
    - But often is custom (small) classes
- Changes the flow of control
- NOT A RETURN!

---

### When to use

Use if:

- Must signal that something happened
    - this code doesn't know how to handle it
- A return value cannot be used as the signal
- Problem is too nested, those who know how to handle it are several spots back in the call stack

---

Example: list removeStart

Class activity: removeEnd

---

Example: insertAt

Class activity: removeAt

---

## Sentinel

- A "dummy" node that the list head always points to.
- Real data starts after it.
- This way `head` is never nullptr.
- Example code for addStart

---

## List with sentinel practice

- constructor, clear, destructor
- `removeAt(i)`
    - out of bounds exception
    - empty list exception
- copy constructor, assignment
- move constructor, move assignment

