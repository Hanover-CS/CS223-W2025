---
title: 'Lab: Dynamic arrays'
tags: [CS223, data structures, lab]

---

# Lab 4 Instructions

The main goals of this lab:

- Practice with a larger program with many functions sharing data
- Work with allocating and deallocating memory
- Writing function prototypes and definitions from a problem description
- Learning about invariants, pre-conditions and post-conditions
- Basics of using `.h` files
- Using Valgrind to look for memory leaks
- Running the build commands manually
- Proper formatting of code

## Dynamically resizable arrays

In this lab you will implement something that appears relatively simple: You have to maintain an array of numbers, in increasing order. The problem: You don't know how many numbers the array will have to hold. So how do we create the array, what number do we use when we say `arr[N]`? If `N` is too small, then you'll run out of room if the user tries to insert too many numbers, and if `N` is too large then you are just wasting space on the computer. The solution is basically this:

- We start with some small initial capacity, say 5.
- When this array gets full, we create a new array, of twice the capacity, and then copy over the values from the first array. Now we have 5 more spots available.
- The user will also be able to remove numbers from the array. If they remove so many numbers that the array can fit 4 times as many, then we will cut the array size in half, again by creating a new array and copying over values.
- These arrays will be allocated in the heap, using a call to `new`. When we create a new array and after we copy values over, we need to make sure to use `delete` on the old array, in order to free the memory that array was occupying, as we no longer need it.

To make things more interesting, we will keep the numbers in our arrays in increasing order, e.g. `[2, 4, 5, 7]`. If the user tried to insert the number `3`, the result would be `[2, 3, 4, 5, 7]`, which means that all the numbers from 4 and on had to move one entry to the right. Similarly if we then remove 4, then the numbers after 4 have to move one entry to the left to fill in the space, and result in `[2, 3, 5, 7]`.

### Variables

In order to manage our state, we will use three global variables defined at the top of `lab4functions.cpp`:

- `int * numbers`: A pointer to the allocated array. Start out as `nullptr`.
- `int capacity`: The current length of the array we have allocated.
- `int size`: How many numbers are currently stored in the array.

#### Invariants

An important concept to keep in mind is the concept of **invariants**: These are statements that are meant to be true throughout the program. So your functions can assume these statements are true when they start their work, and they must ensure these statements remain true whenever they return. Here are the invariants:

- `numbers` is `nullptr` until the `initialize` method is called.
- After `initialize` has been called and for the rest of the program, `numbers` points to an array allocated in the heap with length equal to the `capacity`
- The entries in the `numbers` array from index `0` to index `size - 1` contain the numbers that our application is responsible for. And they are always in increasing order.
- `0 <= size <= capacity`.

### Function Stubs

We will start by implementing stubs for all the functions. Remember that stubs don't need to have any "real work", they must simply be enough code to define a function with the appropriate parameters and return type, to make the compiler happy. Here are the function stubs you should add, in `lab4functions.cpp`. These functions don't return anything unless otherwise stated.

