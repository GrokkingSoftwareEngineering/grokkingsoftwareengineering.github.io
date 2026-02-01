# Queues

Reading time: 25 minutes

A **queue** is a linear data structure that follows the First In, First Out (FIFO) principle. Elements enter at one end (the back) and exit from the other end (the front), just like a line of people waiting at a checkout counter. The person who arrives first gets served first, and new arrivals join at the back.

Queues are everywhere in computing: operating systems use them to schedule tasks, web servers use them to handle incoming requests, and printers use them to manage print jobs. Understanding queues deeply helps you recognize when they are the right tool and how to implement them efficiently. This page covers queue operations, multiple implementation strategies, specialized variants like circular queues and deques, and real-world applications where queues excel.

## The FIFO principle

The **First In, First Out** principle is the defining characteristic of a queue. Unlike a stack where the most recent item comes out first, a queue processes items in the order they arrived.

Consider a print queue. When you send three documents to a printer:
1. Document A arrives first and enters the queue
2. Document B arrives second and joins behind A
3. Document C arrives third and joins behind B

The printer processes them in that exact order: A, then B, then C. Document C cannot jump ahead no matter how small it is or how urgently you want it.

This ordering matters because it provides **fairness**. Every item eventually gets processed, and the wait time is proportional to how many items arrived before it. This property makes queues essential for any system where order of arrival determines order of service.

### Contrast with stacks

Stacks follow Last In, First Out (LIFO). If you placed the same three documents on a stack, document C (the last one added) would come out first. This is useful when you need to reverse order or track nested state, but it violates fairness when items are waiting to be served.

Think of the difference this way:
- **Stack:** A stack of plates. You add and remove from the top only.
- **Queue:** A line at a ticket counter. You join at the back and leave from the front.

When you need fairness and ordering by arrival time, use a queue. When you need to reverse order or process the most recent item first, use a stack.

## Queue operations

A queue supports a small set of core operations. Understanding these operations and their expected behavior is fundamental to using queues correctly.

### Enqueue (add to back)

**Enqueue** adds an element to the back of the queue. This is also called "push," "add," or "offer" in various implementations.

After enqueueing, the new element becomes the last one that will be processed. All elements already in the queue are still ahead of it.

```javascript
// Conceptual representation
queue: [A, B, C]  // front is left, back is right
enqueue(D)
queue: [A, B, C, D]  // D is now at the back
```

### Dequeue (remove from front)

**Dequeue** removes and returns the element at the front of the queue. This is also called "pop," "remove," or "poll" in various implementations.

After dequeueing, the next element in line becomes the new front. The removed element is no longer part of the queue.

```javascript
// Conceptual representation
queue: [A, B, C, D]
element = dequeue()  // returns A
queue: [B, C, D]  // B is now at the front
```

### Peek (view front without removing)

**Peek** returns the front element without removing it. This is also called "front," "head," or "element" in various implementations.

Peek lets you inspect what will be dequeued next without changing the queue's state. This is useful when you need to make decisions based on the next item before committing to process it.

```javascript
// Conceptual representation
queue: [A, B, C, D]
element = peek()  // returns A
queue: [A, B, C, D]  // unchanged
```

### isEmpty (check if empty)

**isEmpty** returns true if the queue contains no elements, false otherwise. Some implementations provide a **size** or **length** property instead, where you check if the size equals zero.

Checking emptiness before dequeuing prevents errors. Attempting to dequeue from an empty queue is an error condition that different implementations handle differently (throwing an exception, returning null, or returning undefined).

```javascript
// Conceptual representation
queue: []
isEmpty()  // returns true

queue: [A]
isEmpty()  // returns false
```

### Size (count elements)

**Size** returns the number of elements currently in the queue. This operation should be constant time, meaning the queue tracks its count rather than recounting on every call.

```javascript
// Conceptual representation
queue: [A, B, C]
size()  // returns 3
```

## Array-based implementation

The simplest way to implement a queue uses an array. You track where the front and back are, and the array holds the elements in between.

### Naive approach and its problem

The most straightforward approach uses an array where index 0 is always the front:

