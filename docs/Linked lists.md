---
title: Linked lists
tags: [CS223, data structures, presentation, notes]

---

# Linked lists

---

## The list abstract type

- **list**: an ordered collection of elements
- `addStart(el)`, `addEnd(el)`
- `removeStart()`, `removeEnd()`
- `addAt(el, i)`, `removeAt(i)`
- `getStart()`, `getEnd()`
- `getAt(i)`
- `size()`, `isEmpty()`
- iterate over all the elements

---

### Implementations

- Dynamically resizable arrays
    - double capacity and copy when full
    - Move elements back/forward on removes/inserts
- Linked list of nodes
- Tradeoffs: Each implementation has its advantages and disadvantages

---

### Linked List

- Built out of list nodes
- Each `ListNode` has a value and a pointer to next node
- There is a `head` pointer to first node
- Last node points to `nullptr`
- Draw example with elements 4, 5, 6
- Have to often traverse from start to end

---

```cpp=
class ListNode {
   public:
    double value;
    ListNode* next;
    ListNode(double v, ListNode* n = nullptr) {
        value = v;
        next = n;
    }
}
```

---

```cpp=
class LinkedList {
   private:
    ListNode* head;
   public:
    LinkedList() { head = nullptr; }
    ~LinkedList();
    long size() const;
    bool isEmpty() const;
    void addEnd(double v);
    void addStart(double v);
    void addAt(double v, int i);
}
```

---

### Examples of functions

Implement:

- `isEmpty()`, `size()`
- `operator<<(out, lst)`   "4 -> 5 -> 6"
- `addEnd(v)`, `addStart(v)`
- `removeEnd()`, `removeStart()`
- Same as above, but now the list also has a `tail` pointer

