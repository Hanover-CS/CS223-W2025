---
title: 'Lab 6: Doubly-linked lists'
tags: [CS223, data structures, assignment]

---

# Lab 6: Doubly-linked list with sentinel

In this lab we will implement a variation of the simple linked list, namely a "doubly-linked list with sentinel". 

## Doubly-linked lists with sentinels.

The key features of this list are as follows:

- There is a special `sentinel` node, that is always the "head" of the list. This node's value doesn't matter, and it is not a "real" node, but it is always there to mark the beginning of the list, and used to detect the end of the list: the `next` of the last element in the list will be the sentinel. So the list kinda loops around to the beginning, if you like. The big advantage of this arrangement is that the head of the list is never null, so you don't need to have any special code to handle the empty list, in most cases. This makes our list code a bit simpler at times.
- Each node contains two pointers, one to the `next` node and one to the `prev` node. This adds a bit of a maintenance cost, but it comes in quite handy, as we have direct access to the end of the list via `sentinel->prev`.

To visualize this, you want to draw each node as 3 boxes in a row. The middle box has the value, the left box has an arrow to the previous node and the right box has an arrow to the next node. Draw such a picture now with a sentinel node followed by a node with value `3`, to a node with value `5` then a node with value `8` which points to the sentinel. Keep this visual image in mind when working on things.

One special case is the case of an empty list. In that case the `sentinel->next` will be the `sentinel` itself, and `sentinel-prev` will also be the `sentinel` itself. This may seem a bit weird but it basically makes things work out smoothly. But you want to also draw this picture and keep it somewhere, because it's an important model. When you initialize your list in the constructor, you will need to make this arrangement happen.

### Invariants

Here are some invariants of the doubly-linked list with sentinel. Recall that the idea with these invariants is that they are true at the beginning of each method and they must be true as well at the end of the method. So your functions can use these invariants, and must also make sure to maintain them if they change stuff around.

1. `sentinel` is never `nullptr`, and it is a constant pointer (but not a pointer to const).
2. A node's `prev` and `next` pointers are never `nullptr` but always point to actual nodes.
3. For nodes `A`, `B`, if `A->prev == B` then `B->next == A` and vice versa.
4. Another way to say 3 is to say that `A->prev->next == A` and `A->next->prev == A`.
5. The first node in the list is `sentinel->next`. The last node is `sentinel->prev`.

### Draw pictures, plan things!

As you work on the functions below, you may find yourself stuck at times. It is imperative that you draw pictures to guide yourself through the steps that might need to happen, and to work through your logic in paper first. There are not many lines of code in this assignment, but they are not always easy steps. If you use a loop at some point, and move some pointer through the list, make sure you have clear in your mind what the pointer points to: Is it the element you want to work with, is it the one before it, or what? And so on. If you just straight into the code, you may easily end up confusing things.

## Implementations

### DLinkedNode :: prevent multiple imports

First of all, let's see if we can get our tests going. Take a look at the `Makefile`, and notice some familiar patterns:

- There is a generic rule for how to turn any `.cpp` file into a `.o` file.
- There is a more specific rule for `tests/tests.o` which tells it to watch for changes in the `tests.cpp` but also the `DLinkedNode.h` and `DLinkedList.h` files, and recompile the tests if any of those files change. Note that this is a rule without any instructions following it: There are no indented lines of code after the rule. What this means is that the system will simply use the existing `%.o: %.cpp` steps. We just wanted add more "trigger" conditions.
- Then there is a rule for `allTests`, which will link the `.o` file into an executable `allTests` then try to execute that file.

So let's try to run this `allTests` rule. Type `make allTests`, and you should encounter some compilation errors, complaining about redefinition of `DLinkedNode`.

So recall what that is about: `.h` files often get included in multiple places. But this causes problems because it appears to the compiler that you are trying to redefine things multiple times. The trick is to add a "guard" that prevents this multiple compilation. This guard consisted of 3 parts:

- An `#ifndef` part that looks to see if a certain constant of your choice exists, and only executes what follows if it doesn't exist.
- A matching `#endif` to indicate the end of this conditional block.
- A `#define` line that appears as the first step within the `#ifndef` block, and defines the missing constant so that the next time around the `#ifndef` block will find the constant defined and hence skip to the end.