```javascript
class NaiveQueue {
  constructor() {
    this.items = [];
  }

  enqueue(element) {
    this.items.push(element);  // O(1) amortized
  }

  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items.shift();  // O(n) - shifts all elements
  }

  peek() {
    return this.items[0];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  size() {
    return this.items.length;
  }
}
```

This works correctly but has a performance problem. The `shift()` operation removes the first element and shifts all remaining elements one position to the left. For a queue with n elements, dequeue takes O(n) time.

If you enqueue and dequeue 1,000,000 items, each dequeue shifts up to 1,000,000 elements. This adds up quickly.

### Using an index pointer

A better approach uses a front index pointer instead of shifting:

```javascript
class IndexedQueue {
  constructor() {
    this.items = [];
    this.front = 0;
  }

  enqueue(element) {
    this.items.push(element);
  }

  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }
    const element = this.items[this.front];
    this.front++;
    return element;
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.front];
  }

  isEmpty() {
    return this.front >= this.items.length;
  }

  size() {
    return this.items.length - this.front;
  }
}
```

Now dequeue is O(1) because you just increment the front pointer instead of shifting elements. The trade-off is that dequeued elements remain in the array, wasting memory.

### Memory cleanup

The indexed approach leaks memory because dequeued elements stay in the array. You can periodically clean up:

```javascript
class CleaningQueue {
  constructor() {
    this.items = [];
    this.front = 0;
  }

  enqueue(element) {
    this.items.push(element);
  }

  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }
    const element = this.items[this.front];
    this.items[this.front] = undefined;  // Allow garbage collection
    this.front++;

    // Compact when half the array is wasted
    if (this.front > this.items.length / 2) {
      this.items = this.items.slice(this.front);
      this.front = 0;
    }

    return element;
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.front];
  }

  isEmpty() {
    return this.front >= this.items.length;
  }

  size() {
    return this.items.length - this.front;
  }
}
```

The cleanup happens when half the array slots are wasted. This keeps memory usage bounded while making dequeue O(1) amortized (the occasional compaction costs O(n), but it happens infrequently enough that the average cost per operation is constant).

### Time complexity summary for array implementation

| Operation | Naive (shift) | Indexed | With cleanup |
|-----------|---------------|---------|--------------|
| enqueue   | O(1) amortized | O(1) amortized | O(1) amortized |
| dequeue   | O(n) | O(1) | O(1) amortized |
| peek      | O(1) | O(1) | O(1) |
| isEmpty   | O(1) | O(1) | O(1) |
| size      | O(1) | O(1) | O(1) |

## Linked list implementation

A linked list naturally supports queue operations with O(1) time for all operations without any cleanup logic.

### Singly linked list queue

With a singly linked list, you maintain pointers to both the head (front) and tail (back):

```javascript
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
}

class LinkedListQueue {
  constructor() {
    this.head = null;  // front of queue
    this.tail = null;  // back of queue
    this.length = 0;
  }

  enqueue(element) {
    const newNode = new Node(element);

    if (this.isEmpty()) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      this.tail.next = newNode;
      this.tail = newNode;
    }

    this.length++;
  }

  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }

    const element = this.head.value;
    this.head = this.head.next;
    this.length--;

    if (this.isEmpty()) {
      this.tail = null;  // Queue is now empty
    }

    return element;
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.head.value;
  }

  isEmpty() {
    return this.length === 0;
  }

  size() {
    return this.length;
  }
}
```

### How it works

Enqueue adds to the tail:
1. Create a new node
2. If the queue is empty, both head and tail point to the new node
3. Otherwise, link the current tail to the new node and update tail

Dequeue removes from the head:
1. Save the head's value
2. Move head to the next node
3. If the queue is now empty, also clear tail
4. Return the saved value

Both operations update a constant number of pointers regardless of queue size.

### Time complexity for linked list implementation

| Operation | Time complexity |
|-----------|-----------------|
| enqueue   | O(1) |
| dequeue   | O(1) |
| peek      | O(1) |
| isEmpty   | O(1) |
| size      | O(1) |

### Trade-offs between array and linked list

