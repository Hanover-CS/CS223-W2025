---
title: Lab 7
tags: [CS223, data structures, lab]

---

# Lab 7: Queues and the maze solver

In this lab we will implement a queue using a circular array, and then use the queue to implement the maze solver. The lab consists roughly of 3 parts:

1. Implementing the needed Queue methods
2. Implementing methods related to representing a maze
3. Implementing a maze solver

Let's get started!

NOTE: There are a lot of different `make` commands you can use for this one. You can run `make list` at any time to get a list of them.

## The Queue class

You will find a `queue.h` file that contains the beginnings of an implementation of a queue class. It contains a template class `Queue` which is based around the idea of a circular array. Before we delve into details, notice that the whole class in included within a `namespace queue` block. This means that the Queue class is namespaced, and when we need to refer to it elsewhere we would need to call it `queue::Queue`.

Now, the main elements of the queue class are as follows:

1. There is an array of a certain initial capacity, which contains in some part of it the elements in the queue.
2. At any given time `front` is the index just before the first element (the element at the front of the queue), while `back` is the index of the last element, is an element exists at all. If it doesn't, then `back` is equal to `front`. This makes sense because in order to enqueue a new element at the back we would need to advance the `back` index, and if it was equal to `front` before then it will now be exactly one spot forward. This matches the expectation that if you move forward from `front` you will find the first element, which in the case of only one element happens to also be the last element.
3. The array is "circular": when we reach the end we simply rotate back to the beginning. To help with this, a `nextIndex(idx)` method is provided, that given one index will return the "next", accounting for the circular nature. You should always call on this function when you need to advance to the next index.
4. The queue is empty when `front == back`.
5. The queue is considered full when only one element is empty. This happens when the index following `back` is actually `front`. This is implemented for you in the `isFull()` function, which you can call on.
6. A constructor is provided, creating an initial array of capacity 5. (So after 4 elements are inserted, the queue would be full).

Make sure you study these provided functions. What you have to do is provide the following three functions. Note that there are spots for these functions in the `queue.h` file, and you should implement them there. Template functions cannot go in a separate `cpp` file.

1. A function template to `enqueue` a new value. This function advances the back index and insert the new value there. You will have to worry about a full queue a bit later, when you get to `resize`.
2. A function template to `dequeue` the value at the front. It would need to advance the `front` index and return the first element. You'll need to throw an `EmptyException` if the queue is empty. Once these two methods are implemented, you can run the tests by doing `make queueTests`.
3. Now the challenging part is to add a `resize` function. First of all you should add a check in `enqueue`, that if the queue is full then we need to call `resize` before inserting. Then you need to implement `resize()` which effectively must: double the capacity, allocate a new array of double the size, copy over the appropriate entries, then delete the old array and do any needed adjustments to `front` and `back`. This function is somewhat tricky, so make sure you draw some good pictures of the situation, and make a plan. You have a couple of different ways to approach this problem. The key thing to watch out for is that because your array capacity is changing, the circular nature will also change. You can't put all numbers in the same places they are an expect it to work. Make sure you understand why this is the case.

To test part 3, remove the `#define RESIZE_IMPLEMENTED` line to enable the resize tests.

## The Square and Maze classes

The main objective of this lab is to work with mazes. A maze is a rectangular arrangement of squares, where each square is either a wall or a plain square, and there is a marked start square and a marked exit square. The key question we want to answer is if there is a path we can follow from the start square to the exit square that goes only through plain squares, and no walls.

Start by taking a look at the `square.h` and `square.cpp` files. This class is fully implemented for you, you just need to understand it and what methods you have access to with it.

One important thing to note is the `kind` and `status` member variables, and what role they might play.

Next you want to look at the `maze.h` file. In particular note the fields that a maze contains, listed towards the bottom:

- Pointers to `start` and `end` squares.
- A "pointer to pointers of squares". This is basically a 2-dimensional array of squares, but because it is dynamically generated we have to do it this way: It is basically an array of the rows. Each row is itself an array of squares.
- And of course the number of columns and number of rows, specifying the dimensions of the maze.

