---
title: Lab 2
tags: [CS223, data structures, lab]

---

# Lab 2

This lab is meant as practice in writing functions. You will write 5 mostly unrelated functions, and will have to decide their return values and parameter values. Two of the functions require a little bit of work but the rest are simple functions.

In order to get started, go into your codespace and from the terminal type:
```
git pull --rebase
```
If you encounter any error messages talk to me. Otherwise in the codespace you should now find, within the `labs` directory, another directory called `lab2`. You should use `cd` as appropriate to switch to that location. You will find a number of files, as we as a "tests" directory, but there are only 2 files you need to worry about:

- `lab2functions.cpp`. This is where you will write the definitions of your functions.
- `lab2main.cpp`. At some point you will add your function prototypes here.

For now you will be adding functions in the `lab2functions.cpp` file.

Remember to commit and sync after each function you implement!

## get_divisor

The first function you will write is called `get_divisor`. You cannot change the name, make sure you type it the same way! This function takes in an integer number and will also return an integer number as follows:

- If the given number is 1 or less, then the function should return -1.
- Otherwise it should return the smallest divisor of the number (after 1). For example given the number 20 it would return 2, given 15 it would return 3, given 11 it would return 11. And so on.

Start by writing a function "stub": This means you write just enough to be able to "compile": so you need the definition line correctly, then for the function body just put a return. It would not do the right thing, but it would be enough to get it to compile.

With the stub in place, execute the following from the terminal:
```
make testDivisor
```
If you see an error saying " No rule to make target 'testDivisor'" then you are not in the correct directory. If you see a long list of error messages then you probably didn't get the function details right. But if you see a message that talks about "test cases" and "assertions" then you are on the right track, and you can now worry about making the function do the right thing.

Look through the error message and you should be seeing things like:
```
testDivisor.cpp:8: ERROR: CHECK( get_divisor(20) == 2 ) is NOT correct!
  values: CHECK( 0 == 2 )
```
Read these parts carefully. For example this part says that it was expecting `get_divisor(20)` to equal 2, but instead it was 0. Once you implement the correct logic, you should be seeing all the tests light up green.

# promptName and greet

Then you are expected to implement two functions, called `promptName` and `greet`. The tests expect both functions to be there.

- `promptName` takes no arguments, and it will prompt the user for a name by printing the prompt `"Enter your name: "` then reading in a name and returning it.
- `greet` takes a name string, and simply prints `"Hello ..."` with the name in place of the dots. It doesn't return anything.

As before, start by writing stubs for these methods, in order to get things to compile. To run the tests for these methods you must run:
```
make testWelcome
```
This command will result in syntax errors until you get the function types right. You may need to also add some appropriate `#include` and `using ...` instructions.

Once the syntax errors are OK you will simply get the message about failing test cases. Now you can start working on implementing your function logic. Once again, when you get it working all the tests will light up green.

## get_middle

The next functions to write is called `get_middle`. It takes in three integers and has to return the one that is the "middle" in increasing order. For example if the three integers are 4, 3 and 5, it will return 4.

Once again, write a stub first. Then try to run the tests with:
```
make testMiddle
```
Once you get that command to show you failing tests, you can then proceed to implement this function's logic. Keep going until you get all tests to pass.

## rot13

Finally, the last function, which is a bit more complicated. It is called `rot13`, and it takes in one character and returns a character. (Note: characters, not strings)

What this function is supposed to do is "rotate the letters". What this means is that if you imagine all 26 lowercase letters in a circle from a to z, then `rot13` moves the letters over by 13. so `a` becomes `n`, `b` become `o`, and so on, and once we reach `z` we basically go back to the start. So for example `z` would return `m`. Here is how we will achieve this:

Characters can basically be treated as numbers, and you can look at Appendix A of the book for the exact correspondence and section 2.9 for a refresher. Looking at the appendix you can see for example that `'a'` corresponds to the (decimal) number 97. and the nice thing is that we can do arithmetic if needed. for example `'a' + 3` will be `'d'`.

So here is what we need to do in the function:

- First of all if the character is not lowercase (i.e. it is not between `'a'` and `'z'`) then we return the character as is, with no change. You can compare characters just as you can compare integers, so for example `'a' < 'd'` is true, but `'z' < 'd'` is false.
- Otherwise we compute the "offset" of the number, i.e. how far it is from `'a'`. You can simply subtract `'a'` from your character to find that. For example the offset of `'d'` should be 3.
- Now with the offset we want to add 13 to it. But if the result is over 25 you need to go back to the start, so we need to do `% 26`. For example starting with `15` we should end up with `15+13=28` which is `28-26=2`.
- Now with this new offset at hand, we want to compute what character it corresponds to, by simply adding `'a'` to it. So for example if the new offset was 2, then `'a' + 2` will become the character `'c'`.
- Simply return this final character you computed.

Phew, lots of steps. Let's trace an example:
```
Starting with 'g'
The offset if 'g' - 'a' = 103 - 97 = 8
Adding 13 is 8 + 13 = 21
Mod 26 is still 21
The corresponding character is 'a' + 21 = 97 + 21 = 118 which is 'v'
So the function would return 'v'
```

So to wrap up:

- If the character is not between `'a'` and `'z'` just return it.
- Otherwise transform it according to the above scheme and return it.

Once you get this right you should see all the tests passing. To run the tests, the command is:
```
make testRotate
```

## Prototypes

Now that you have all the methods running, it is time to work on the "main" file, which uses these functions. But for that to happen we need to first of all include the prototypes. So go to `lab2main.cpp` and at the designated place at the top add prototypes for all your functions. Now compile the application as follows:
```
g++ -o lab2 lab2main.cpp lab2functions.cpp
```
If all goes well you should see the file `lab2` created. Here's how to test it, try the following commands:
```bash
echo 237 | ./lab2 factor          # 237 = 3 79
echo 231 | ./lab2 factor          # 231 = 3 7 11
./lab2 welcome                    # enter your name, get greeted
echo "23 12 34" | ./lab2 middle   # Middle of 23 12 34 = 23
echo "Gray fox!" | ./lab2 rotate  # Genl sbk!
echo "Genl sbk!" | ./lab2 rotate  # Gray fox!
./lab2 rotate < text1.txt > text2.txt # See encrypted version in text2.txt
./lab2 rotate < text2.txt          # Get back original message
```

Make sure you do a final commit and sync!