**Array advantages:**
- Better memory locality (elements are contiguous in memory)
- Less memory overhead per element (no pointers)
- Faster in practice for small queues due to cache efficiency

**Linked list advantages:**
- True O(1) operations without amortization
- No wasted space from dequeued elements
- No need for periodic cleanup or resizing logic
- Memory usage always proportional to current size

**When to use each:**
- Use arrays for small, performance-critical queues where cache efficiency matters
- Use linked lists when you need guaranteed O(1) operations or when queue size varies dramatically
- In languages with efficient built-in arrays (like JavaScript), the indexed array approach often performs well enough

## Circular queue

A **circular queue** (also called a **ring buffer**) solves the memory waste problem of indexed arrays without needing cleanup. It treats the array as circular, wrapping around from the end back to the beginning.

### The concept

Imagine the array as a circle rather than a line. When you reach the end, you wrap around to the beginning. This lets you reuse slots that were freed by dequeue operations.

```
Linear view:   [_, _, C, D, E, _, _]
                      ^        ^
                    front     back

Circular view:     C
                 /   \
               D       B (future)
                 \   /
                   E
```

### Fixed-size implementation

A circular queue requires a fixed capacity because the circular indexing depends on knowing where to wrap:

```javascript
class CircularQueue {
  constructor(capacity) {
    this.capacity = capacity;
    this.items = new Array(capacity);
    this.front = 0;
    this.back = 0;
    this.length = 0;
  }

  enqueue(element) {
    if (this.isFull()) {
      return false;  // Queue is full
    }

    this.items[this.back] = element;
    this.back = (this.back + 1) % this.capacity;
    this.length++;
    return true;
  }

  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }

    const element = this.items[this.front];
    this.items[this.front] = undefined;  // Optional: help garbage collection
    this.front = (this.front + 1) % this.capacity;
    this.length--;
    return element;
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.front];
  }

  isEmpty() {
    return this.length === 0;
  }

  isFull() {
    return this.length === this.capacity;
  }

  size() {
    return this.length;
  }
}
```

### How wrapping works

The key insight is the modulo operation: `(index + 1) % capacity`

- If `back` is at position 4 in a capacity-5 queue, `(4 + 1) % 5 = 0`, wrapping back to the start
- If `back` is at position 2, `(2 + 1) % 5 = 3`, normal increment

This arithmetic handles all cases uniformly without special conditionals for the end of the array.

### Distinguishing full from empty

A naive circular queue using only front and back pointers cannot distinguish full from empty (both have `front === back`). There are three common solutions:

1. **Track length separately** (shown above): Add a length counter. Simple and clear.

2. **Waste one slot:** Never let the queue completely fill. Full means `(back + 1) % capacity === front`. This wastes one slot but avoids tracking length.

3. **Use a flag:** Add a boolean that tracks whether the last operation was enqueue or dequeue. If `front === back` and last operation was enqueue, the queue is full; if dequeue, it is empty.

The length-tracking approach is recommended for clarity.

### Dynamic resizing

You can make a circular queue resizable, though it requires copying elements:

```javascript
class DynamicCircularQueue {
  constructor(initialCapacity = 8) {
    this.capacity = initialCapacity;
    this.items = new Array(initialCapacity);
    this.front = 0;
    this.back = 0;
    this.length = 0;
  }

  enqueue(element) {
    if (this.length === this.capacity) {
      this.resize(this.capacity * 2);
    }

    this.items[this.back] = element;
    this.back = (this.back + 1) % this.capacity;
    this.length++;
  }

  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }

    const element = this.items[this.front];
    this.items[this.front] = undefined;
    this.front = (this.front + 1) % this.capacity;
    this.length--;

    // Shrink if too empty (optional)
    if (this.length > 0 && this.length === this.capacity / 4) {
      this.resize(this.capacity / 2);
    }

    return element;
  }

  resize(newCapacity) {
    const newItems = new Array(newCapacity);

    // Copy elements in order from front to back
    for (let i = 0; i < this.length; i++) {
      newItems[i] = this.items[(this.front + i) % this.capacity];
    }

    this.items = newItems;
    this.front = 0;
    this.back = this.length;
    this.capacity = newCapacity;
  }

  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.front];
  }

  isEmpty() {
    return this.length === 0;
  }

  size() {
    return this.length;
  }
}
```

