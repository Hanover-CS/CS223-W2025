---
title: Lab 1
tags: [CS223, data structures, lab]

---

# Lab 1

In this lab we will build a small interactive session, that manages a user's bank account balance. Here are the tools we will utilize:

- `cout` and `cin` to interact with the user
- `while` or `for` loops to keep interacting with the user, and using `break` to end a loop
- strings and string comparison, to find out what choice the user makes
- numbers, arithmetic operations and comparisons, to keep track of the user's balance and number of operations performed
- conditionals to decide what to do based on the user input
- formatting the output
- named constants to store important information that doesn't change

Let's get started. Here is the overall idea: We are looking at a user's bank account, which starts with a balance of $100.0. The user can perform up to 10 "operations" by typing an instruction word, possibly followed by typing a number, to perform one of the following:

1. Deposit some amount to the account, increasing the balance. However, they are not allowed to deposit an amount more than $200.0 at any one time
2. Withdraw some amount from the account, again with a limit of $200.0 per transaction. But they cannot withdraw more funds than are present in the account.
3. Accrue interest, at a fixed 1.3% rate
4. Exit early (before the 10 transactions have occurred)

## Preparations

Before we get started with code, let's think about what we would need to store, as we will need variables for each of them. For each of these, decide what a good **name** and what an appropriate **data type** would be, and write them down somewhere:

1. We need a variable to store the user's current account balance
2. We need a variable to store the number of transactions that have happened
3. We need a variable to store the user's typed instruction, which would be a word like `deposit`
4. We need a variable to store the user's typed amount, if the instruction required an amount
5. We need a named constant to represent the transaction limit of $200.0
6. We need a named constant to represent the 1.3% interest rate

## General advice

Here are some things you should be mindful of as you work on your code:

- Choose good names for your variables, that explain what the variable is for. They don't have to be super-long, usually a word or two. If it's a normal variable and it uses more than one word, put them together `likeThis`, if it's a constant then do it `LIKE_THIS`.
- Make sure there is proper indentation in your code.
- Use comment lines to explain any steps you feel need explaining, or to help you remember what you need to do.

## Setup

Start by opening up the private codespace that I shared with you. You should see it contain a `Labs` folder, and within it a `Lab1` folder, and in there a file `lab1.cpp`, which is currently empty. You should do the following:

1. In the terminal window, use appropriate `cd` commands to bring your terminal into this directory. When you do `ls` you should be seeing the `lab1.cpp` file name show up.
2. Open the `lab1.cpp` file in the editor. Write a tiny "hello world" program that simply prints hello world.
3. In the terminal, type `g++ lab1.cpp -o lab1` to compile your program and if there were no errors then type `./lab1` to execute the created file. You should see the hello world message printed back. If there were any errors then of course you must fix them. Look to the error messages for assistance in identifying what went wrong.

From this point on, as you add different parts to your application, run the compile and execute commands frequently, to fix any errors as soon as they occur. You can always hit Ctrl-C to exit your program.

## Initial interaction loop

We will start by setting up the main interaction loop:

1. Start by creating a basic for loop that will run for 10 iterations (i.e. as long as there are interactions remaining). Most of our program will happen inside this loop (except for some variables defined outside of it). If you do this right, you can use the remaining interactions as a loop index.
2. For each of those iterations, start by printing a message to the user, that looks like this:
    ```
    Your balance is $123.45. You have 6 interactions remaining. Choose an action by typing the appropriate keyword:
    - deposit      Deposit an amount. You will be prompted for the amount.
    - withdraw     Withdraw an amount. You will be prompted for the amount.
    - interest     Accrue interest.
    - quit         Exit the program.
    ```
    Of course, you will want the two numeric spots to come from corresponding variables. Make sure that the balance is formatted to show exactly 2 digits after the decimal point. The same is true for all other dollar amounts in the assignment.
3. After the loop is completed, print a concluding statement:
    ```
    Thank you for using our system! Your final balance was: $12.34. Goodbye!
    ```
4. When you run the application at this point, it should simply print the options message 10 times, then the final message, then stop. Add a step where you read in the user's action choice (but don't do anything with it yet). The program should now stop after each options message, waiting for you to type a word.
5. Now we'll prepare for handling the four actions. After reading the user's action choice word, you want to create a series of `if, else if, else` blocks to cover each of the 4 possible word choices, along with a 5th case that handles all other inputs. For now put in each block simply a message "you chose ...", just to test the system. For the 5th block that handles all other inputs, put a message saying `"This was not a valid choice! Please try again."`.
6. As an *optional* challenge, you may make it so that the user doesn't lose an interaction when typing the wrong thing. But you don't have to.
7. After you have checked that this above system works, implement the "quit" branch to immediately get us out of the loop.

This is a good time to commit and sync your work.

## Accruing interest

We will start with the case of accruing interest. This doesn't require any further input from the user, other than choosing the "interest" action.

1. Make sure you have created a named constant that holds the 1.3% interest rate, stored as a pure number, so `0.013`. Remember the **naming convention** for named constants.
2. In the "interest" block of the conditionals, compute the interest on the account (multiply the balance with the rate). 
3. Then add this to the balance amount and print a message saying: 
    ```
    You accrued $23.20 based on a 1.30% interest rate.
    ```
    Make sure that both amounts are printed using 2 decimal places, and that the `1.30%` is actually computed using the named constant, rather than typing it out. If we decide to change the rate in the constant, then this value should automatically adjust.

That's it! Short and sweet.

This is a good time to commit and sync your work.

## Depositing

Now we will work on the deposit block. This requires a bit more work as we want to prompt the user to give us an amount to withdraw. All this work will happen within the "deposit" block:

1. Make sure there is a named constant that represents the $200 "transaction limit".  Remember the **naming convention** for named constants.
2. Print a message to the user, saying "Please type an amount to deposit: "
3. Read in the amount that the user has typed
4. If the amount exceeds the transaction limit, then print a message saying "I'm sorry, but the transaction limit is $200.00". Make sure that number is based on the named constant, and not directly typed in.
5. If the amount did not exceed the transaction limit, then add the amount to the balance, and print a message saying something like "You have deposited $123.22"

This is a good time to commit and sync your work.

## Withdrawing

Withdrawing will follow the same steps as the deposit section above, except for changing "deposit" to "withdraw" everywhere, and subtracting funds instead of adding them. However, there is one extra step:

After checking that the amount is no more than the transaction limit, but before adjusting the balance, you must check that the amount is not more than the balance (because then withdrawing it would have resulted in a negative balance). If it is, then print a mesage like "I'm sorry but you don't have enough funds for this."

You should now have a workable setup.

This is a good time to commit and sync your work.

## The end

1. Make sure your code is properly formatted and indented
2. Make sure, one last time, that you can compile and run your application
3. Make sure you have committed and synced.
