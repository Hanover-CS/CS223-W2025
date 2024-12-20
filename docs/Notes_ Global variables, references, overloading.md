---
title: 'Notes: Global variables, references, overloading'
tags: [CS223, data structures, notes]

---

# Global variables, references, overloading

---

## Odds and Ends: Random numbers

- `rand()` returns random integer
- `RAND_MAX`
- Not truly random numbers!
- Pseudo-random: Specific function that gives next number.
- `srand(...)` initialize 
- `rand_range(int a, int b)`
- break vs exit(..)

---

## Gotchas: Local variables

- Local variables have unknown initial values
- Whatever was in memory at the time
- Always initialize your variables!

```cpp
int main() {
    int x;
    if (x < 100) { ... }
    else { ... }
}
```

---

## Global variables

- Defined outside of functions
- Visible EVERYWHERE
- Their own spot in memory
- Initialized to "default values"
- Only use global constants
- Use longer names to prevent conflicts

---

## Default arguments

- `int f(int x, int y=2)`
- Can call as `f(2, 1)`, or `f(5)`
- Params with no defaults must precede those with defaults
- `int f(int x = 2, int y, int z = 5)`
    - What does `f(4, 6)` mean?
    - `f(2, 4, 6)` or `f(4, 6, 5)`?

---

## Function Overloading

- Functions can have same name, if they have different parameter lists
    - Different number, or different types
    - Cannot differ in just return value
    - Default arguments complicate things
- Depending on the call, compiler has to be able to figure out which function we mean.
- Example: functions two double an int, a double number, a string

---

- Example: `<<`
- Actually a function we can overload
- `operator<<(stream, int)`
- `operator<<(stream, double)`
- `operator<<(stream, string)`
- We will use this to print custom things later

---

## References

- It is an alias, another name for the same place in memory
- Used mostly in parameters but can also be local variables
- Ampersand after the type
- Changing one of the things changes the other
- Example: Implementing swap
- Example 2: See on C++ tutor

---

When to use references:

- We really need to change the original variable's value (in the caller)
    - `cin`
- Need to "return multiple things"
- We don't want to make a copy of the value
    - Relevant for more complex values
    - `cin` and streams