### Applications of circular queues

Circular queues excel when you have a bounded buffer:

- **Audio/video streaming:** Buffer incoming data, consume at playback rate
- **Keyboard buffers:** Store keystrokes until the system processes them
- **Network packet buffers:** Hold incoming packets until the application reads them
- **Producer-consumer patterns:** Fixed-size buffer between producer and consumer threads
- **Sliding window algorithms:** Keep the last N items for moving averages or rate limiting

The fixed capacity is often a feature, not a limitation. It prevents unbounded memory growth and provides backpressure when producers outpace consumers.

## Deque (double-ended queue)

A **deque** (pronounced "deck") supports insertion and deletion at both ends. It generalizes both stacks and queues: you can use it as a stack (add/remove from one end), a queue (add to one end, remove from the other), or something in between.

### Operations

A deque supports six core operations:

| Operation | Description |
|-----------|-------------|
| addFront  | Insert at the front |
| addBack   | Insert at the back |
| removeFront | Remove from the front |
| removeBack | Remove from the back |
| peekFront | View front without removing |
| peekBack | View back without removing |

### Array-based implementation

```javascript
class ArrayDeque {
  constructor() {
    this.items = [];
  }

  addFront(element) {
    this.items.unshift(element);  // O(n) - shifts all elements
  }

  addBack(element) {
    this.items.push(element);  // O(1) amortized
  }

  removeFront() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items.shift();  // O(n) - shifts all elements
  }

  removeBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items.pop();  // O(1)
  }

  peekFront() {
    return this.items[0];
  }

  peekBack() {
    return this.items[this.items.length - 1];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  size() {
    return this.items.length;
  }
}
```

This simple implementation has O(n) operations at the front due to shifting. For better performance, use a circular buffer or linked list.

### Doubly linked list implementation

A doubly linked list provides O(1) operations at both ends:

```javascript
class DequeNode {
  constructor(value) {
    this.value = value;
    this.prev = null;
    this.next = null;
  }
}

class LinkedDeque {
  constructor() {
    this.head = null;  // front
    this.tail = null;  // back
    this.length = 0;
  }

  addFront(element) {
    const newNode = new DequeNode(element);

    if (this.isEmpty()) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      newNode.next = this.head;
      this.head.prev = newNode;
      this.head = newNode;
    }

    this.length++;
  }

  addBack(element) {
    const newNode = new DequeNode(element);

    if (this.isEmpty()) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      newNode.prev = this.tail;
      this.tail.next = newNode;
      this.tail = newNode;
    }

    this.length++;
  }

  removeFront() {
    if (this.isEmpty()) {
      return undefined;
    }

    const element = this.head.value;
    this.head = this.head.next;

    if (this.head === null) {
      this.tail = null;
    } else {
      this.head.prev = null;
    }

    this.length--;
    return element;
  }

  removeBack() {
    if (this.isEmpty()) {
      return undefined;
    }

    const element = this.tail.value;
    this.tail = this.tail.prev;

    if (this.tail === null) {
      this.head = null;
    } else {
      this.tail.next = null;
    }

    this.length--;
    return element;
  }

  peekFront() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.head.value;
  }

  peekBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.tail.value;
  }

  isEmpty() {
    return this.length === 0;
  }

  size() {
    return this.length;
  }
}
```

### Time complexity for deque implementations

| Operation | Array (naive) | Doubly linked list |
|-----------|---------------|-------------------|
| addFront | O(n) | O(1) |
| addBack | O(1) amortized | O(1) |
| removeFront | O(n) | O(1) |
| removeBack | O(1) | O(1) |
| peekFront | O(1) | O(1) |
| peekBack | O(1) | O(1) |

### Applications of deques

Deques are useful when you need flexibility about which end to use:

- **Sliding window maximum/minimum:** Keep a deque of useful elements, adding at back and removing from either end as the window slides
- **Work-stealing algorithms:** Threads add to their own deque's back and steal from other deques' fronts
- **Undo/redo with limited history:** Remove oldest entries from one end when the history limit is reached
- **Palindrome checking:** Add characters to deque, then compare front and back as you remove from both ends

