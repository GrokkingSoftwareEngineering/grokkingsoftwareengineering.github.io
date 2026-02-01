# Linked Lists

Reading time: 25 minutes

A **linked list** is a linear data structure where each element, called a **node**, contains data and a reference (or pointer) to the next element in the sequence. Unlike arrays, linked list elements are not stored in contiguous memory locations. Instead, nodes can be scattered throughout memory, connected only by their references to one another.

This fundamental difference gives linked lists unique trade-offs compared to arrays. You gain flexibility in insertion and deletion operations but lose the ability to access elements by index in constant time. Understanding when these trade-offs favor linked lists will help you choose the right data structure for your specific problems.

## The anatomy of a node

Every linked list is built from nodes. A **node** is the basic building block that holds two essential pieces of information: the data itself and one or more references to other nodes.

### Basic node structure

The simplest node structure looks like this:

```javascript
class Node {
    constructor(data) {
        this.data = data;
        this.next = null;
    }
}
```

The `data` property holds whatever value you want to store. It could be a number, a string, an object, or any other data type. The `next` property holds a reference to the next node in the sequence, or `null` if this is the last node.

### Why references matter

In arrays, you find an element by computing its memory address from the array's starting address and the element's index. The address of element `i` is simply `startAddress + (i * elementSize)`. This arithmetic works because elements are contiguous.

In linked lists, you cannot compute addresses this way. Each node only knows where the next node is. To find the fifth element, you must start at the first node and follow four `next` references. This is the fundamental reason linked lists have O(n) access time instead of O(1).

However, this same property makes linked lists flexible. Since nodes only need to know about their neighbors, you can insert or remove nodes by updating just a few references. No shifting of elements is required.

## Singly linked lists

A **singly linked list** is the simplest form of linked list. Each node has exactly one reference, pointing to the next node in the sequence.

### Structure and representation

A singly linked list maintains a reference to its first node, called the **head**. Optionally, it may also track the last node (the **tail**) and the total count of nodes.

```javascript
class SinglyLinkedList {
    constructor() {
        this.head = null;
        this.tail = null;
        this.length = 0;
    }
}
```

The list is empty when `head` is `null`. When you add the first node, both `head` and `tail` point to it. As you add more nodes, `tail` advances while `head` stays at the beginning.

### Visual representation

Consider a list containing the values 10, 20, and 30:

```
head -> [10 | next] -> [20 | next] -> [30 | next] -> null
                                            ^
                                           tail
```

Each box represents a node. The first section holds the data, and the second section holds the reference to the next node. The last node's `next` points to `null`, indicating the end of the list.

### Traversal

Traversing a singly linked list means visiting each node from head to tail. You start at the head and follow `next` references until you reach `null`.

```javascript
traverse() {
    const result = [];
    let current = this.head;

    while (current !== null) {
        result.push(current.data);
        current = current.next;
    }

    return result;
}
```

**Time complexity:** O(n), where n is the number of nodes. You visit each node exactly once.

**Space complexity:** O(1) for the traversal itself (not counting the result array). You only need a single pointer variable.

### Inserting at the beginning

Adding a node at the head of the list is one of the most efficient operations:

```javascript
insertAtHead(data) {
    const newNode = new Node(data);

    if (this.head === null) {
        // List is empty
        this.head = newNode;
        this.tail = newNode;
    } else {
        // Point new node to current head
        newNode.next = this.head;
        // Update head to new node
        this.head = newNode;
    }

    this.length++;
}
```

**Time complexity:** O(1). You perform a fixed number of operations regardless of list size.

**Why it is fast:** You do not need to traverse the list. You simply create a new node, point it to the current head, and update the head reference.

### Inserting at the end

Adding a node at the tail is also efficient if you maintain a tail reference:

```javascript
insertAtTail(data) {
    const newNode = new Node(data);

    if (this.tail === null) {
        // List is empty
        this.head = newNode;
        this.tail = newNode;
    } else {
        // Point current tail to new node
        this.tail.next = newNode;
        // Update tail to new node
        this.tail = newNode;
    }

    this.length++;
}
```

**Time complexity:** O(1) with a tail pointer. Without a tail pointer, you would need O(n) to find the last node.

### Inserting at a specific position

Inserting at an arbitrary position requires traversing to that position first:

```javascript
insertAtPosition(data, position) {
    // Handle edge cases
    if (position < 0 || position > this.length) {
        throw new Error('Position out of bounds');
    }

    if (position === 0) {
        return this.insertAtHead(data);
    }

    if (position === this.length) {
        return this.insertAtTail(data);
    }

    // Traverse to the node before the insertion point
    const newNode = new Node(data);
    let current = this.head;

    for (let i = 0; i < position - 1; i++) {
        current = current.next;
    }

    // Insert the new node
    newNode.next = current.next;
    current.next = newNode;
    this.length++;
}
```

**Time complexity:** O(n) in the worst case. You may need to traverse nearly the entire list to reach the insertion point.

**Key insight:** Once you reach the insertion point, the actual insertion takes O(1) time. The cost is in finding the position, not in inserting.

### Deleting from the beginning

Removing the head node is straightforward:

```javascript
deleteFromHead() {
    if (this.head === null) {
        return null; // List is empty
    }

    const removedData = this.head.data;
    this.head = this.head.next;

    if (this.head === null) {
        // List is now empty
        this.tail = null;
    }

    this.length--;
    return removedData;
}
```

**Time complexity:** O(1). You update a single reference.

### Deleting from the end

Removing the tail node in a singly linked list is more expensive:

```javascript
deleteFromTail() {
    if (this.tail === null) {
        return null; // List is empty
    }

    if (this.head === this.tail) {
        // Only one node
        const removedData = this.tail.data;
        this.head = null;
        this.tail = null;
        this.length--;
        return removedData;
    }

    // Find the second-to-last node
    let current = this.head;
    while (current.next !== this.tail) {
        current = current.next;
    }

    const removedData = this.tail.data;
    current.next = null;
    this.tail = current;
    this.length--;
    return removedData;
}
```

**Time complexity:** O(n). You must traverse the entire list to find the node before the tail. This is a significant limitation of singly linked lists.

### Deleting at a specific position

Deleting at an arbitrary position follows a similar pattern to insertion:

```javascript
deleteAtPosition(position) {
    if (position < 0 || position >= this.length) {
        throw new Error('Position out of bounds');
    }

    if (position === 0) {
        return this.deleteFromHead();
    }

    // Traverse to the node before the deletion point
    let current = this.head;
    for (let i = 0; i < position - 1; i++) {
        current = current.next;
    }

    const removedNode = current.next;
    current.next = removedNode.next;

    if (removedNode === this.tail) {
        this.tail = current;
    }

    this.length--;
    return removedNode.data;
}
```

**Time complexity:** O(n) to find the position, O(1) to perform the deletion.

### Searching for a value

To find a node containing a specific value, you traverse until you find a match:

```javascript
search(value) {
    let current = this.head;
    let position = 0;

    while (current !== null) {
        if (current.data === value) {
            return position;
        }
        current = current.next;
        position++;
    }

    return -1; // Not found
}
```

**Time complexity:** O(n) in the worst case. The value might be at the end or not present at all.

### Complete singly linked list implementation

Here is a complete implementation with all the operations discussed:

```javascript
class Node {
    constructor(data) {
        this.data = data;
        this.next = null;
    }
}

class SinglyLinkedList {
    constructor() {
        this.head = null;
        this.tail = null;
        this.length = 0;
    }

    isEmpty() {
        return this.length === 0;
    }

    insertAtHead(data) {
        const newNode = new Node(data);

        if (this.head === null) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            newNode.next = this.head;
            this.head = newNode;
        }

        this.length++;
    }

    insertAtTail(data) {
        const newNode = new Node(data);

        if (this.tail === null) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            this.tail.next = newNode;
            this.tail = newNode;
        }

        this.length++;
    }

    insertAtPosition(data, position) {
        if (position < 0 || position > this.length) {
            throw new Error('Position out of bounds');
        }

        if (position === 0) {
            return this.insertAtHead(data);
        }

        if (position === this.length) {
            return this.insertAtTail(data);
        }

        const newNode = new Node(data);
        let current = this.head;

        for (let i = 0; i < position - 1; i++) {
            current = current.next;
        }

        newNode.next = current.next;
        current.next = newNode;
        this.length++;
    }

    deleteFromHead() {
        if (this.head === null) {
            return null;
        }

        const removedData = this.head.data;
        this.head = this.head.next;

        if (this.head === null) {
            this.tail = null;
        }

        this.length--;
        return removedData;
    }

    deleteFromTail() {
        if (this.tail === null) {
            return null;
        }

        if (this.head === this.tail) {
            const removedData = this.tail.data;
            this.head = null;
            this.tail = null;
            this.length--;
            return removedData;
        }

        let current = this.head;
        while (current.next !== this.tail) {
            current = current.next;
        }

        const removedData = this.tail.data;
        current.next = null;
        this.tail = current;
        this.length--;
        return removedData;
    }

    deleteAtPosition(position) {
        if (position < 0 || position >= this.length) {
            throw new Error('Position out of bounds');
        }

        if (position === 0) {
            return this.deleteFromHead();
        }

        let current = this.head;
        for (let i = 0; i < position - 1; i++) {
            current = current.next;
        }

        const removedNode = current.next;
        current.next = removedNode.next;

        if (removedNode === this.tail) {
            this.tail = current;
        }

        this.length--;
        return removedNode.data;
    }

    search(value) {
        let current = this.head;
        let position = 0;

        while (current !== null) {
            if (current.data === value) {
                return position;
            }
            current = current.next;
            position++;
        }

        return -1;
    }

    traverse() {
        const result = [];
        let current = this.head;

        while (current !== null) {
            result.push(current.data);
            current = current.next;
        }

        return result;
    }

    reverse() {
        let previous = null;
        let current = this.head;
        this.tail = this.head;

        while (current !== null) {
            const next = current.next;
            current.next = previous;
            previous = current;
            current = next;
        }

        this.head = previous;
    }
}
```

## Doubly linked lists

A **doubly linked list** extends the singly linked list by adding a second reference in each node. Each node points to both the next node and the previous node.

### Node structure for doubly linked lists

```javascript
class DoublyNode {
    constructor(data) {
        this.data = data;
        this.next = null;
        this.prev = null;
    }
}
```

The additional `prev` pointer enables traversal in both directions and makes certain operations more efficient.

### Visual representation

Consider the same values 10, 20, and 30 in a doubly linked list:

```
null <- [prev | 10 | next] <-> [prev | 20 | next] <-> [prev | 30 | next] -> null
              ^                                              ^
             head                                           tail
```

Each node has two arrows: one pointing forward to `next` and one pointing backward to `prev`. The head's `prev` is `null`, and the tail's `next` is `null`.

### Advantages over singly linked lists

The extra `prev` pointer provides several benefits:

**Bidirectional traversal:** You can traverse the list in either direction. Starting from the tail, you can efficiently walk backward to the head.

**O(1) tail deletion:** With access to `prev`, you can delete the tail node without traversing the entire list. You simply update `tail.prev.next` to `null`.

**Easier deletion with a node reference:** If you have a reference to a node in the middle of the list, you can delete it in O(1) time without needing to traverse from the head to find the previous node.

### Trade-offs

The `prev` pointer comes with costs:

**Increased memory usage:** Each node requires an additional pointer. For lists with many nodes storing small data, this overhead can be significant.

**More complex insertion and deletion:** You must update more pointers during modifications, increasing the chance of bugs.

**Slightly slower modifications:** The extra pointer updates add constant-time overhead to each operation.

### Doubly linked list operations

Here is a complete implementation:

```javascript
class DoublyNode {
    constructor(data) {
        this.data = data;
        this.next = null;
        this.prev = null;
    }
}

class DoublyLinkedList {
    constructor() {
        this.head = null;
        this.tail = null;
        this.length = 0;
    }

    isEmpty() {
        return this.length === 0;
    }

    insertAtHead(data) {
        const newNode = new DoublyNode(data);

        if (this.head === null) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            newNode.next = this.head;
            this.head.prev = newNode;
            this.head = newNode;
        }

        this.length++;
    }

    insertAtTail(data) {
        const newNode = new DoublyNode(data);

        if (this.tail === null) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            newNode.prev = this.tail;
            this.tail.next = newNode;
            this.tail = newNode;
        }

        this.length++;
    }

    insertAtPosition(data, position) {
        if (position < 0 || position > this.length) {
            throw new Error('Position out of bounds');
        }

        if (position === 0) {
            return this.insertAtHead(data);
        }

        if (position === this.length) {
            return this.insertAtTail(data);
        }

        const newNode = new DoublyNode(data);
        let current;

        // Optimize: start from head or tail depending on position
        if (position < this.length / 2) {
            current = this.head;
            for (let i = 0; i < position; i++) {
                current = current.next;
            }
        } else {
            current = this.tail;
            for (let i = this.length - 1; i > position; i--) {
                current = current.prev;
            }
        }

        // Insert before current
        newNode.next = current;
        newNode.prev = current.prev;
        current.prev.next = newNode;
        current.prev = newNode;

        this.length++;
    }

    deleteFromHead() {
        if (this.head === null) {
            return null;
        }

        const removedData = this.head.data;

        if (this.head === this.tail) {
            this.head = null;
            this.tail = null;
        } else {
            this.head = this.head.next;
            this.head.prev = null;
        }

        this.length--;
        return removedData;
    }

    deleteFromTail() {
        if (this.tail === null) {
            return null;
        }

        const removedData = this.tail.data;

        if (this.head === this.tail) {
            this.head = null;
            this.tail = null;
        } else {
            this.tail = this.tail.prev;
            this.tail.next = null;
        }

        this.length--;
        return removedData;
    }

    deleteAtPosition(position) {
        if (position < 0 || position >= this.length) {
            throw new Error('Position out of bounds');
        }

        if (position === 0) {
            return this.deleteFromHead();
        }

        if (position === this.length - 1) {
            return this.deleteFromTail();
        }

        let current;

        if (position < this.length / 2) {
            current = this.head;
            for (let i = 0; i < position; i++) {
                current = current.next;
            }
        } else {
            current = this.tail;
            for (let i = this.length - 1; i > position; i--) {
                current = current.prev;
            }
        }

        // Remove current node
        current.prev.next = current.next;
        current.next.prev = current.prev;

        this.length--;
        return current.data;
    }

    deleteNode(node) {
        // O(1) deletion when you have a direct reference to the node
        if (node === this.head) {
            return this.deleteFromHead();
        }

        if (node === this.tail) {
            return this.deleteFromTail();
        }

        node.prev.next = node.next;
        node.next.prev = node.prev;

        this.length--;
        return node.data;
    }

    search(value) {
        let current = this.head;
        let position = 0;

        while (current !== null) {
            if (current.data === value) {
                return position;
            }
            current = current.next;
            position++;
        }

        return -1;
    }

    traverseForward() {
        const result = [];
        let current = this.head;

        while (current !== null) {
            result.push(current.data);
            current = current.next;
        }

        return result;
    }

    traverseBackward() {
        const result = [];
        let current = this.tail;

        while (current !== null) {
            result.push(current.data);
            current = current.prev;
        }

        return result;
    }
}
```

### Optimized position-based access

Notice how `insertAtPosition` and `deleteAtPosition` optimize traversal by choosing to start from the head or tail based on which is closer to the target position. This cuts the average traversal time in half, though the worst case remains O(n).

## Circular linked lists

A **circular linked list** is a variation where the last node points back to the first node instead of pointing to `null`. This creates a continuous loop with no defined end.

### Singly circular linked list

In a singly circular linked list, the tail's `next` pointer references the head:

```
head -> [10 | next] -> [20 | next] -> [30 | next] --+
         ^                                          |
         +------------------------------------------+
```

### Doubly circular linked list

In a doubly circular linked list, both ends connect:
- The tail's `next` points to the head
- The head's `prev` points to the tail

```
    +--------------------------------------------------+
    |                                                  |
    v                                                  |
[prev | 10 | next] <-> [prev | 20 | next] <-> [prev | 30 | next]
    ^                                                  |
    |                                                  |
    +--------------------------------------------------+
```

### Implementation of singly circular linked list