Take a look at the files in the `maze_files` directory for some examples of mazes and how they will be stored in files. One of the early methods you will implement is to read such a file. Take note in `square.h` of the meaning of each symbol.

### constructFromStream

The first method you need to implement is called `constructFromStream`. It is used by a constructor that takes in istream as input, and it is also used by a constructor that takes a string as input, then converts it to an istream. Here's what needs to happen:

- You should initialize the start and exit pointers to be nullptr.
- You first read two numbers from the stream. They are supposed to be your rows and columns. If either of them is less than 1 you want to throw an `std::domain_error` error message.
- If the row and column numbers are reasonable, you now need to allocate space in the heap. You need to assign to the `grid` variable a dynamically allocated array of size equal to your row number, whose entries are pointers to squares. Then loop over each entry of this array, and initialize it with value equal to an array of squares of size equal to the number of columns. So after you have done this, you have "an array of arrays of squares", and something like `grid[1][2]` gives us the square on row 1 and column 2. But there are no squares yet as we haven't created them.
- Next you want to do a double loop over rows and columns, and in each case read the next character, and assign a square to the appropriate grid entry. If the character is invalid, then the square constructor will be throwing a `domain_error`.
- If the stream ends before you can read all those entries then you need to throw a `domain_error`.
- If you encounter a square marked as start, store it also as the start pointer entry. Similarly for exit squares.
- If there are multiple start or exit squares, this should be a `domain_error`.
- If there are no starts or no squares, this should be a `domain_error`.

You can run the tests with `make mazeTests`.

Note that this is a fairly long method, close to 40 lines. Plan it carefully.

### Provided methods: `get`, `operator<<`

Note that there is a `get` method provided for you, and you can call it on a maze object with something like `m.get(2, 3)`. It will throw `domain_error` if the index is out of bounds.

Another method provided to you is `operator<<` for writing the maze to a stream. You can use this for diagnostics.

### Destructor

You will need to implement the destructor, which has a bit of work to do. Remember that our grid is an array of "row arrays". In order to clean it up you cannot just delete the array, because this will not actually call delete on each row array. So you need to first loop through your array and array-delete each entry, then array-delete the whole thing.

Run `make mazeConstructorTests` to check for leaks. You will find that even after you implemented the destructor correctly, you would still have a number of lost bites. This is because our `constructFromStream` method from earlier did a very bad thing: It threw exceptions without cleaning up after itself. So the `grid` that is allocated will not actually be deleted. This is because the destructor won't actually run if the constructor didn't successfully complete.

In order to fix those errors, wrap the part of the `constructFromStream` method following the allocation of `grid` into a try-catch block, catching the `domain_error` exceptions. In your catch block you should do the work to delete the array, and then throw the exception again (the same enough you caught, can just re-throw it). This should now clear all the leaks.

### getNeighbors

The last method in the maze class is `getNeighbors`. It uses the `std::vector` class, so you should make sure you know the basics of a vector first. The main idea of a vector is that it is like an array but automatically resizes itself. The main function you need to know about it for this part is `push_back`: It is given a new value, and it appends it to the end of the vector, after any previously inserted elements.

The `getNeighbors` method's job is the following: It is given a square, and it is supposed to return a vector of pointers to squares. The vector should contain all "neighbors" of the square that are not wall squares. A neighbor is a square that is just north, south, left or right from the current square. So you'll need to find out the current square's location, then compute the possible 4 neighboring points and if they are valid entries and also not walls then add them to the provided vector, before returning it. You can use the `get(i, j)` method to retrieve the needed pointer to a square. You'll want to get back to running the `make mazeTests` command to test this function. Note that the order in which you place the squares in the function technically doesn't matter, but some later tests will expect the order to be north - right - south - left.

### Formatting

Before moving on, run `./format.sh maze.cpp` and make sure to fix any formatting errors.

## MazeSolver

