---
title: Unit testing
tags: [CS223, data structures, notes, presentation]

---

# Unit testing

---

## Unit tests

- A piece of code that is supposed to verify a very specific part of our program behavior
- Written to verify a function does what it is supposed to do
- Automated: No need for human inspection
- Documents expected behavior
- A failing test should be telling you exactly which part of your code is wrong

---

## Parts of a unit test

- **Arrange**, **Act**, **Assert**, (cleanup)
- Arrange: Set up the system in a state appropriate for the test
- Act: Perform the step that we want to test
- Assert: Verify that the resulting state is as it should be
    - May be the result/return value of the action
    - May be side-effects/changes to state

---

## What to test

- A typical case
- All possible edge cases
- Various inputs
- All branches in a conditional
- Example: insertNode in doubly-linked list

---

## Examples

- `max` function: Given 3 numbers, returns the largest
- `find` function: Searches for number in list