So, you should now add a guard to our `DLinkedNode.h` file, so that all the current contents of the file happen inside that block. You should name your constant something like `DLINKED_NODE_H`, though there is no check for this: As long as you use the same name for `#ifndef` and `#define` it should all work.

Run the tests after you have done this and you should now be able to carry out a correct compilation.

### DLinkedNode :: insertNode

Now we come to perhaps the most important function of this lab. We will add a `insertNode` method to our `DLinkedNode` class. What this method does is that it is given a node that is "detached", i.e. it is not linked to any other nodes, and the method is supposed to insert this node immediately following `this`, and adjusting all the appropriate pointer values so that nothing is lost (i.e. what used to be the node after `this` would now be after the new node instead, etc). There is a total of 4 pointers you need to fix, and you need to do it in the correct order. Draw some pictures to make sure you understand the process.

No, really, draw some pictures of what needs to happen to correctly inject this new node between `this` and its next. Don't just into code until you have a good picture of what needs to happen.

When you are ready to code, go ahead and add an `insertNode` method that takes as input a pointer to a node and carries out the insertion. Add your code inline in the `.h` file. Once you are ready to try it out, remove the `DLINKED_NODE_INSERT_NODE` define line, and `make allTests` to run the tests again. You should have 3 test cases passing and 1 failing.

Take a look at the one failing, and see what it talks about. It should be about inserting the node after itself. I bet you didn't think of what would happen if we try to insert the node after itself, i.e. if `newNode` and `this` are actually pointers to the same node. And that's an important lesson, you have to always think of the edge cases like this. Granted this should never happen, after all they were only supposed to give us nodes that are "detached". But nonetheless, we'll add some checks for this.

Now walk through your code steps in this example and note how it would all go really bad if we allowed something like this to happen: If we have node A followed by B followed by C, and we try to add A after A (i.e. after itself), what happens to the pointers?

The conclusion you should be coming out with is that it would be really bad for something like this to happen. In such situations you have broadly 3 ways of handling it:

- You can simply assume/expect that users of your function will never do this. Some times that works. In this case it is bound to just cause us trouble when we do it accidentally as we try to do the work of the linked list class. And it will lead to hard-to-detect errors.
- You can recognize the situation and silently ignore it. In this case we could start by checking to see if `this` and `newNode` are the same pointer, and if they are then we simply return without inserting the node. That's also usually a bad idea, as the user of our function thinks that they have achieved their goal, and will later be confused.
- Finally, you can produce some kind of error. The standard mechanism for doing this is by throwing an exception. You should do this in this case: If the pointers are the same, thrown a string exception that says something like `"Cannot insert node after itself"`. Make sure it is a string that you recognize, because you might end up seeing that message if your list code does some stuff wrong.

Adding this appropriate one line should now make your tests pass.

### DLinkedNode :: removeNode

Now time to implement one more inline method in `DLinkedNode`. Start by removing the `DLINKED_NODE_REMOVE_NODE` define and running the tests, and you should get some compile errors since there are tests expecting a `removeNode` method now.

This method is called `removeNode()` and it takes no parameters and returns nothing. But what it does is this: It works under the pre-condition that the `this` node is part of a valid chain of nodes, and it does the needed steps to "detach it", so that its previous and its next nodes are now directly connected instead. You will need to adjust two pointers to make this happen. You don't need to worry about changing the `next` and `prev` pointers of the `this` node, as the assumption is that this node is about to be deleted. But if you want to set them to `nullptr` you can do so. But don't `delete` any nodes! That's not this method's responsibility.

Now your tests should be all passing.

And this is the end of our work on the `DLinkedNode.h` file and class. The rest of our work will take place in `DLinkedList.h` and `DLinkedList.cpp`.

### DLinkedList class :: basics

Now we start the implementation of the DLinkedList class. As a first set of changes you should do the following:

- To the `Makefile` add a line that says that `DLinkedList.o` should depend on `DLinkedList.cpp`, `DLinkedList.h` and `DLinkedNode.h` (but with the same build rule, so just declare dependencies like we did with `tests/tests.o`).
- Add `DLinkedList.o` to the list of dependencies for `allTests`. Also in the `allTests` rule change the `$<` to `$^`. This would make it include "all the dependencies" instead of only the first one.
- Remove the `DLINKED_LIST_CLASS` define to reveal the first set of tests for this class. At this point you should be getting compile errors on your tests, looking for a `DLinkedList` type that doesn't exist yet.
- Add a "double-include guard" to the `DLinkedList.h` file, similar to what we did for `DLinkedNode.h` (but use a different constant name, of course).
- Add a `DLinkedList` class definition to the `DLinkedList.h` file. It should contain a private member variable called `sentinel` that should be a "constant pointer to `DLinkedNode`. The compiler will not directly force you to make it a constant pointer, but make sure you do so. (And note, NOT a pointer to constant).
- (Even if not mentioned below, you should only add function prototypes to this `.h` file, and add function definitions in the `.cpp` file instead)
- Add a `size` member function prototype that returns an int, takes no parameters, and should be marked as `const`.
- Add a `isEmpty` member function prototype that returns a boolean, takes no parameters, and should be marked as `const`.
- Add stub implementations in the `.cpp` file for these two functions, returning obvious values.
- At this point if you have done things right, when you try to compile the tests it should give you an error about a "non-static const member in class without a constructor". If you are not seeing this error, go back and make sure that you marked `sentinel` as a const pointer.
- Now what this error is saying is that we have marked the `sentinel` as a constant, and therefore we must make sure that it is initialized in the constructor. As the default constructor would not do that, we must make sure to provide a constructor. So add a no-parameter constructor prototype. It takes no parameters, but you will have to implement it.
- At this point compliling the tests would give you a link error about undefined reference: you promised a constructor but didnt provide one.
- Now in the `cpp` file implement this no-argument constructor. Here is what needs to happen there: We basically need to create a new node, set it to the sentinel and then also make sure that its `next` and `prev` pointers point to itself. But there is a catch: You can't actually write something like `this->sentinel = ...`, because this would be "reassigning" the value of the sentinel pointer and it was supposed to be constant. What you need to do here is use "member initialization list" syntax. Check out the constructors chapter for details on how to do that. You can set sentinel's `next` and `prev` pointers normally though (but remember they should point back to the sentinel, not to nullptr), it's just the sentinel pointer itself that you must set via member initialization list. Make sure you understand why, and ask me if you don't.

At this point your tests should be compiling and passing. Of course things might fail when we implement `size` and `isEmpty` for real, but as we have no way of adding new elements to the list right now there's no meaningful tests we can write, yet.

### DLinkedList class :: insertStart

We will now implement our first function, that will allow us to add new elements to the list. It is called `insertStart`, and takes as parameter a value of type `T`. You need to remove the `DLINKED_LIST_INSERT_START` define then add a prototype in the `.h` file and a definition in the `.cpp` file.

What this function must do is to use the value of type `T` that it was given to create a new node, then insert that node at the very start of the list. Make sure you use the `insertNode` method to achieve the insertion part.

At this point in order to pass the tests should also have a correct implementation of `isEmpty` and `size`. `isEmpty` is relatively easy, you just need to be able to detect if the list is empty. There is something about the `sentinel` node that should help you in that direction. As for `size`, you will need to traverse the list and count nodes as you go, making sure you correctly detect when you've made it to the end.

Once you have all these methods implemented correctly, the next set of tests should be passing. It still doesn't test that you implemented the insertion correctly, we'll be able to test that better after the next function. So you may have to come back to the `insertStart` method at that point.

### DLinkedList class :: operator<<

In order to help us diagnose problems, we'll implement the output stream operator early on. Recall that this function must be declared as a `friend`, and it must have a very specific signature:

- Its name is `operator<<`
- It takes as first parameter an `std::ostream&`, typically called `out`, and also returns that reference. Remember that we have to use the full `std::`-prefixed names in the `.h` files, to avoid `using namespace`.
- Its second parameter is a "const reference" to our class type.

You must also include the `ostream` header so that you have access to `std::ostream`

You should print out the list as follows:

- If the list is empty, write the word `"empty"` to the stream.
- Otherwise write to the stream the longs in the list with arrows between them, for example something like: `5 -> 23 -> 2`. You must take care not to have an extra arrow or spaces at the end or start.

Remove the `DLINKED_LIST_OPERATOR_OUT` define to activate the next set of tests.

At this point you may find yourself having to go back and fixing your implementation for `insertStart`.

You may get various errors when trying this part out. Make sure you read the messages and understand what they are telling you, and ask me if you are not sure.

### DLinkedList class :: getAt

Next we'll implement the `getAt` function. What this function does is given an index looks up that place in the list. So for example `getAt(0)` will return the value stored at the first element, `getAt(1)` will return the value stored at the next element, and so on. For starters, you may assume that the index given to you is in the range of valid indices (so between `0` and `size-1`). We will add more functionality in a moment. Make sure your function is marked as const, and returns a value of type `T`.

To implement this correctly, you'll need to do a loop, counting the index down while simultaneously moving forward through the list. Once implemented, remove `DLINKED_LIST_GET_AT` and you should be able to pass the first few of the tests.

The remaining test uses negative indices, Python style: So `getAt(-1)` should return the last element, `getAt(-2)` should return the next-to-last element and so on. You should now implement the logic for this: Place your previous code inside an `if` to handle the case of non-negative indices, then add a `else` block for the negative indices.

Note that you should not use the `size()` method at any point during your implementation of `getAt`, while you may be tempted to do so. It is an extremely slow function as it has to traverse the entire list every time.

### DLinkedList class :: out of bounds exceptions

Now we'll add some "index out of bounds" protection: We want the function to tell us when we try to access out of bounds. So this is what this next set of tests is about.

Start by removing the `DLINKED_LIST_OUT_OF_BOUNDS` define. Now you can try to run the tests, and you will get compile errors: The tests expect to find a class called `OutOfBoundsException` within the list class, so add such an "empty" class definition. We'll only use this to throw exceptions when we are out of bounds. After you add this class you should get some normal test failures. You will need to go back to your implementation of the `getAt` function and add appropriate checks to throw this exception where needed.

### DLinkedList class :: insertEnd

Next we'll take a "break" and define a relatively easy method, namely `insertEnd`. It is given a value of type `T`, and it is supposed to insert it at the very end of the list. Make sure you use the `insertNode` function for this, once you create a new node and once you find the last node in the list.

Remove the `DLINKED_LIST_INSERT_END` define to activate the tests.

### DLinkedList class :: insertAt

One last insert method, and maybe the hardest of them: `insertAt` is supposed to take two parameters: First an int index where to insert, and second a value of type `T` to use on the new node. Unlike its `getAt` counterpart this method should not accept negative indices, you should immediately throw an out-of-bounds exception in that case. Note that this function will essentially end up moving the node that is currently at index i one spot further down the list. You can look at the test examples to make some sense of this. Also note that unlike most index-based functions, where the index just after the end would not be valid, for this function this is still a valid index, resulting in inserting the new value at the very end without shifting anything. You shouldn't need to handle this in any special case, just something to note.

Remove the `DLINKED_LIST_INSERT_AT` define to activate the tests.

Make sure you also take care of throwing out of bounds exception if an attempt is made to insert out of bounds. Placing the checks for this might get tricky. 

### DLinkedList class :: removes

The last set of changes has to do with removing elements. There are 3 remove methods:

- `removeStart()` removes the very first element
- `removeEnd()` removes the very last element
- `removeAt(int i)` removes the element at index i, throwning out of bounds exceptions  for negative numbers and for index past the last element.

You also need to add a new exception class `EmptyListException`, thrown by the `removeStart` and `removeEnd` methods if they are called on an empty list. Don't forget to make use of the `removeNode` method you implemented earlier.

Remove the `DLINKED_LIST_REMOVES` define to activate the tests.

### Handling memory leaks