- `get_size` takes no parameters and returns an integer.
- `get_capacity` takes no parameters and returns an integer.
- `copy_arrays` takes two int pointers (that represent arrays), and an integer for how many elements to copy.
- `initialize` takes a single int parameter, for the capacity.
- `readjust_capacity` takes a single int parameter for the new capacity value.
- `enlarge_numbers_array` takes no parameters.
- `shrink_numbers_array` takes no parameters.
- `insert_number` takes a single integer parameter for the number to insert. It returns whether insertion was successful or not (we will have a rule that you can't add the same number twice).
- `remove_number` takes a single integer parameter for the number to remove. It reutrns whether the removal was successful (i.e. the number was indeed there, and we removed it).
- `print_numbers` takes a `ostream` reference parameter, and returns that same string reference.

#### Compiling

In order to check that this works, we must compile the test file, which is set up to import our `lab4functions.cpp` file. The test file is in `tests/tests_setup.cpp`. Compile it with all warnings enabled by doing:
```bash
g++ -Wall -o runTests tests/tests_setup.cpp
```
If your function definitions match what the test expects, the code should compile (but no tests run yet). If you get compile errors, you need to look at them and understand what they are saying. When you get this to compile, you can then proceed to the rest. A `runTests` file should have been created, and you can run it with:
```bash
./runTests
```
You will likely be getting some serious errors, including a scary fatal error "segmentation violation signal". This is usually an indication that you have tried to use a pointer that no longer points to valid memory.

### Function implementations

Now let's start implementing our functions. Start by implementing `get_size` and `get_capacity`. They can simply return the values in the variables `size` and `capacity` respectively.

#### initialize

Next you need to implement the `initialize` function. This function takes as input a value for the capacity, and it must set the values for the variables for `capacity` and `size`, and must also allocate from the heap an array with length equal to the capacity, and store it in `numbers`. This function is meant to be called only once, at the beginning of the program.

To test the function, compile the tests as above then run:
```bash
./runTests --test-case="*initialize*"
```

#### copy_arrays

The next function is a helper function called `copy_arrays`. It takes as input two pointers to arrays, `from` and `to`, where `from` is a pointer to const, as well as an integer number of items `n`. The pre-conditions (things you can assume to be true) of this function are:

- `n` is non-negative
- `from` and `to` are non-null pointers to allocated arrays
- `from` points to an array with at least `n` elements
- `to` points to an array that can fit at least `n` elements

The post-conditions (what should be true after the function is run) are:

- The elements of `from` are unchanged (we can enforce this by making sure `from` is a "pointer to const").
- is that the first `n` elements in the array pointed by `to` contain the same entries as the first `n` elements in the array pointed by `from`.
- Elements in `to` after the first `n` remain unaffected.

In simple terms, `copy_arrays` has to copy the first elements from the array `from` over to the array `to`. This should be a simple for loop to implement. You can test this method on its own by using
```bash
./runTests --test-case="*copy_arrays*"
```

#### readjust_capacity

Next is a key function, whose job it is to adjust things when the array is too full or too empty. It does not itself determine if things should be adjusted, that's someone else's job and then they call this method. It simply carries out the adjustment.

Pre-conditions:

- The parameter `newCapacity` contains the desired new capacity value.
- The `size` (number of numbers we have) is less than newCapacity.
- The heap-allocated `numbers` array contains in its first `size` elements the numbers we keep track of.

Post-conditions:

- A new array has been heap-allocated and stored in `numbers`, with capacity equal to `newCapacity`.
- The old `numbers` array has been de-allocated.
- The value of `capacity` is equal to `newCapacity`.
- The first `size` entries in the (new) `numbers` array contain the same values that were in the previous array.

Essentially what this function needs to do is adjust the `capacity` value, allocate a new array of the correct length, copy over the appropriate values using the helper you created earlier, and de-allocate the old array that is no longer needed. The order of operations is important.

Run your tests with compiling and then:
```bash
./runTests --test-case="*readjust_capacity*"
```

#### Checking for leaks: Valgrind

Before we move on to the next method, we must also ensure the code we just wrote doesn't cause any memory leaks. Recall that a memory leak is an area of memory that the system gave to you, but for which you no longer have a pointer, and can no longer access. So this area basically will go completely unused. These memory leaks are subtle errors that our simple compiler and testing won't catch. For this we need some separate tools, and the most common of these is called Valgrind. Valgrind will basically run your program under a monitoring layer, where it can see what your program does with memory and then report back what it notices.

To use Valgrind, we need a small "main" that basically runs the program in some way that would help us test things. I have created such a function in the file `tests/check_leaks.cpp`, take a look at it and in particular lines 6-8 inside the conditional. We will run these functions, and all they do is repeatedly call `readjust_capacity` before exiting. To run this with Valgrind, we'll need to compile it first, and it's good to compile with some extra settings:
```bash
g++ -Wall -O0 -g -o checkLeaks tests/check_leaks.cpp
```
The `-O0` part tells the compiler to not try any "optimizations". The `-g` tells it to generate debugging information. Combined, these have the effect that programs like Valgrind can give us more useful info when something goes wrong.

Then we run valgrind:
```bash
valgrind --leak-check=full ./checkLeaks simple
```
You will see a report. If things are OK then the report will go straight into a heap summary section, followed by a leak summary section, with hopefully nothing marked as "lost". If there are problems, then you will find a bunch of messages before these sections, that typically contain some code locations as well as a key phrase that tells you what to look for, such as:

- `24 bytes in 1 blocks are definitely lost in loss record 1 of 3` or something along these lines. "definitely lost" is an indication that you lost track of a pointer before calling `delete` or `delete []`. Note that the message for this will often point you to where that memory was allocated, and not where you forgot to delete it, as the system can't possibly know when you intended to delete it.
- `Mismatched free() / delete / delete []`. This is a sign that you called the wrong `delete` method. Recall that pointers to allocated arrays need to be deallocated using `delete []`, not plain `delete`.

#### `enlarge_numbers_array` and `shrink_numbers_array`

The next two methods simply call on `readjust_capacity`. The first is supposed to simply double the current capacity, while the second is supposed to halve it, but only if the resulting capacity will not be below 5. You can test these methods just as we did for earlier methods.

#### `insert_number`

Now we come to one of the two main methods of the system. This method takes in a new number, and it is supposed to insert it into the array at the appropriate spot, maintaining the increasing order of elements. Review the "Invariants" section to remember some key statements you can assume to be true, and must maintain.

- Pre-condition: The system is in the state described by the invariants.
- Post-condition: The system has maintained the state described by the invariants
- Post-condition: If the number did not already exist in the array, it has been inserted in a spot that maintains the increasing order, and the array size has increased by 1.
- Post-condition: If the number was already in the array, nothing happens.
- Post-condition: The return value is true if an element was added, and false otherwise

Your function implementation will need to traverse the array in search of the place the element should be inserted at. Then it must shift numbers to the right to create space (enlarging the array if needed) and insert the number, adjusting the size. If the number was there, then it simply returns without changing anything.

#### `remove_number`

The last big method is `remove_number`, which is given a number and will remove it from the array if it is present. Upon removal, it should shift all the following elements to the left by 1 to fill in the empty gap. If the number is not present then it should simply return. The function must not go through more of the array than is needed to determine if the number is there or not! Think of what that means, you must stop your loop early if you can.

Lastly, if the size of the array is less than a quarter of the capacity, then our method should also call the previously created method to halve the array capacity.


For practice, write down what the pre- and post-conditions would be for this function. Then code and test the function.

#### `print_numbers`

Our last function before looking at main is `print_numbers`. It takes as input an `ostream` reference, and must return that reference, and also write the numbers to the stream like so: `2 5 45 67`. There must be exactly one space between the numbers, and no spaces on either end.

Run all your tests to make sure they all pass.

Also build and run the check-leaks program again, this time omitting the word `simple` at the end of the command. This will skip the conditional in the code, and instead perform a large series of insertions and removals, trying to "stress-test" the system so to speak. Make sure no leaks are reported.

#### Formatting

To fix formatting errors, let's run the formatter:
```bash
make formatFunctions
```
Fix any reported errors. If it looks like it's telling you the same thing as what you have typed, look for extra spaces at the end of lines.

### Main

Now we will work on the main function, whose goal it would be to allow the user to interact with this program we wrote. It will need to do the following:

- initialize the array
- report out the size and capacity
- print out all the numbers
- insert or remove a number

#### The .h files

Go into `lab4functions.h` and add in it prototypes for just the functions needed to perform the above steps. This will leave out some functions and that's a good thing: We don't need our `lab4main.cpp` to know more than it needs to.

While here let's learn a bit about the syntax of `.h` files. These files are used primarily to share function prototypes and other constants that many files need to know about. So one of their key features is that they are often included in multiple places. This is no different than what happens when you include files like `iostream`.

But we need to make sure such files are not accidentally included twice. We do this by using what is known as "preprocessor macros":
```cpp
#ifndef LAB4_FUNCTIONS_H
#define LAB4_FUNCTIONS_H
...
...
#endif
```
These are read by the preprocessor, the part that is responsible for including the contents of `iostream` and other files like that. And they are a mini programming language of their own. The first line is a conditional, that checks "if something called `LAB4_FUNCTIONS_H` is *not* defined, `if-n-def`". If not, then it will read in and include the lines that follow, up to the `endif`. And these lines contain our various definitions, and also contain the magical line `#define LAB4_FUNCTIONS_H`, which "defines" this variable. You could even give it a value and treat it as a constant, and other cool stuff that we will not get into. But the bottom line is that if we somehow include this file twice, the contents of that ifndef block will only be included once, because the second time through that variable is going to be defined. So it's a way to ensure things thing is only included once.

#### Back to main

Once you have added your prototypes, make sure your tests are still passing and you don't get compile errors there. Then start working on a main function in `lab4main.cpp`. You have some leeway in how it is implemented, but you should make sure it does the following:

- Has a loop that reports to the user how many numbers are stored and what the array capacity is, and then prints all the numbers.
- Then expects the user to type in a number. If the number is positive then it adds it to the array, but prints an error message if the number was already there (which you get to find out about because of the `insert_numbers` return value).
- If the number is negative, e.g. `-5`, then you should interpret that as a request to remove the number `5` from the list, and similarly print an error if `5` was not in the list to begin with.
- You are free to handle the number `0` however you like. Maybe you can use it to exit the program.

To test the function, you must compile both files:
```bash
g++ -Wall -g -o main lab4main.cpp lab4functions.cpp
./main
```
And you can clean up the code by running the formatter:
```bash
make formatMain
```

Commit and push your files. Note that you have some extra files you created. The ones you should commit and push are the two `.cpp` and the one `.h` file, and you should delete the executable files you created like `checkLeaks`, `main`, `runTests`.