```javascript
class CircularNode {
    constructor(data) {
        this.data = data;
        this.next = null;
    }
}

class CircularLinkedList {
    constructor() {
        this.head = null;
        this.tail = null;
        this.length = 0;
    }

    isEmpty() {
        return this.length === 0;
    }

    insertAtHead(data) {
        const newNode = new CircularNode(data);

        if (this.head === null) {
            this.head = newNode;
            this.tail = newNode;
            newNode.next = newNode; // Points to itself
        } else {
            newNode.next = this.head;
            this.head = newNode;
            this.tail.next = newNode; // Update tail to point to new head
        }

        this.length++;
    }

    insertAtTail(data) {
        const newNode = new CircularNode(data);

        if (this.tail === null) {
            this.head = newNode;
            this.tail = newNode;
            newNode.next = newNode;
        } else {
            newNode.next = this.head;
            this.tail.next = newNode;
            this.tail = newNode;
        }

        this.length++;
    }

    deleteFromHead() {
        if (this.head === null) {
            return null;
        }

        const removedData = this.head.data;

        if (this.head === this.tail) {
            this.head = null;
            this.tail = null;
        } else {
            this.head = this.head.next;
            this.tail.next = this.head;
        }

        this.length--;
        return removedData;
    }

    deleteFromTail() {
        if (this.tail === null) {
            return null;
        }

        const removedData = this.tail.data;

        if (this.head === this.tail) {
            this.head = null;
            this.tail = null;
        } else {
            let current = this.head;
            while (current.next !== this.tail) {
                current = current.next;
            }
            current.next = this.head;
            this.tail = current;
        }

        this.length--;
        return removedData;
    }

    traverse() {
        if (this.head === null) {
            return [];
        }

        const result = [];
        let current = this.head;

        do {
            result.push(current.data);
            current = current.next;
        } while (current !== this.head);

        return result;
    }

    search(value) {
        if (this.head === null) {
            return -1;
        }

        let current = this.head;
        let position = 0;

        do {
            if (current.data === value) {
                return position;
            }
            current = current.next;
            position++;
        } while (current !== this.head);

        return -1;
    }
}
```

### Key difference in traversal

Notice the traversal uses a `do-while` loop instead of a `while` loop. Since there is no `null` to terminate the loop, you must check whether you have returned to the starting node. Starting with `do-while` ensures you visit the head node before checking the termination condition.

### Use cases for circular linked lists

**Round-robin scheduling:** Operating systems use circular lists to cycle through processes, giving each a time slice before moving to the next.

**Circular buffers:** Audio and video streaming applications use circular buffers to continuously overwrite old data with new data.

**Game turns:** Multiplayer games can use circular lists to cycle through player turns indefinitely.

**Playlist repeat:** Music players implementing repeat functionality can model the playlist as a circular list.

## Time complexity comparison

Understanding the time complexity of each operation helps you choose the right type of linked list.

### Singly linked list

| Operation | Time Complexity | Notes |
|-----------|----------------|-------|
| Insert at head | O(1) | Direct access to head |
| Insert at tail | O(1) | With tail pointer; O(n) without |
| Insert at position | O(n) | Must traverse to position |
| Delete from head | O(1) | Direct access to head |
| Delete from tail | O(n) | Must find second-to-last node |
| Delete at position | O(n) | Must traverse to position |
| Search | O(n) | Must traverse until found |
| Access by index | O(n) | Must traverse to index |

### Doubly linked list

| Operation | Time Complexity | Notes |
|-----------|----------------|-------|
| Insert at head | O(1) | Direct access to head |
| Insert at tail | O(1) | Direct access to tail |
| Insert at position | O(n) | Can start from closer end |
| Delete from head | O(1) | Direct access to head |
| Delete from tail | O(1) | Direct access via prev pointer |
| Delete at position | O(n) | Can start from closer end |
| Delete given node | O(1) | With direct reference to node |
| Search | O(n) | Must traverse until found |
| Access by index | O(n) | Can start from closer end |

### Circular linked list

Time complexities are the same as their non-circular counterparts, with the exception that you can traverse from any node to any other node by continuing in one direction.

## Linked lists vs. arrays

Understanding when to use linked lists versus arrays is crucial for choosing the right data structure.

### When arrays are better

**Random access:** If you frequently access elements by index, arrays provide O(1) access versus O(n) for linked lists.

**Memory efficiency for small elements:** Arrays store only the data. Linked lists add pointer overhead (8 bytes per pointer on 64-bit systems) for each element.

**Cache performance:** Arrays store elements contiguously, which works well with CPU cache prefetching. Linked list nodes may be scattered in memory, causing cache misses.

**Simple iteration:** Iterating through an array is typically faster due to memory locality, even though both are O(n).

### When linked lists are better

**Frequent insertions and deletions at known positions:** If you have a reference to a node and need to insert or delete nearby, linked lists offer O(1) operations versus O(n) for arrays.

**Unknown or highly variable size:** Linked lists grow and shrink naturally without reallocation. Arrays may waste space or require expensive resizing.

**No index-based access needed:** If you only access elements sequentially or through references, linked lists avoid the overhead of maintaining contiguous memory.

