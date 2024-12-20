---
title: Stacks and Queues
tags: [CS223, data structures, presentation]

---

# Stacks and Queues

---

| Structure | Operations         |
| --------- |:------------------:|
| Stack (LIFO)    | push, pop, isEmpty |
| Queue (FIFO)    | enqueue, dequeue   |
| Deque     | insertFront, insertBack, removeFront, removeBack                   |

---

- Static
    - Its max capacity cannot change
    - Lives entirely in the stack
    - Array of fixed size
- Dynamic
    - Its max capacity can change
    - Lives mostly in the heap
    - Can be resizable array or linked list

---

| Structure |          Static          |          Dynamic           |
| --------- |:------------------------:|:--------------------------:|
| Stack     |    array + nextIndex     |        linked list         |
| Queue     | array + front/back index | linked list + tail pointer |
| Deque     | array + front/back index |     doubly-linked list     |

---

Stack with array:

- Index at top element
- Push: Advance the index, put the value
- Pop: Reduce the index, return the value

---

Stack with list:

- top element at the front/head
- Push: insert new head node
- Pop: remove head node

---

Queue with list:

- front at head
- back at tail (need 2nd pointer)
- enqueue: insert at tail, advance tail
- dequeue: remove head

---

Queue with array:

- front index, back index
- enqueue: advance back, insert element
- dequeue: advance front,
- non-circular array: back >= front
- circular array: loop around at the end

