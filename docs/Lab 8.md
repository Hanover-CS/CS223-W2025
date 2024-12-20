---
title: Lab 8
tags: [CS223, data structures, lab]

---

# Lab 8 Dictionaries via binary search trees

In this lab we will implement dictionaries via binary search trees. A dictionary fundamentally does the following:

- It holds key-value pairs, for given types of keys and values. For examples the keys could be strings, and the values integers.
- You can efficiently insert a new key-value pair.
- You can efficiently look up the value associated with a given key, or find out if such a key-value entry exists.
- You can efficiently remove a key-value pair.

To implement this, we will store these key-value pairs in the nodes of a binary search tree, using those keys to search through the tree.

To complicate matters, we want the types of the keys and values to be flexible, so we will use template functions and classes through out.

In this lab all our code will go into one file, `tree.h`. Here's what you will find at the start of this file:

- There is a simple `Pair<K, V>` class template, that represents having a pair. It simply gives you direct access to its `key` and `value` properties, and it has ways to test for equality and an `operator<<` implementation.
- There is a `Node<K, V>` class template. A Node consists of a Pair, and left and right pointers. There is a constructor for it, with default `nullptr` values for left and right, as well as an `operator<<` implementation, which uses a recursive `print` function. Study that function, it is a good example of a recursive function. Note also the extra `depth` parameter it takes, which is used to print a node at a certain indentation level. Here is an example of the produced printout:

```
- (C, 5)
  - (B, 3)
  - (E, 7)
    - (D, 2)
    -
```

This is a node with a key-value pair `(C, 5)`, which has a left child with `(B, 3)` and a right child `(E, 7)`. The left child has no children, while the right child has a left child `(D, 2)` and no right children.

The Node class also contains an implementation for `==`, as well as a version of that, `bothNullOrEqual`, which compares two node pointers. It marks two node pointers as equal if they are both `nullptr` or if they point to nodes that are equal. And two nodes are equal if their pairs are equal and their left pointers are `bothNullOrEqual` and their right pointers are `bothNullOrEqual`. This is implemented via recursion, the two functions `==` and `bothNullOrEqual` calling each other. Make sure you look at their code to understand how that works. The effect of this is that we can use it to compare two trees, describing them as equal if their contents match.

Next is the `BSTree` class, where you will have to now implement most of the functionality. Function stubs have been provided for all functions, you just need to fill in the code. Most functions contain two versions, one is the tree version and one is the recursive version that takes in a node.

The class contains a `Node` pointer `root` that starts out as a `nullptr`.

Note: By default, deleting a node deletes just that node, not its children. We want that to be the case. But we also implement a `deleteAll` function that aims to delete all the children. This is implemented for you, and it is a good example of a recursive function.

The nodes are meant to be organized based on their key: left descendants all have a smaller key than their parent, while right descendants all have a larger key.

To run the tests for this file, run `make allTests`.

## size, depth

The first set of functions you should implement are `size` and `depth`, and their node counterparts `sizeFrom` and `depthFrom`. The tree versions simply call their node counterparts on the `root` node. The size of a tree is the total number of nodes, while the depth of a node is the length of the *longest* path from the node to the bottom, not counting the node itself. So a single leaf node has a depth of 0. By convention, a `nullptr` has a depth of -1.

## insert

The next set of functions is the pair of `insert` and `insertUnder`. These are meant to let us insert a new node at the appropriate location, starting with a given key and value. The `insertUnder` version is, given a node, supposed to return whatever node is supposed to be at this place in the tree after the insertion is complete. So most of the time it should return the provided node, after calling `insertUnder` on it. But if the current node is null, then it is supposed to return the new node that will be created.

One special case you will need to handle is if there is already a key-value entry in the tree for your searched key. In that event you are supposed to simply change the value at that node, instead of creating a new node.

## findMax

Next we have `findMaxNode`. It is given a start node pointer, and it is supposed to return a pointer to the max node, which can be obtained by following right children until there is no right child. This will be `nullptr` unless what we started with was a `nullptr` to begin with (empty tree).

There is also a `findMax` function. It is supposed to return the value that is at the max node. If the tree was empty it should be throw a `domain_error` exception.

## find

Next we have `find` and `findFrom`. Now their return types are bit more complicated. `findFrom` starts from a node, and searches for the provided `key`. If it finds a `key-value` pair that contains this key, then it returns a pointer to that pair's location. If no such pair exists then it returns `nullptr`.

The `find` function, which is supposed to call on `findFrom` for the heavy lifting, is supposed to return a pointer to a value. So if `findFrom` did not find a pair it should return `nullptr`, but if `findFrom` returned a pointer to a valid pair then it should return a pointer to the value that is within that pair.

## removeMax

Next we have two methods `removeMax` and `removeMaxFrom`. `removeMax` is supposed to simply remove the maximum key node. The hard work is done by `removeMaxFrom`, which is given a node to start from. it is supposed to remove and delete the max node within the subtree starting from the given node, throwing a `domain_error` exception if it was a `nullptr` to begin with. The function must leave the tree in a valid state, by moving the left child up. To facilitate this, the `removeMaxFrom` function is supposed to return the node that is supposed to at this place in the tree after the removal.

## remove

Next we have the generic removal functions `remove` and `removeFrom`, which given a key aim to remove the node that has that key. The latter form is given a node to start from, and returns the node that is supposed to be at that place after the removal has happened, often the node itself.

To accomplish the removal, you'll have to first locate the node that is to be removed, by recursively searching for the key. When you get there:

- If there is no left child, you can simply promote the right child to take the place of the current node.
- If there is a left child, then use `findMaxFrom` to find the maximum on the left child, then use `removeMaxFrom` to remove this maximum, and adjust your node with the pair you retrieved from `findMaxFrom`.

Once you have successfully gotten your tests to pass, also do `make constructorTests` which will test for any leaks happening.

Congratulations! This was the hardest part of the lab.

## Traversals

There are now 6 remaining functions that implement traversals: `inOrder` and `inOrderFrom`, `preOrder` and `preOrderFrom`, and `postOrder` and `postOrderFrom`. They are given an output stream, and they are supposed to write the pairs to that stream in the appropriate order. Each pair will be followed by a whitespace (*including* the last pair).

Congratulations! All that is left to do is formatting. Run `./format.sh tree.h` to find and fix formatting errors.