Finally we get to reaping the fruits of our labor, and implement the MazeSolver class. The maze solver is given a maze, and also contains a "worklist" queue that it can use to maintain a list of squares that still need to be examined. You should carefully read the instructions in `mazeSolver.h`, as well as the instructions that follow right here, then implement the needed methods in `mazeSolver.cpp`. But in brief the idea is as follows:

- You start by placing the start square in the worklist queue.
- As long as the worklist queue is not empty:
    - You take out the next square (a pointer to it, more precisely) to work with it. I will refer to this as the current square.
    - If that square is the exist square, then you have successfully found a solution to the maze, and you can find the path by following the `prev` pointers. It's effectively a linked list that leads back to the start. Then you can return from the `solve` method.
    - Otherwise, you identify the neighbors of this square, and focus on those that are unvisited.
    - For those unvisited neighbors, add them to the worklist, mark their status as being in the worklist, and set their previous to be the current square.
    - At this point you are basically done processing the current square, so you mark it as explored, and loop back to dequeue the next square from the worklist queue.
- If the worklist is empty, it means you have not stopped earlier by finding a path, and there are no more reachable squares to explore. Therefore this is a case with no solution.

The functions you will need to implement are the following:

- `MazeSolver(std::istream& in)` and `MazeSolver(std::string& s)` are two constructors that simply have to initialize the two member variables, by using the provided parameter to create a maze, and by creating a new empty queue worklist. Note that the variables are marked as `const`, and therefore you must use the initialization list syntax for it.
- `~MazeSolver()`, the destructor, must simply delete any pointers that were allocated in the constructors.
- `get_path(mazelab::square* sq_ptr)` is a function that given a square follows the prev pointers to return the entire path to this square from the start square. The path must be returned so that it starts from the start square and ends with the provided square. You'll want to implement this as a recursive function: Call yourself on the prev square, and get back the vector that is the path up to that previous square, then use `push_back` to place yourself at the end and return the path. The baseline case is if the provided square pointer is the null pointer (which should happen when you are in the start square and try to go to previous), and in that case the function should return an empty vector.
- `print_vector_of_squares(std::vector<mazelab::square*> squares)` is given a vector of squares, and must simply loop over the vector to print each square, separated by spaces (and with no trailing space). You must print the positions of the squares, so something like `[1, 2]`
- `mark_solution(std::vector<mazelab::square*> squares)` is given a vector of squares that is meant to be the solution path, and must mark those squares as solution squares in their status.
- `mazelab::square* step()` is a helper function that essentially does one step of exploration. So it is supposed to take the next square out of the worklist, fully explore is as described in the steps earlier, then return it. If it is called and the worklist is empty then it returns a nullptr.
- `std::string solve()` is the main function that is called to try to solve the maze. It will for the most part use `step` to do the hard work. It must end by returning `"unsolvable"` if there is no possible solution. If there is a solution, then the solution path must be marked via `mark_solution` and the return value is the printed version of the path as produced by `print_vector_of_squares`.
- `std::ostream& operator<<(std::ostream& out, const MazeSolver& solver)` simply prints the current progress state; Look in the `.h` file for the expected format.

In order to test your work, you can run `make mazeSolver`, which creates the `mazeSolver` executable, followed by `./mazeSolver`. You can also get some basic leak information by running `make mazeSolverLeaks`.

You can run a specific maze file instead of the small example built in to the main function, by running `./mazeSolver filenameHere`. For example to run the file `maze_b1.txt` in the `maze_files` directory you would do: `./mazeSolver maze_files/maze_b1.txt`

IMPORTANT DIAGNOSTIC TOOL: As you work on figuring out any problems, you may also find it helpful to add a step that uses the maze `operator<<` in your `solve` method to print the state of the board after each step. It will be helpful for seeing if the correct steps are being followed, basically. You can do this by adding a single line. You can then comment out the line whenever you don't need it.

Note that no automated tests are provided for this! You are responsible for ensuring that your logic is correct. You may find it useful to design your own mazes in some files, then load them in.

Don't forget to run `./format.sh mazeSolver.cpp` and fix any formatting errors.