## Priority queue introduction

A **priority queue** is a queue where elements have priorities, and dequeue always returns the element with the highest (or lowest) priority, regardless of insertion order.

This is not a FIFO structure. If you enqueue items A, B, C and B has the highest priority, dequeue returns B first, not A. Priority queues are covered in depth in the Heaps page, but understanding them relative to standard queues is valuable here.

### Comparison with standard queues

| Aspect | Standard queue | Priority queue |
|--------|---------------|----------------|
| Ordering | Insertion order (FIFO) | Priority order |
| Dequeue returns | Oldest element | Highest priority element |
| Use case | Fairness, ordering | Urgency, importance |

### Basic interface

```javascript
// Conceptual interface (not a full implementation)
class PriorityQueue {
  enqueue(element, priority) { }  // Add with priority
  dequeue() { }                    // Remove highest priority
  peek() { }                       // View highest priority
  isEmpty() { }
  size() { }
}
```

### Implementation approaches

- **Sorted array:** Keep elements sorted by priority. Enqueue is O(n) to find the right position; dequeue is O(1).
- **Unsorted array:** Append on enqueue in O(1); scan for highest priority on dequeue in O(n).
- **Heap (recommended):** A binary heap provides O(log n) for both enqueue and dequeue. This is the standard implementation.

### Common applications

- **Task scheduling:** Process high-priority tasks before low-priority ones
- **Event simulation:** Process events in time order (priority = event time)
- **Dijkstra's algorithm:** Always expand the node with the smallest distance
- **Huffman coding:** Build trees by repeatedly combining the two lowest-frequency nodes
- **A* pathfinding:** Expand nodes with the lowest estimated total cost

Priority queues are essential for algorithms where you always need "the best" item next, not just "the oldest" item.

## Time complexity summary

Here is a comprehensive summary of time complexities for all queue variants:

### Standard queue

| Implementation | enqueue | dequeue | peek | isEmpty |
|----------------|---------|---------|------|---------|
| Array (shift) | O(1)* | O(n) | O(1) | O(1) |
| Array (indexed) | O(1)* | O(1) | O(1) | O(1) |
| Linked list | O(1) | O(1) | O(1) | O(1) |
| Circular (fixed) | O(1) | O(1) | O(1) | O(1) |
| Circular (dynamic) | O(1)* | O(1)* | O(1) | O(1) |

*Amortized time; occasional operations take longer due to resizing.

### Deque

| Implementation | addFront | addBack | removeFront | removeBack |
|----------------|----------|---------|-------------|------------|
| Array (naive) | O(n) | O(1)* | O(n) | O(1) |
| Doubly linked list | O(1) | O(1) | O(1) | O(1) |
| Circular buffer | O(1)* | O(1)* | O(1)* | O(1)* |

### Priority queue

| Implementation | enqueue | dequeue | peek |
|----------------|---------|---------|------|
| Sorted array | O(n) | O(1) | O(1) |
| Unsorted array | O(1) | O(n) | O(n) |
| Binary heap | O(log n) | O(log n) | O(1) |

## Common applications

Queues appear throughout computer science and software engineering. Understanding these applications helps you recognize when a queue is the right choice.

### Breadth-first search (BFS)

BFS explores a graph level by level, visiting all neighbors of the current node before moving to neighbors' neighbors. A queue ensures you process nodes in the order they were discovered.

```javascript
function bfs(graph, startNode) {
  const visited = new Set();
  const queue = [startNode];
  const result = [];

  visited.add(startNode);

  while (queue.length > 0) {
    const current = queue.shift();
    result.push(current);

    for (const neighbor of graph[current] || []) {
      if (!visited.has(neighbor)) {
        visited.add(neighbor);
        queue.push(neighbor);
      }
    }
  }

  return result;
}

// Example graph as adjacency list
const graph = {
  'A': ['B', 'C'],
  'B': ['A', 'D', 'E'],
  'C': ['A', 'F'],
  'D': ['B'],
  'E': ['B', 'F'],
  'F': ['C', 'E']
};

console.log(bfs(graph, 'A'));  // ['A', 'B', 'C', 'D', 'E', 'F']
```