**Implementing certain abstract data types:** Stacks and queues can be efficiently implemented with linked lists, especially when memory is fragmented or size is unpredictable.

### Practical decision guide

Ask yourself these questions:

1. **Do I need random access by index?** If yes, use an array.
2. **Do I frequently insert or delete in the middle?** If yes, and you have references to the insertion/deletion points, consider a linked list.
3. **Is my data size predictable?** If yes and fixed, arrays are simpler. If highly variable, linked lists avoid resizing.
4. **Am I memory-constrained with small elements?** If yes, arrays waste less space on overhead.
5. **Is cache performance critical?** If yes, arrays likely perform better for sequential access.

In practice, dynamic arrays (like JavaScript's built-in arrays) are the default choice for most scenarios. They offer a good balance of flexibility and performance. Linked lists shine in specific situations where their O(1) insertion and deletion properties outweigh their access time disadvantages.

## Common linked list patterns and techniques

Several patterns appear frequently when solving problems involving linked lists.

### Two-pointer technique (slow and fast pointers)

The **two-pointer technique** uses two pointers that move at different speeds. This technique solves several problems elegantly.

#### Finding the middle of a list

Move one pointer one step at a time (slow) and another two steps at a time (fast). When the fast pointer reaches the end, the slow pointer is at the middle.

```javascript
function findMiddle(head) {
    if (head === null) {
        return null;
    }

    let slow = head;
    let fast = head;

    while (fast !== null && fast.next !== null) {
        slow = slow.next;
        fast = fast.next.next;
    }

    return slow;
}
```

#### Detecting a cycle

If a linked list has a cycle, a fast pointer will eventually catch up to a slow pointer. If there is no cycle, the fast pointer will reach `null`.

```javascript
function hasCycle(head) {
    if (head === null || head.next === null) {
        return false;
    }

    let slow = head;
    let fast = head;

    while (fast !== null && fast.next !== null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow === fast) {
            return true;
        }
    }

    return false;
}
```

#### Finding the start of a cycle

Once you detect a cycle, you can find where it starts. Reset one pointer to the head and move both pointers one step at a time. They will meet at the cycle's start.

```javascript
function findCycleStart(head) {
    if (head === null || head.next === null) {
        return null;
    }

    let slow = head;
    let fast = head;

    // Detect cycle
    while (fast !== null && fast.next !== null) {
        slow = slow.next;
        fast = fast.next.next;

        if (slow === fast) {
            break;
        }
    }

    // No cycle
    if (fast === null || fast.next === null) {
        return null;
    }

    // Find cycle start
    slow = head;
    while (slow !== fast) {
        slow = slow.next;
        fast = fast.next;
    }

    return slow;
}
```

#### Finding the kth node from the end

Move the first pointer k steps ahead. Then move both pointers one step at a time. When the first pointer reaches the end, the second pointer is k steps from the end.

```javascript
function findKthFromEnd(head, k) {
    let first = head;
    let second = head;

    // Move first pointer k steps ahead
    for (let i = 0; i < k; i++) {
        if (first === null) {
            return null; // List has fewer than k nodes
        }
        first = first.next;
    }

    // Move both pointers until first reaches the end
    while (first !== null) {
        first = first.next;
        second = second.next;
    }

    return second;
}
```

### Reversing a linked list

Reversing a linked list is a fundamental operation that appears in many problems.

#### Iterative approach

```javascript
function reverseList(head) {
    let previous = null;
    let current = head;

    while (current !== null) {
        const next = current.next;
        current.next = previous;
        previous = current;
        current = next;
    }

    return previous; // New head
}
```

#### Recursive approach

```javascript
function reverseListRecursive(head) {
    // Base case: empty list or single node
    if (head === null || head.next === null) {
        return head;
    }

    // Reverse the rest of the list
    const newHead = reverseListRecursive(head.next);

    // Put current node at the end
    head.next.next = head;
    head.next = null;

    return newHead;
}
```

#### Reversing a portion of the list

Sometimes you need to reverse only a portion of the list, from position m to position n.

```javascript
function reverseBetween(head, m, n) {
    if (head === null || m === n) {
        return head;
    }

    // Use a dummy node to simplify edge cases
    const dummy = new Node(0);
    dummy.next = head;

    // Find the node before position m
    let prev = dummy;
    for (let i = 1; i < m; i++) {
        prev = prev.next;
    }

    // Reverse from m to n
    let current = prev.next;
    for (let i = 0; i < n - m; i++) {
        const next = current.next;
        current.next = next.next;
        next.next = prev.next;
        prev.next = next;
    }

    return dummy.next;
}
```

### Using a dummy (sentinel) node

A **dummy node** (also called a sentinel node) is a placeholder node added at the beginning of a list. It simplifies edge cases involving the head.

```javascript
function removeElements(head, val) {
    // Without dummy node, you must handle head removal specially
    // With dummy node, all removals are uniform

    const dummy = new Node(0);
    dummy.next = head;

    let current = dummy;
    while (current.next !== null) {
        if (current.next.data === val) {
            current.next = current.next.next;
        } else {
            current = current.next;
        }
    }

    return dummy.next;
}
```

The dummy node technique is especially useful when:
- The head might be removed
- You need a reference to the node before the first node
- You want to avoid special-casing empty lists

### Merging two sorted lists

Combining two sorted lists into one sorted list is a common operation.

```javascript
function mergeTwoLists(l1, l2) {
    const dummy = new Node(0);
    let tail = dummy;

    while (l1 !== null && l2 !== null) {
        if (l1.data <= l2.data) {
            tail.next = l1;
            l1 = l1.next;
        } else {
            tail.next = l2;
            l2 = l2.next;
        }
        tail = tail.next;
    }

    // Attach remaining nodes
    tail.next = l1 !== null ? l1 : l2;

    return dummy.next;
}
```

**Time complexity:** O(n + m), where n and m are the lengths of the two lists.

### Partitioning a list

Partitioning rearranges nodes so that all nodes with values less than a given value come before nodes with values greater than or equal to that value.

```javascript
function partition(head, x) {
    const beforeHead = new Node(0);
    const afterHead = new Node(0);
    let before = beforeHead;
    let after = afterHead;

    let current = head;
    while (current !== null) {
        if (current.data < x) {
            before.next = current;
            before = before.next;
        } else {
            after.next = current;
            after = after.next;
        }
        current = current.next;
    }

    after.next = null;
    before.next = afterHead.next;

    return beforeHead.next;
}
```

## Real-world applications

Linked lists appear in many real-world systems, often as building blocks for more complex data structures.

### Implementation of other data structures

**Stacks:** A singly linked list with insertion and deletion at the head provides O(1) push and pop operations.

**Queues:** A singly linked list with insertion at the tail and deletion at the head provides O(1) enqueue and dequeue operations.

**Deques:** A doubly linked list allows O(1) insertion and deletion at both ends.

**Hash table chaining:** Hash tables use linked lists to handle collisions. Each bucket contains a linked list of entries that hash to the same index.

### LRU cache

An **LRU (Least Recently Used) cache** combines a hash map with a doubly linked list. The hash map provides O(1) lookup by key. The doubly linked list tracks access order, with the most recently used item at the head and the least recently used at the tail.

```javascript
class LRUCache {
    constructor(capacity) {
        this.capacity = capacity;
        this.cache = new Map();
        this.head = new DoublyNode(null); // Dummy head
        this.tail = new DoublyNode(null); // Dummy tail
        this.head.next = this.tail;
        this.tail.prev = this.head;
    }

    get(key) {
        if (!this.cache.has(key)) {
            return -1;
        }

        const node = this.cache.get(key);
        this.moveToHead(node);
        return node.data.value;
    }

    put(key, value) {
        if (this.cache.has(key)) {
            const node = this.cache.get(key);
            node.data.value = value;
            this.moveToHead(node);
        } else {
            const node = new DoublyNode({ key, value });
            this.cache.set(key, node);
            this.addToHead(node);

            if (this.cache.size > this.capacity) {
                const removed = this.removeTail();
                this.cache.delete(removed.data.key);
            }
        }
    }

    addToHead(node) {
        node.prev = this.head;
        node.next = this.head.next;
        this.head.next.prev = node;
        this.head.next = node;
    }

    removeNode(node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    moveToHead(node) {
        this.removeNode(node);
        this.addToHead(node);
    }

    removeTail() {
        const node = this.tail.prev;
        this.removeNode(node);
        return node;
    }
}
```

This implementation provides O(1) time complexity for both `get` and `put` operations.

### Browser history

Browsers implement navigation history using a data structure similar to a doubly linked list. Each page visit creates a node. The back button follows the `prev` pointer; the forward button follows the `next` pointer. When you navigate to a new page from the middle of history, all forward nodes are discarded.

### Music and media playlists

Media players use linked lists to implement playlists. The play order follows the list sequence. Shuffle creates a random ordering. Repeat can be implemented by making the list circular.

### Undo/redo functionality

Text editors and design tools implement undo/redo using linked lists of states. Each edit creates a new node. Undo moves backward through the list; redo moves forward. This approach works well with a doubly linked list for bidirectional navigation.

### Memory allocation

Operating systems use linked lists to track free memory blocks. When memory is allocated, blocks are removed from the free list. When memory is freed, blocks are added back. This approach, called the **free list**, allows efficient memory management without requiring contiguous free space.

### Polynomial representation

Mathematical software represents polynomials as linked lists where each node contains a coefficient and an exponent. This representation efficiently handles sparse polynomials with many zero coefficients.

## Common pitfalls and how to avoid them

Working with linked lists introduces several common sources of bugs.

### Null pointer exceptions

The most common bug is dereferencing a null pointer. Always check for null before accessing `next` or `prev`.

```javascript
// Wrong: crashes if current is null
while (current.next !== null) { ... }

// Correct: checks current first
while (current !== null && current.next !== null) { ... }
```

### Losing references

When modifying pointers, you can accidentally lose references to nodes. Save references before updating pointers.

```javascript
// Wrong: loses reference to next node
current.next = previous;
current = current.next; // Now points to previous!

// Correct: save next reference first
const next = current.next;
current.next = previous;
current = next;
```

### Forgetting to update all pointers

In doubly linked lists, you must update both `next` and `prev` pointers. Missing one creates an inconsistent state.

```javascript
// Wrong: only updates one direction
newNode.next = current.next;
current.next = newNode;

// Correct: updates both directions
newNode.next = current.next;
newNode.prev = current;
current.next.prev = newNode;
current.next = newNode;
```

### Off-by-one errors in traversal

When traversing to a specific position, carefully count whether you need to stop before or at the target.

```javascript
// To insert AFTER position i, traverse to position i
for (let j = 0; j < i; j++) {
    current = current.next;
}

// To insert BEFORE position i, traverse to position i-1
for (let j = 0; j < i - 1; j++) {
    current = current.next;
}
```

### Infinite loops in circular lists

Circular list traversal must explicitly check for returning to the start. Without this check, you will loop forever.

```javascript
// Wrong: infinite loop
while (current !== null) {
    current = current.next;
}

// Correct: stop when returning to start
do {
    // process current
    current = current.next;
} while (current !== head);
```

### Not handling empty list edge cases

Many operations behave differently on empty lists. Always consider what happens when `head` is null.

```javascript
function traverse(head) {
    // Handle empty list
    if (head === null) {
        return [];
    }

    // Normal traversal
    const result = [];
    let current = head;
    while (current !== null) {
        result.push(current.data);
        current = current.next;
    }
    return result;
}
```

## Summary

Linked lists are fundamental data structures that store elements in nodes connected by references. Unlike arrays, they do not require contiguous memory, which gives them unique trade-offs in terms of performance and flexibility.

Key takeaways:

- **Singly linked lists** use one pointer per node and support O(1) insertion and deletion at the head. They require O(n) traversal to access elements by position or to delete from the tail.

- **Doubly linked lists** add a backward pointer, enabling O(1) deletion from the tail and O(1) deletion of any node when you have a direct reference. They use more memory and require more careful pointer management.

- **Circular linked lists** connect the tail to the head, creating a continuous loop. They are useful for round-robin scheduling, circular buffers, and any scenario requiring cyclic iteration.

- The **two-pointer technique** solves many linked list problems elegantly, including finding the middle, detecting cycles, and finding nodes from the end.

- **Dummy nodes** simplify edge cases involving the head by providing a consistent node before the first real element.

- Linked lists excel at **frequent insertions and deletions at known positions** but perform poorly for random access. Arrays are better when you need index-based access or cache-friendly sequential traversal.

- Real-world applications include implementing stacks, queues, hash table chaining, LRU caches, browser history, and memory allocation.

- Common pitfalls include null pointer exceptions, losing references during pointer updates, forgetting to update all pointers in doubly linked lists, and infinite loops in circular lists.

Understanding linked lists gives you a foundation for more complex data structures. Many trees, graphs, and associative containers are built on the same node-and-pointer concepts you have learned here.
