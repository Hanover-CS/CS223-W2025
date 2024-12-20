---
title: 'Conditionals, Loops'
tags: [CS223, data structures, notes, presentation]

---

# Conditionals, Loops

---

## Booleans

- `true`, `false`
- Comparison operations: `<`, `<=`, `==`
- Logical operations: `&&`, `||`, `!`
- Not equal: `!=`
- Printed as 1 and 0
- Careful: `!x > 2`

---

## Cautions

- Can technically use a normal number where boolean expected
- Non-zero numbers are true
- Other kinds of values have similar "convert to boolean" rules
- AVOID THEM ALL!!! That way madness lies.

---

## Low level program flow

- Low level program execution:
    - Commands in memory location
    - "Program Counter" points to currently executing location.
    - PC increases by 1 each time.
    - A command can instruct to "jump"
- Problem when reasoning about "program flow"

---

```
L1: x = 10
L2: if x < 5 go to L7
L3: x = x - 2
L4: if x >= 4 go to L3
L5: x = x + 1
L6: go to L2
L7: print x
```

---

## Structured programming

- Restrict to 3 types of program flow:
    - Sequence: statements executing in order
    - Selection: Choose amongst alternatives
    - Iteration: Repeat a group of statements 
- Following these rules makes it easier to reason about program logic
- This is why we have `if`s and `while`s

---

## Conditionals

```cpp
if (<test>) {
    <statement1>
    <statement2>
    ...
}
```
```cpp
if (<test>) {
    ...
} else {
    ...
}
```
```cpp
if (<test1>) {
    ...
} else if (<test2>) {
    ...
} else {
    ...
}
```

---

Write tests for:

1. `x` is between 10 and 100
2. `color` is `"purple"` or `"blue"`
3. `color` is `"purple"` but not `"blue"`
4. `x` is less than 10 and `color` is `"purple"`, or `x` is more than 5 and `color` is `"blue"`
5. `x` is less than 10, or `x` is more than 5 and an even number

---

```
switch (<value>) {
    case 5: ....
            break;
    case 4: ....
            break;
    ......
    default: ....
}
```

---

## Choosing a conditional



| What                                                    |   Conditional    |
|:------------------------------------------------------- |:----------------:|
| Do something in some cases, nothing in others           |       `if`       |
| Do something in some cases, otherwise do something else |    `if-else`     |
| Multiple possible paths                                 | `if-elseif-else` |
| Different options for a variable                                                        | `switch`                 |

---

### Examples

1. Print a message when a number is positive
2. Print "woof" if my pet is a dog, "meow" if it is a cat, or "hm" otherwise.
3. Take different actions depending on whether the user chose "withdraw", "deposit", "transfer" or "exit".

---

## Ternary / Conditional operator

```
// Syntax:  <test> ? <valueIfTrue> : <valueIfFalse>
int y = x > 5 ? 2 : 3;
// Equivalent:
int y;
if (x > 5) {
    y = 2;
} else {
    y = 3;
}
```

---

## Repetition

```
while (<test>) {
    <statement1>
    <statement2>
    ...
}
```
```
for (<initialize>; <test>; <increment>) {
    <statement1>
    <statement2>
    ...
}
```

---

| Type  |            When to use            |
|:----- |:---------------------------------:|
| for   | Clear start/end/update conditions |
| while |             Otherwise             |

---

### Examples

1. For every odd number from 0 to 25, print the square of the number
2. As long as the user types in numbers, keep reading them
3. Keep reading numbers from the user until the sum is more than 100
4. Read in 10 numbers from the user
5. Read in 10 numbers from the user but stop early if the user types a negative number
6. Program that prints the calories burned after 10, 20, 30, 40 and 50 minutes respectively.

---

Fun fact: 

Every `while` loop can be written as a `for` loop, and vice versa

---

For every odd number from 0 to 25, print the square of the number

1. Write as while loop and as for loop
2. Print in single line instead, separated by comma. But no trailing comma!