BFS is used for:
- Finding shortest paths in unweighted graphs
- Level-order traversal of trees
- Finding all nodes within a certain distance
- Web crawlers (visit pages in discovery order)

### Task scheduling

Operating systems use queues to manage processes waiting for CPU time. Each process joins the queue when it needs the CPU and waits its turn.

```javascript
class TaskScheduler {
  constructor() {
    this.taskQueue = [];
  }

  addTask(task) {
    this.taskQueue.push(task);
    console.log(`Task "${task.name}" added to queue`);
  }

  processNext() {
    if (this.taskQueue.length === 0) {
      console.log('No tasks to process');
      return null;
    }

    const task = this.taskQueue.shift();
    console.log(`Processing task "${task.name}"`);
    task.execute();
    return task;
  }

  processAll() {
    while (this.taskQueue.length > 0) {
      this.processNext();
    }
  }
}

// Usage
const scheduler = new TaskScheduler();
scheduler.addTask({ name: 'Send email', execute: () => console.log('Email sent') });
scheduler.addTask({ name: 'Update database', execute: () => console.log('Database updated') });
scheduler.addTask({ name: 'Generate report', execute: () => console.log('Report generated') });

scheduler.processAll();
```

This pattern appears in:
- Print spoolers
- Message queues (RabbitMQ, Kafka)
- Job schedulers (cron jobs, background workers)
- Event loops (JavaScript runtime)

### Buffering

Queues act as buffers between components that operate at different speeds. The producer adds items to the queue, and the consumer removes them when ready.

```javascript
class DataBuffer {
  constructor(maxSize) {
    this.buffer = [];
    this.maxSize = maxSize;
  }

  write(data) {
    if (this.buffer.length >= this.maxSize) {
      console.log('Buffer full, waiting...');
      return false;
    }
    this.buffer.push(data);
    return true;
  }

  read() {
    if (this.buffer.length === 0) {
      return null;  // Buffer empty
    }
    return this.buffer.shift();
  }

  available() {
    return this.buffer.length;
  }
}

// Simulating producer/consumer
const buffer = new DataBuffer(5);

// Producer writes faster than consumer reads
buffer.write('packet1');
buffer.write('packet2');
buffer.write('packet3');

// Consumer reads when ready
console.log(buffer.read());  // packet1
console.log(buffer.read());  // packet2
```

Buffering with queues appears in:
- Network I/O (TCP receive buffers)
- Audio/video streaming (playback buffers)
- Keyboard input (type-ahead buffer)
- Inter-process communication (pipes)

### Rate limiting and throttling

Queues help implement rate limiting by controlling how fast requests are processed:

```javascript
class RateLimiter {
  constructor(maxRequestsPerSecond) {
    this.requestQueue = [];
    this.maxRequestsPerSecond = maxRequestsPerSecond;
    this.processing = false;
  }

  async addRequest(request) {
    return new Promise((resolve) => {
      this.requestQueue.push({ request, resolve });
      this.processQueue();
    });
  }

  async processQueue() {
    if (this.processing || this.requestQueue.length === 0) {
      return;
    }

    this.processing = true;

    while (this.requestQueue.length > 0) {
      const { request, resolve } = this.requestQueue.shift();
      const result = await this.executeRequest(request);
      resolve(result);

      // Wait to maintain rate limit
      await this.delay(1000 / this.maxRequestsPerSecond);
    }

    this.processing = false;
  }

  async executeRequest(request) {
    console.log(`Executing: ${request}`);
    return `Result of ${request}`;
  }

  delay(ms) {
    return new Promise(resolve => setTimeout(resolve, ms));
  }
}

// Usage: max 2 requests per second
const limiter = new RateLimiter(2);
limiter.addRequest('Request 1');
limiter.addRequest('Request 2');
limiter.addRequest('Request 3');
```

### Tree level-order traversal

BFS on a tree gives you level-order traversal, visiting all nodes at depth 0, then depth 1, and so on:

```javascript
class TreeNode {
  constructor(value) {
    this.value = value;
    this.left = null;
    this.right = null;
  }
}

function levelOrderTraversal(root) {
  if (root === null) {
    return [];
  }

  const result = [];
  const queue = [root];

  while (queue.length > 0) {
    const current = queue.shift();
    result.push(current.value);

    if (current.left !== null) {
      queue.push(current.left);
    }
    if (current.right !== null) {
      queue.push(current.right);
    }
  }

  return result;
}

// Build a sample tree:
//        1
//       / \
//      2   3
//     / \   \
//    4   5   6

const root = new TreeNode(1);
root.left = new TreeNode(2);
root.right = new TreeNode(3);
root.left.left = new TreeNode(4);
root.left.right = new TreeNode(5);
root.right.right = new TreeNode(6);

console.log(levelOrderTraversal(root));  // [1, 2, 3, 4, 5, 6]
```

### Cache eviction (LRU)

A queue helps track access order for Least Recently Used (LRU) cache eviction:

```javascript
class LRUCache {
  constructor(capacity) {
    this.capacity = capacity;
    this.cache = new Map();  // Map maintains insertion order in JavaScript
  }

  get(key) {
    if (!this.cache.has(key)) {
      return -1;
    }

    // Move to end (most recently used)
    const value = this.cache.get(key);
    this.cache.delete(key);
    this.cache.set(key, value);
    return value;
  }

  put(key, value) {
    if (this.cache.has(key)) {
      this.cache.delete(key);
    } else if (this.cache.size >= this.capacity) {
      // Remove oldest (first key in map)
      const oldestKey = this.cache.keys().next().value;
      this.cache.delete(oldestKey);
    }

    this.cache.set(key, value);
  }
}

const cache = new LRUCache(3);
cache.put('a', 1);
cache.put('b', 2);
cache.put('c', 3);
console.log(cache.get('a'));  // 1 (moves 'a' to end)
cache.put('d', 4);            // Evicts 'b' (oldest)
console.log(cache.get('b'));  // -1 (not found)
```

## Edge cases and common pitfalls

Working with queues involves several edge cases you should handle carefully.

### Empty queue operations

Always check for empty before dequeue or peek:

```javascript
function safeDequeue(queue) {
  if (queue.isEmpty()) {
    throw new Error('Cannot dequeue from empty queue');
    // Or return undefined, null, or a sentinel value
  }
  return queue.dequeue();
}
```

Different libraries handle this differently:
- Some throw exceptions
- Some return null or undefined
- Some return a special "empty" value

Know what your implementation does and handle it consistently.

### Single element queue

A queue with one element has the same element at front and back. After dequeueing, both front and back become invalid:

```javascript
// In linked list implementation
dequeue() {
  // ...
  this.head = this.head.next;

  if (this.head === null) {
    this.tail = null;  // Don't forget this!
  }
  // ...
}
```

Forgetting to update tail when the queue becomes empty is a common bug.

### Circular queue boundary conditions

With circular queues, test these cases specifically:
- Enqueue into a full queue
- Dequeue from an empty queue
- Fill the queue completely, then empty it completely, then fill again
- Operations when front > back (wrapped around)

```javascript
// Test case: wrap-around behavior
const cq = new CircularQueue(3);
cq.enqueue('A');  // [A, _, _], front=0, back=1
cq.enqueue('B');  // [A, B, _], front=0, back=2
cq.dequeue();     // [_, B, _], front=1, back=2
cq.enqueue('C');  // [_, B, C], front=1, back=0 (wrapped)
cq.enqueue('D');  // [D, B, C], front=1, back=1 (full)
```

### Memory leaks in array implementations

When using arrays with index tracking, dequeued elements remain in memory:

```javascript
// Memory leak
dequeue() {
  const element = this.items[this.front];
  this.front++;  // Element still in array!
  return element;
}

// Fixed
dequeue() {
  const element = this.items[this.front];
  this.items[this.front] = undefined;  // Allow garbage collection
  this.front++;
  return element;
}
```

### Concurrent access

