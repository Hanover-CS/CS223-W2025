---
title: Lab 3
tags: [CS223, data structures, lab]

---

# Lab 3 Instructions

The main goals of this lab:

- Practice with a larger program with many functions
- Work with arrays, both 1-dimensional and 2-dimensional
- Implementing simple functions
- Using the random libraries
- Proper formatting of code

In order to get started, go into your codespace and from the terminal type:
```
git pull --rebase
```
If you encounter any error messages talk to me. Otherwise in the codespace you should now find, within the `labs` directory, another directory called `lab3`. You should use `cd` as appropriate to switch to that location. You will find a number of files, as we as a "tests" directory, but there are only 2 files you need to edit:

- `lab3functions.cpp`. This is where you will write the definitions of your functions.
- `lab3game.cpp`. At some point you will implement the main method here.


## Introduction to the game

We will implement a version of the [Crazy Scientist](https://boardgames-puzzles.com/products/crazy-scientist) game. The actual board game is as follows:

- We have a number of test "tubes", 3 in the standard version.
- In each tube we can place colored balls, that come in a variety of colors. In the standard setup there are 2 balls of each of 3 colors, for a total of 6 balls.
- Each tube has a fixed size, only fitting a given number of balls, 4 in the standard version.
- Balls can only go in and out of a tube at the top.
- The only move you can make is to "pour" the top ball from one tube into another.
- The goal of the game is to match a certain arrangement of balls on each tube. In our version of the game, the player would be starting with a random arrangement, and the goal is to bring it to standard arrangement where each tube has only balls from one color, and in a particular order.

## Implementation

### Overview

We will implement this using arrays. A tube will be represented by an array of characters, and each character indicates the color of the ball, with an underscore indicating an empty space. For example, the array `{ 'R', 'G', 'R', '_' }` will correspond to a tube whose bottom ball is red (`'R'`), the one above it is green, the one above that is red, and the top spot is empty (`'_'`). We will abbreviate this by writing `RGR_`.

We will also have a *tube set*, which is a set of tubes. We will represent this as an array of tubes, or basically a 2-dimensional array. For example for the default case of 3 tubes with 4 spots in them, we will use an array of type `char[3][4]`, and we imagine this as 3 rows of 4 elements each. We may write the standard arrangement of such an array, for example, as:
```
R___
GRB_
BG__
```
The "goal" configuration, needed to win the game, would be this:
```
BB__
GG__
PP__
```

We will implement a number of methods that will help us work with this setup. Here are brief descriptions of these methods (we will go into more details later):

- `write_tube`: Write the given tube out to an output stream (like `cout`) 
- `tube_empty`: Return whether the tube is empty or not (i.e. contains no balls)
- `tube_full`: Return whether the tube is full or not (i.e. already has as many balls as it can fit)
- `put_ball`: Place a given colored ball at the top of the given tube
- `remove_ball`: Remove the topmost ball from the given tube, and returns it
- `move_ball`: Move the topmost ball from one given tube to another
- `equal_tubes`: Test if two given tubes have the exact same contents (and in same order) 
- `write_set`: Write a whole set of tubes to an output stream 
- `equal_sets`: Test if two given sets of tubes are equal 
- `write_two_sets`: Writes two sets of tubes side by side to the output stream, indicating any tubes that match exactly
- `fill_empty`: Fills the values for the provided set of tubes so that they are all completely empty
- `fill_as_goal`: Fills the values for the provided set of tubes so that they match the goal configuration
- `fill_random`: Fills the values for the provided set of tubes in a random way (with the correct number of balls from each color
- `main game`: We will also write a "main game" function that uses these functions to actually produce a playable game.

You can play a functional version of the game by going into the `lab3` fodler and running `./workingGame`. Then you can type two numbers, e.g. `1 2`, to move a ball from one tube to another. The game ends when the set on the left matches the set on the right.

### Defined constants

There is a file `lab3defs.h` that contains a number of important constants to be used in the project. Whenever you need to use these numbers, use the name of the constant instead! For example, if we wanted to do a loop that goes through the spots in a tube, we would write:
```
for (int i = 0; i < TUBE_SIZE; i++) {
    ...
}
```
Using `TUBE_SIZE` instead of the value `4`. This allows us to easily change the dimensions of our game at will, by simply changing these constants. You should never use explicit constant values!

Take a look at that file now, and look for the following:

- The definition of the char constant `BLANK` to be the underscore character `'_'`. You should use `BLANK` instead of `'_'` if it comes up in your code.
- The definition of the array of color characters, named `COLORS`. For example `COLORS[0]` is the character `'B'`, `COLORS[1]` is the character `'G'` and so on.
- Then we have three constants that control the game "dimensions". `BALLS_PER_COLOR` indicates how many balls from each color we will have. `TUBE_SIZE` indicates how many balls each tube can fit (it is set to 4 currently), and `NUM_TUBES` is the number of tubes in a set of tubes (set to 3 currently). We could easily vary the game by simply changing one of these values. When you implement your functions keep this in mind!!!! Would your code stop working correctly if one of these constants changed? Then you have a problem.
- Following the constants we have two "type definitions". Recall from your reading that these type definitions simply allow us to give a different name to a type. In this case the first definition says `typedef char tube[TUBE_SIZE]`. This means that the type `tube` is the same as the type `char[TUBE_SIZE]`, or in our current case `char[4]`. So we can write something like this to create a tube: `tube t1` instead of `char t1[4]`.
- The next type definition is `typedef tube tube_set[NUM_TUBES]`. This one defines `tube_set` as the same as `tube[NUM_TUBES]`, or `tube[3]` for our particular values. This way when we want to refer to a set of tubes, we can say `tube_set` instead of `char[3][4]`.
- Lastly, we define two strings, called `MARK_CORRECT` and `MARK_WRONG`, which look a bit strange but basically produce a green checkmark (&#x2705;) or a red X (&#x274c;) respectively. You'll use them in one method where you want to show how two sets of tubes compare to each other.

### The functions

Now we can start implementing the methods, and this work will take place in `lab3functions.cpp`. Take a look at the file `lab3functions.h` where you will find the prototypes for these functions. The first thing to do is to create a stub for each function.

#### Stubs

Recall: A "stub" is the simplest function definition you can make that the compiler will accept. For that it needs:

- A definition line that matches the prototype, but includes names for the parameters.
- Return type and parameter types that match those in the prototype (including the user of `const` or references `&`)
- Curly braces instead of semicolons.
- For non-void methods, a return statement returning a value of the correct type, e.g. 0 for ints. The actual value doesn't matter, it is a placeholder for now, to get things to compile.

Go ahead and add function definitions for all the provided functions, using the brief descriptions provided in the `lab3functions.cpp` file as well as the prototypes provided in the `lab3functions.h` file (don't change that file). Make sure the const and & specifications match the prototype.

While doing this, you should think about each function, and whether the parameters and their types make sense. Make sure you do this! If it doesn't make sense, ask, don't skip it!

- Should this function need this parameter?
- What is a good name for it?
- Does the type for this parameter make sense?
- Does it make sense that this parameter is a reference with the & symbol? Does it make sense that it is not?
- Is `const` used correctly, for parameters that shouldn't change?

#### Implementations

After you have implemented the stubs, you should be able to get the following to compile, and show you many failing tests:
```
make runTests
```

One you have that part compiling and running, you can start working on the individual functions. In order to not see all the tests for functions you didn't even write, do the following two commands instead, changing the name of the function each time:
```
g++ -Wall -o run_tests ./tests/all_tests.cpp lab3functions.cpp
./run_tests --test-case="*write_tube*"
```
Remember that you need to run both command after you make changes. If you like to put the two commands in one line, you can do so by using ambersands between them:
```
g++ -Wall -o run_tests ./tests/all_tests.cpp lab3functions.cpp && ./run_tests --test-case="*write_tube*"
```
As you move from each function to the next, follow this process:

- Change the test-case name to match the new function
- Run the above test line and make sure there are failing tests
- Now implement the function to do what it is supposed to do.

There are comments above each function, detailing what needs to be done. Read those comments and ask questions if you have any.

Once you have implemented all the functions, run the tests one final time, to make sure they all pass.

#### Formatting

For this assignment, we will also use a "linter" which is a program that looks through your code and points out various kinds of errors. We will use just a small part of it for now, to achieve consistent formatting in terms of braces and indentation. To run the linter, do the following:
```
make formatFunctions
```
The output of this command will be pairs of red and green lines:

- The numbers above the lines indicate which range of rows we are looking at.
- The red is your current version, the green is what the formatter suggests. 

Some times if the two lines look the same it's because your line has an extra space at the end. Some times the system shows an "empty line", which usually means you have too many blank lines and it wants you to remove some.

Make changes and rerun the command until your whole file is fixed.

### The main game

Now that you have these functions working, it is time to set up the main game. There is a main function in the file `lab3game.cpp`, and you will need to implement it. To compile and run the game, do:
```
g++ -Wall -o game lab3game.cpp lab3functions.cpp
./game  # to run the game
```

Here what you need to do in your main:

- You need to use `srand` to initialize the random number generator, using the current time as an initial seed.
- You will need to create two variables of type tube_set to store the "current" tube set that the user is changing, and the "goal" tube set.
- Then you start your main loop. In the loop:
    - Use `write_two_sets` to show the current state
    - Check if the two sets match, and if they do show a congratulatory message and end the program
    - Prompt the user for their move, then read two integers for the tubes the user wants to move from and to
    - Check that the two integers correspond to tubes (they should be from 1 to NUM_TUBES). Show message and repeat the loop if not.
    - Check that the tubes are not-empty and not-full as appropriate for the move. Show message and repeat the loop if not. Pay attention to the indices!!!! If the user types 1, that corresponds to the first tube, at offset 0.
    - If everything is OK, carry out the move using `move_tube`. Pay attention to the indices!!!! 

You should now have a working game!

Before you finish up, check the formatting with:
```
make formatGame
```

If everything is OK, commit and sync.