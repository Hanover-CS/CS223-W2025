---
title: Functions
tags: [CS223, data structures, presentation]

---

# Functions

---

## (From earlier stuff)

- Is `0 < x <= 10` valid C++? If so, what would its value be for numbers `x` between 1 and 10?
- types of statements
    - empty statement `;`
    - assignment `x=...;`
    - declaration `int x;`
    - compound `{ ...; ...; }`
- `if (<test>) <statement>`
- `if (<test>) ;` <-- empty statement
- `pow` vs normal multiplication

---

## Functions

- Independent pieces of code
- Can be "called" from other parts of our code
- Can "return" a result
- Why:
    - Break code up into smaller units
    - Avoid repeating ourselves
    - Can test them

---

Print program prompt:
```c++
void print_prompt() {
    cout << "Greetings!" << endl;
    cout << "How are we doing today?" << endl;
    cout << "What would you like to do next?" << endl;
}
```

---

Print a double with fixed # of digits after decimal point
```c++
void print_double(double n, int digits) {
    cout << fixed << setprecision(digits) << n;
}
```

---

## Program flow

On a function call:
 
- Execution goes from the call place to the function's code
- On a return, execution goes back to the call place
- We continue right where the call happened

---

## How small should functions be?

- A function should have a clear purpose.
- If there is some piece of code that does a clear task, it's a candidate for a function.
- It should "do one thing".
- Ideal: 4-5 lines.

---

## Three different syntax constructs

- Function declaration/prototype
- Function definition (code)
- Function calls

---

## Function definition

- Return type
- Name
- Parentheses
    - Parameters (type + name)
- Body (curly braces)
    - Return
- `int subtract(int x, int y) { return x - y; }`

---

# Function prototype

- Like definition except:
- No body (semicolon instead)
- Parameter names optional
- `int subtract(int, int);`
- The `#include` usually include prototypes

---

# Function call

- Name
- Parentheses
    - Arguments: A value for each parameter
- Must be preceded by definition or prototype
- `x = subtract(4, 5)`

---

```
int subtract(int, int);        // Prototype
int subtract(int x, int y) {   // Definition
    return x - y; 
}
x = subtract(4, 5)             // Call
y = subtract(10, 5)            // Another call
```

---

Write prototype, definition and call:

1. Function that returns the largest of two integers
2. Function that prints the largest of two integers
3. Function that given two integers returns whether the first is larger than the second

---

- `cout` vs `return` vs `endl`
- `cout` used to print something to the screen
- `endl` used to end the line when printing on screen
- `return` simply gives a value back to the caller. The caller decides what to do with it.

---

Real life example: Code check person

- prototype: 
    - `Coat giveCoatBack(int ticket_num)`
- Given a ticket number.
- Goes and finds actual coat in storage.
- Records transaction in logbook (`cout`)
- Gives the coat to customer (`return`)

---

## Returning

- Void function: 
    - Can use `return;` to end it
    - Implicitly returns at its end
- Non-void function:
    - Must use `return <something>`
    - In every path out of the function
- Special case: main
    - `int main() { ... }`
    - If no return, implicit `return 0;`
    - If a return, must return an integer (indicates error)

---

## When to return

Different approaches:

- Return as soon as you know the result
- Keep track of answer, only return at end

---

Examples:

- `isEven(int num)`
- `isFirstLarger(int a, int b)`
- `isPrime(int num)`

---

## Why do we need prototypes?

- Compiler must create code that "hands off" to the function's code
- Needs to know how to place the arguments (values for the parameters)
- Also needs to know that the call is "valid"

---

## Shell practice

- Pipe: output of one command as input to another
- Redirects: Link cin/cout to files
- Program that squares numbers. 
    - Use `cat` to read file
    - Then read from file, write to file
- Program that writes sequence `a+bn`