If multiple threads or async operations access the same queue, you need synchronization:

```javascript
// Problem: race condition
// Thread 1: if (!queue.isEmpty())
// Thread 2: queue.dequeue()  // Takes the last element
// Thread 1: queue.dequeue()  // Fails unexpectedly

// Solution: atomic check-and-dequeue
function safeDequeue(queue, lock) {
  lock.acquire();
  try {
    if (queue.isEmpty()) {
      return null;
    }
    return queue.dequeue();
  } finally {
    lock.release();
  }
}
```

In JavaScript, single-threaded execution avoids most of these issues, but be careful with async operations that interleave.

### Maximum size constraints

For bounded queues, decide how to handle overflow:

```javascript
class BoundedQueue {
  constructor(maxSize) {
    this.items = [];
    this.maxSize = maxSize;
  }

  enqueue(element) {
    if (this.items.length >= this.maxSize) {
      // Option 1: Reject
      return false;

      // Option 2: Remove oldest
      // this.items.shift();

      // Option 3: Throw
      // throw new Error('Queue full');
    }
    this.items.push(element);
    return true;
  }
}
```

Choose the behavior that matches your use case:
- **Reject:** When losing new items is acceptable (rate limiting)
- **Remove oldest:** When old items become stale (streaming data)
- **Throw:** When overflow indicates a bug that should be fixed

## Using JavaScript's built-in structures

JavaScript arrays work as queues out of the box, though with performance trade-offs.

### Array as queue

```javascript
const queue = [];

// Enqueue
queue.push('A');
queue.push('B');
queue.push('C');

// Dequeue
console.log(queue.shift());  // 'A'
console.log(queue.shift());  // 'B'

// Peek
console.log(queue[0]);  // 'C'

// Check empty
console.log(queue.length === 0);  // false
```

This is simple but `shift()` is O(n).

### Map as ordered queue

JavaScript's Map maintains insertion order, which you can exploit:

```javascript
const queue = new Map();
let nextId = 0;

// Enqueue
function enqueue(value) {
  queue.set(nextId++, value);
}

// Dequeue
function dequeue() {
  const firstKey = queue.keys().next().value;
  if (firstKey === undefined) return undefined;
  const value = queue.get(firstKey);
  queue.delete(firstKey);
  return value;
}

enqueue('A');
enqueue('B');
console.log(dequeue());  // 'A'
```

This has O(1) amortized complexity for both operations in most JavaScript engines.

### When to implement your own

Implement a custom queue when:
- You need guaranteed O(1) operations (use linked list)
- You need a bounded circular buffer
- You need a deque with O(1) at both ends
- You need thread-safe operations (in Node.js with workers)
- The built-in array performance is measurably insufficient

For most applications, the built-in array with push/shift is sufficient.

## Summary

Queues are fundamental data structures that enforce First In, First Out ordering. They appear throughout computing in task scheduling, buffering, graph algorithms, and anywhere that arrival order determines processing order.

Key takeaways:

- **FIFO principle:** Elements are processed in the order they arrived. This provides fairness and predictable ordering.

- **Core operations:** enqueue (add to back), dequeue (remove from front), peek (view front), and isEmpty are the essential operations. All should be O(1) in a good implementation.

- **Implementation choices:** Arrays with index tracking work well for most cases. Linked lists provide guaranteed O(1) operations. Circular queues (ring buffers) efficiently handle bounded buffers.

- **Deques** extend queues to support operations at both ends, enabling use as either a stack or a queue.

- **Priority queues** break FIFO ordering to serve the highest-priority element first. They are typically implemented with heaps.

- **BFS relies on queues** to visit graph nodes level by level. Any algorithm requiring "process in discovery order" likely needs a queue.

- **Edge cases matter:** Empty queue operations, single-element transitions, circular buffer wraparound, and memory cleanup all require careful handling.

- **Choose based on your needs:** For small, simple cases, use built-in arrays. For performance-critical code or specific requirements, implement a custom queue with the right trade-offs.

Understanding queues prepares you for more advanced topics like priority queues, BFS-based algorithms, and concurrent message passing systems that use queue semantics.