Unless you took extra steps not described above, your program would at this point have a number of memory leaks. Let's examine them. Make a new rule in the `Makefile` called `checkLeaks`, and make it depend on `allTests`. Then in its execution line have it do: 
```
valgrind --leak-check=full --log-file=report.txt ./allTests
```
You should also go to all the `g++` steps in the `Makefile` and add a `-g` flag to them, so they say `... -Wall -g ....`. This will give us better error information.

If you've done this correctly, then doing `make checkLeaks` should run the tests then produce a leak report in a `report.txt` file. You should see a long report that shows you some leaks and how they occurred. In my case it reported definitely losing 456 bytes in 19 blocks and indirectly losing 1080 bytes in 45 blocks blocks. Your numbers may be different.

Look through these reports. Some of these would be clear, pointing to the constructor of `DLinkedList` where we allocated a new node for `sentinel`. But we never de-allocated that storage. So we'll need to do that in a destructor, but let's keep looking first. You should also be finding references to one of the `insert` methods, as they are also creating new nodes that we never deallocate. So we'll need to address these two things:

- All the `remove` methods should also deallocate the node they are removing. And this should NOT happen in the `removeNode` method because the `DLinkedNode` class was not the one responsible for allocating in the first place: Whoever was responsible for allocating should also be responsible for deallocating, unless it is clear that they are passing the responsibility over to someone else. So what you want to do in this case is create a little helper function `detachAndDeleteNode` that takes the pointer to the node as a parameter, then uses `removeNode` to remove the node and then deletes the node. All three of your `remove` methods should then call on this method to do the "hard work". This doesn't have to be a member function, it's just a regular function existing only in the `DLinkedList.cpp` file.
- If you have done this correctly, then you should see your number of lost bytes and blocks go down a bit, but not entirely. This is because we have another big source of leaks, namely all the nodes left in a linked list when it gets deleted. We must now deallocate all of those nodes. Do this now, create a destructor for the class, and in it loop over all the nodes and delete one at a time. Leave the sentinel for last. You don't need to worry about using `removeNode` here, you can just `delete` the nodes, as they we are not going to use any part of the list after this, we are in the destructor for the list. However, you should make sure you grab the link to the "next node" before you delete a node: Don't expect that link to exist on the line right after your `delete`, as the system may in the meantime have written something in the memory you released, and broken the link.

If you've done this correctly, you should get an "all clear" report from valgrind.

### Copy and Move constructors

So far we have omitted an important part, namely to set up copy and move constructors and assignments, a necessary step whenever we allocate in the heap. As a quick recap:

- Custom copy constructors and assignments are needed if your class uses heap allocation, to ensure that you create duplicates of those heap positions. Failing to do so usually results in a crash, as you end up with two objects both pointing to the same memory, and causing a double-delete when the constructors are called.
- Custom move constructors and assignments are not strictly needed, but they provide a considerable performance benefit when the source of a copy action is a "temporary object" that is about to get deleted, so we can appropriate its resources instead of copying them.

#### Copy constructor and assignment

In order to test these constructors, I have set up a separate test file. The reason is that in order to properly test these constructors we must first force their execution, as the compiler tends to do a lot of optimizations and "eliding" those constructor calls. So we must compile with a special flag called `no-elide-constructors`. Add the following to the Makefile:
```makefile
constructorTests: tests/constructorTests.cpp DLinkedList.cpp
	g++ -Wall -g -fno-elide-constructors -o $@ $^
	valgrind ./constructorTests -s -ni -npf
```
Then run it with `make constructorTests` (If you copy/pasted it, you'll need to delete the spaces in front of the indented lines and then reindent). What you should find is both failing tests and a fatal error crash, with a SIGSEGV signal. That's a universal sign that you are accessing memory you shouldn't, often cause by deleted memory problems.

So to address this error message, add a copy constructor for `DLinkedList` in the prototype and implement it in the `cpp` file. It should start using constructor delegation to call the no-argument constructor, which sets up the sentinel, then proceed to traverse the nodes of the other list and create a new node for each. You can rely on `insertEnd` for adding these nodes, or you can also use the DLinkedNode class' `insertNode` method, which requires a bit more work. Once you have the constructor implemented correctly, the tests should pass and valgrind should report no leaks. In general, there are three kinds of errors you might encounter:

- Crashes usually mean you forgot to copy something over
- Failing tests probably indicate your copy logic is wrong and isn't copying everything
- Memory leak errors usually indicate you forgot to delete some resource before you lost track of it by moving a pointer around

You might wonder here why we don't need copy constructors for `DLinkedNode`. Even though that node also uses pointers, it itself is not responsible for allocating or releasing any memory.

Now to work on copy assignment. Start by looking at the `tests/constructorTests.cpp` file and removing the `COPY_ASSIGNMENT` define line. This will let the copy assignment tests run, which should now be failing to compile. This is because by default, copy assignment is actually implicitly not allowed in C++. We have to explicitly specify it if we want it to be a thing. So go ahead and add an assignment operator and implement it. You can start with an empty operator that does nothing, but gets the tests to compile at least, then proceed to implement it. Make sure you first remove all the old elements, before adding new elements in. At this point what you should realize is that you are having some duplication going on: Both in this method and in the destructor you had to remove all the existing elements. What you should do instead is create a member function called `clear()` that does exactly that, and then call this member function from both places. You can make it a public member function as it sounds useful in general, or keep it private. Up to you. You may run into some weird errors when you do this: Perhaps when you implemented this in the destructor you deleted nodes without taking care to leave the sentinel at a valid state at the end, but now that you will use the method elsewhere you need to make sure the sentinel points to the right things once you te list is emptied.

Also, you had to copy over all the elements from one list and add them to the other, in both the copy constructor and the copy assignment. You should create a function `copyOver(const DLinkedList & other)` to copy over those elements, then call it from both the copy constructor and the copy assignment. This way your copy assignment method will really be just a few lines of code. And your copy constructor is probably literally just a called to `copyOver`.

Don't forget to also account for the case where we are assigning `l1 = l1`, in which case no changes should actually be happening.

#### Move constructor and assignment

Now we move on to the move constructor. The first challenge is to even get the constructor to activate, as the system will simply use a copy constructor if a move constructor is not provided. So we have no easy test for that. You should start by removing the `MOVE_CONSTRUCTOR` define from the tests file, then add a move constructor prototype and implementation where for now just copy over what you did with the copy constructor. To see the constructors called, add to both of them print messages like so `std::cout << "Move constructor called" << std::endl;` (you will need to temporarily include the iostream header for this to work). Make sure that when you run the tests you see the Copy version of this message showing up amongst the copy constructor test, while two versions of the move message show up in relation to our move constructor test. You may wonder why the move constructor is called twice, and that's a great question! See if you can figure it out. Once you confirm that your move constructor is getting called, remove the printouts and the iostream include.

Now to "fix" the move constructor. Recall that the whole point was to avoid copying over data. Instead we want to appropriate their stuff and give them ours. This would mean replacing our sentinel with theirs, and giving them our sentinel. Alas because we made the sentinel a const pointer this is not possible. So you should go back to our class definition and remove const from the sentinel. Then in your move method, simply swap the two sentinel pointers. Your tests should still be passing.

Now for the move assignment, remove the `MOVE_ASSIGNMENT` define, then create a move assignment function and add a printout to confirm that it is getting called. After that you can remove the printout and fix the implementation so that it simply swaps the pointers (but only after the check for self-assignment).

And this should be it!

### Formatting cleanup

To wrap up this assignment, let's check our formatting. There is a `format.sh` file in your directory, and you should be able to execute it on one of your files as follows:
```bash
./format.sh DLinkedNode.h
./format.sh DLinkedList.h
./format.sh DLinkedList.cpp
```
Make all the corrections the formatter suggests. Then you are done!

## Extra work

If you are feeling motivated (this part is not required), create a "main.cpp" file with a main function that is an interactive session with a list. It would operate somewhat like this:

- Shows the user the current contents of the list, and its size, then prompts for input. The user can then type:
- `insertStart xx` to add the number `xx` to the beginning of the list
- `insertEnd xx` to add the number `xx` to the end of the list
- `insertAt n xx` to add the number `xx` at index `n` in the list
- Similar methods for removing from the list
- Your program will need to implement a `try-catch` system to catch the exceptions that these methods might throw, and show an appropriate message.
- Whatever else you want to add!
