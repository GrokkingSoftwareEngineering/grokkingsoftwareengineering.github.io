# Basic Data Structures

Reading time: 25 minutes

**Basic data structures** are the foundational building blocks that every programmer uses daily, whether consciously or through abstractions. They include arrays, linked lists, stacks, queues, and hash tables. Mastering these five structures gives you the vocabulary to reason about more complex data organization and the skills to solve the majority of everyday programming challenges efficiently.

This page provides the conceptual groundwork for understanding basic data structures. You will learn what makes each structure unique, when to reach for one over another, and how they relate to each other. The sub-pages go deeper into each structure with implementation details and code examples.

## Why start with basic structures

These five structures appear everywhere in software. Arrays back lists, buffers, and matrix operations. Linked lists power undo systems, memory allocators, and cache eviction policies. Stacks enable recursion, expression parsing, and backtracking. Queues drive event loops, message systems, and breadth-first search. Hash tables provide the O(1) lookups that make databases, caches, and symbol tables fast.

You encounter these structures constantly, even when you do not realize it. Every time you use an array in JavaScript, a dictionary in Python, or a vector in C++, you are using one of these basic structures. Understanding how they work beneath the surface transforms you from someone who uses tools to someone who understands them.

More importantly, basic data structures teach fundamental trade-offs that apply everywhere:
- Contiguous memory versus linked nodes
- Fast reads versus fast writes
- Ordered access versus random access
- Memory efficiency versus time efficiency

Once you internalize these trade-offs, you can reason about any data structure you encounter, even ones you have never seen before.

## The five basic structures

This section introduces each structure conceptually. You will learn what problems each structure solves, what operations it supports efficiently, and when you should consider using it.

### Array

An **array** is a contiguous block of memory that stores elements of the same type at consecutive addresses. You access elements by their numeric index, and the computer calculates the exact memory location using simple arithmetic: base address plus index times element size.

**What arrays do well:**
- **Random access.** Fetching the element at position i takes constant time regardless of array size. The computer calculates the address directly without scanning.
- **Cache efficiency.** Because elements sit next to each other in memory, accessing one element often loads neighboring elements into CPU cache. Sequential traversal is extremely fast.
- **Memory efficiency.** Arrays store just the data with minimal overhead. No pointers, no per-element metadata.

**What arrays struggle with:**
- **Insertion in the middle.** Adding an element at position i requires shifting all elements after i by one position. For large arrays, this is expensive.
- **Deletion in the middle.** Removing an element similarly requires shifting to fill the gap.
- **Resizing.** Fixed-size arrays cannot grow. Dynamic arrays grow by allocating larger memory and copying all elements, which is expensive when it happens.

**When to use arrays:**
- You know the size ahead of time or it changes rarely.
- You primarily access elements by index.
- Sequential traversal is a common operation.
- Memory efficiency matters.

Arrays are the default choice for many problems. Start with an array unless you have a specific reason to use something else.

### Linked list

A **linked list** stores elements in separate nodes, where each node contains data and a pointer (or reference) to the next node. The nodes can be scattered anywhere in memory; the pointers connect them into a sequence.

**Variants:**
- **Singly linked list.** Each node points to the next node only. You can traverse forward but not backward.
- **Doubly linked list.** Each node points to both the next and previous nodes. You can traverse in either direction.
- **Circular linked list.** The last node points back to the first, forming a ring.

**What linked lists do well:**
- **Insertion and deletion at known positions.** If you have a reference to a node, inserting or deleting a neighbor takes constant time. You just update pointers; no shifting required.
- **Dynamic sizing.** The list grows and shrinks naturally as you add and remove nodes. No resizing or reallocation needed.
- **Flexible structure.** Nodes connect however you want. This flexibility enables variations like circular lists and multi-level lists.

**What linked lists struggle with:**
- **Random access.** Finding the element at position i requires walking through i nodes. There is no arithmetic shortcut.
- **Cache performance.** Nodes are allocated separately and may be scattered in memory. Each pointer traversal potentially misses cache, making linked lists much slower than arrays for sequential access in practice.
- **Memory overhead.** Each node stores one or two pointers in addition to data. For small data items, this overhead can double or triple memory usage.

**When to use linked lists:**
- You frequently insert or delete at positions where you already have a reference.
- You need a structure that grows and shrinks frequently without expensive resizing.
- Random access is rare or unnecessary.
- You are implementing other structures (like certain queue or stack variants) that benefit from linked nodes.

In modern computing, linked lists are less common than they once were because cache effects hurt their performance. However, they remain important for specific use cases and for understanding how pointers and nodes work.

### Stack

A **stack** is a Last-In-First-Out (LIFO) structure. The most recently added element is the first one removed. Think of a stack of plates: you add to the top and remove from the top.

**Core operations:**
- **Push.** Add an element to the top of the stack.
- **Pop.** Remove and return the element from the top.
- **Peek (or Top).** Look at the top element without removing it.
- **IsEmpty.** Check if the stack contains any elements.

**What stacks do well:**
- **Reversing order.** Items come out in the opposite order they went in. This is exactly what you need for undo operations, backtracking, and recursive algorithms.
- **Tracking nested state.** Opening brackets, function calls, and other nested structures naturally match the LIFO pattern.
- **Simple and fast.** All operations are O(1). Stacks are easy to implement correctly.

**What stacks struggle with:**
- **Accessing middle elements.** You can only access the top. To reach an element in the middle, you must pop everything above it.
- **Ordering flexibility.** LIFO is the only order available. If you need a different order, a stack is the wrong structure.

**Common use cases:**
- **Function call tracking.** When a function calls another function, the return address is pushed onto a call stack. When the inner function returns, the address is popped.
- **Expression evaluation.** Parsing mathematical expressions or programming languages often uses stacks to handle operator precedence and parentheses.
- **Undo functionality.** Each action is pushed onto a stack. Undo pops the most recent action and reverses it.
- **Depth-first search.** Exploring graph or tree nodes in depth-first order uses a stack (explicitly or via recursion).
- **Backtracking algorithms.** When exploring possibilities and needing to reverse course, stacks track the path taken.

Stacks can be implemented using arrays or linked lists. Array-based stacks are more common because they have better cache performance, but linked-list stacks avoid resizing overhead.

### Queue

A **queue** is a First-In-First-Out (FIFO) structure. The first element added is the first one removed. Think of a line at a checkout counter: people join at the back and are served from the front.

**Core operations:**
- **Enqueue.** Add an element to the back of the queue.
- **Dequeue.** Remove and return the element from the front.
- **Front (or Peek).** Look at the front element without removing it.
- **IsEmpty.** Check if the queue contains any elements.

**Variants:**
- **Simple queue.** Basic FIFO behavior as described above.
- **Circular queue (ring buffer).** Uses a fixed-size array with wrap-around, avoiding the need to shift elements.
- **Double-ended queue (deque).** Supports adding and removing from both ends.
- **Priority queue.** Elements are removed based on priority rather than insertion order. This is typically implemented as a heap, not a simple queue.

**What queues do well:**
- **Preserving order.** Items come out in the same order they went in. This is essential for fair scheduling and ordered processing.
- **Decoupling producers and consumers.** Producers add items to the queue; consumers remove them. The queue buffers speed differences between them.
- **Breadth-first processing.** When you need to process items level by level (like breadth-first search), queues naturally maintain the correct order.

**What queues struggle with:**
- **Accessing middle elements.** Like stacks, you can only access the ends. Random access requires dequeuing multiple elements.
- **Priority-based access.** A basic queue cannot efficiently extract the highest-priority item; you need a priority queue for that.

**Common use cases:**
- **Task scheduling.** Operating systems use queues to manage processes waiting for CPU time.
- **Message passing.** Distributed systems pass messages through queues to decouple senders and receivers.
- **Breadth-first search.** Exploring graph or tree nodes level by level uses a queue to maintain the frontier.
- **Buffering.** Queues buffer data between components that produce and consume at different rates.
- **Print spooling.** Print jobs are queued and processed in order of submission.

Queues can be implemented with arrays (especially circular arrays) or linked lists. Circular arrays are efficient and cache-friendly. Linked lists avoid fixed-size limitations.

### Hash table

A **hash table** (also called a **hash map**) stores key-value pairs and supports fast lookup, insertion, and deletion by key. It uses a hash function to convert keys into array indices, allowing direct access to the stored value without scanning.

**How it works:**
1. You provide a key (like a string or number).
2. A hash function computes an integer from the key.
3. The integer is converted to an array index (typically using modulo).
4. The value is stored at or near that index.

**Handling collisions:**
Two different keys can hash to the same index. Hash tables handle this with one of two main strategies:
- **Chaining.** Each array position holds a list (or other structure) of all key-value pairs that hashed to that index.
- **Open addressing.** When a collision occurs, the table probes for another empty slot using a defined sequence.

**What hash tables do well:**
- **Fast average-case operations.** With a good hash function and enough capacity, lookup, insertion, and deletion all take O(1) average time.
- **Flexible keys.** You can use almost any data type as a key, as long as you can define a hash function for it.
- **Efficient memory use.** Hash tables only store what you put in them (plus some overhead for capacity).

**What hash tables struggle with:**
- **Worst-case performance.** If many keys hash to the same index, operations degrade to O(n). Good hash functions and resizing policies minimize this risk.
- **No ordering.** Keys are not stored in any particular order. You cannot efficiently find the minimum key or iterate in sorted order.
- **Cache behavior varies.** Chained hash tables have pointer-chasing overhead. Open-addressed tables can have better cache performance but suffer from clustering.

**Common use cases:**
- **Dictionaries and maps.** Storing key-value associations is the primary use case.
- **Sets.** Storing keys without values to check membership quickly.
- **Caching.** Storing computed results keyed by their inputs to avoid recomputation.
- **Counting and grouping.** Counting occurrences of items or grouping items by category.
- **Symbol tables.** Compilers and interpreters use hash tables to store variable names and their properties.
- **Deduplication.** Checking if an item has been seen before.

Hash tables are one of the most widely used data structures because O(1) operations are extremely valuable. Understanding how they work, including collision handling and load factors, is essential knowledge for any programmer.

## How these structures relate

The five basic structures are not isolated; they connect to each other in important ways.

### Implementation relationships

Some structures are implemented using others:
- **Stacks** are typically implemented using arrays or linked lists. The underlying structure is hidden; you only interact with push and pop.
- **Queues** are typically implemented using arrays (especially circular arrays) or linked lists.
- **Hash tables** use arrays as their underlying storage, with linked lists or probing sequences handling collisions.

Understanding these layered relationships helps you reason about performance. A linked-list-based stack inherits the cache problems of linked lists. An array-based queue inherits the resizing behavior of arrays.

### Conceptual progression

The structures also form a conceptual progression:
- **Arrays** introduce contiguous storage and random access.
- **Linked lists** introduce pointers and dynamic structures.
- **Stacks and queues** introduce restricted access patterns that enable specific algorithms.
- **Hash tables** introduce key-based access and the power of hashing.

Each structure builds on ideas from the previous ones. Understanding arrays helps you understand how hash tables store their buckets. Understanding linked lists helps you understand chaining in hash tables.

### Trade-off patterns

The same trade-offs appear across structures:
- Arrays sacrifice insertion flexibility for access speed. Hash tables make a similar trade-off at a higher level: they sacrifice ordering for lookup speed.
- Linked lists sacrifice access speed for insertion flexibility. Priority queues (heaps) make a similar trade-off: they sacrifice general access for fast access to the extreme element.
- Stacks and queues restrict what you can do in exchange for simplicity and speed.

Recognizing these patterns helps you predict the behavior of new structures you encounter.

## Choosing between basic structures

Selecting the right structure starts with understanding your requirements. Here are questions to guide your choice.

### What operations do you need?

Different structures excel at different operations:

| Operation | Array | Linked List | Stack | Queue | Hash Table |
|-----------|-------|-------------|-------|-------|------------|
| Access by index | Fast | Slow | N/A | N/A | N/A |
| Access by key | N/A | N/A | N/A | N/A | Fast |
| Access ends | Fast | Fast (with tail pointer) | Fast (top only) | Fast (both ends) | N/A |
| Insert at end | Fast* | Fast (with tail pointer) | Fast (push) | Fast (enqueue) | Fast |
| Insert in middle | Slow | Fast (with reference) | N/A | N/A | N/A |
| Delete at end | Fast | Fast (doubly linked) | Fast (pop) | Slow** | Fast |
| Delete in middle | Slow | Fast (with reference) | N/A | N/A | Fast |
| Search by value | Slow | Slow | N/A | N/A | Fast (by key) |

*Fast amortized; occasionally slow due to resizing.
**Dequeues are from the front, not the end.

### Is ordering important?

- **Need sorted order?** None of these basic structures maintain sorted order efficiently. Consider a tree or sorted array.
- **Need insertion order?** Arrays and linked lists preserve insertion order. Hash tables do not (though some variants like LinkedHashMap do).
- **Need LIFO order?** Use a stack.
- **Need FIFO order?** Use a queue.
- **Order does not matter?** Hash tables offer the fastest operations when ordering is irrelevant.

### What is your access pattern?

- **Accessing by numeric position?** Arrays are the clear choice.
- **Accessing by key?** Hash tables provide O(1) lookup.
- **Always accessing the most recent item?** Stacks are designed for this.
- **Always accessing the oldest item?** Queues handle this naturally.
- **Frequently traversing the entire collection?** Arrays have the best cache performance.

### How does the collection change?

- **Size is fixed or rarely changes?** Arrays are simple and efficient.
- **Frequent insertions and deletions at known positions?** Linked lists avoid shifting.
- **Frequent additions to one end?** Both arrays (with dynamic sizing) and linked lists handle this well.
- **Frequent additions and removals at both ends?** Deques are designed for this.

### Are there memory constraints?

- **Need compact storage?** Arrays have minimal overhead per element.
- **Have many small elements?** Linked lists and hash tables have significant per-element overhead (pointers, metadata).
- **Need to handle large numbers of items?** Hash tables scale well but need capacity planning.

## Common misconceptions

Clearing up frequent misunderstandings helps you use these structures correctly.

### Arrays are always the simplest choice

Arrays are simple for random access and sequential traversal. But for frequent insertions and deletions, their simplicity becomes complexity: you end up writing shifting logic or managing indices carefully. A linked list might be simpler for that use case.

### Linked lists are obsolete

Linked lists have real disadvantages in modern computing due to cache effects. But they remain valuable in specific contexts:
- Implementing other data structures (queues, LRU caches)
- When you have direct references to nodes and need fast local modifications
- In languages or systems where pointer manipulation is efficient
- For teaching fundamental concepts about pointers and memory

Dismissing linked lists entirely means missing use cases where they genuinely excel.

### Hash tables are always O(1)

Hash table operations are O(1) on average, with good hash functions and appropriate sizing. But:
- Worst case is O(n) when many keys collide.
- Resizing is O(n) when it happens.
- Bad hash functions can make every operation slow.
- The constant factors in O(1) can be significant (computing hashes, handling collisions).

Understanding when hash tables degrade helps you avoid performance surprises.

### Stacks and queues are just restricted arrays

While you can implement stacks and queues with arrays, thinking of them as "restricted arrays" misses the point. The restrictions are the value:
- They enforce usage patterns that make bugs less likely.
- They communicate intent to other developers.
- They match specific algorithmic patterns (LIFO for recursion, FIFO for scheduling).

The restriction is a feature, not a limitation.

## Performance characteristics summary

Here is a quick reference for the time complexity of common operations. All times are for typical implementations with reasonable assumptions.

### Array

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| Access by index | O(1) | Direct address calculation |
| Search by value | O(n) | Must scan; O(log n) if sorted |
| Insert at end | O(1) amortized | O(n) when resizing |
| Insert at position | O(n) | Requires shifting |
| Delete at end | O(1) | No shifting needed |
| Delete at position | O(n) | Requires shifting |

### Linked list

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| Access by index | O(n) | Must traverse |
| Search by value | O(n) | Must traverse |
| Insert at head | O(1) | Just update pointers |
| Insert at tail | O(1) | With tail pointer |
| Insert after node | O(1) | Given reference to node |
| Delete head | O(1) | Just update pointer |
| Delete after node | O(1) | Given reference to node |

### Stack

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| Push | O(1) | Or O(1) amortized for array-based |
| Pop | O(1) | |
| Peek | O(1) | |
| IsEmpty | O(1) | |

### Queue

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| Enqueue | O(1) | Or O(1) amortized for array-based |
| Dequeue | O(1) | |
| Front | O(1) | |
| IsEmpty | O(1) | |

### Hash table

| Operation | Average | Worst Case | Notes |
|-----------|---------|------------|-------|
| Insert | O(1) | O(n) | Worst case with many collisions |
| Delete | O(1) | O(n) | |
| Lookup | O(1) | O(n) | |
| Resize | O(n) | O(n) | Happens when load factor exceeded |

## Space complexity

Understanding memory usage helps you choose structures for memory-constrained environments.

### Array

Memory usage: n * (element size), plus a small fixed overhead for the array object itself.

For dynamic arrays, there may be unused capacity. Common growth strategies double the capacity, so up to 50% of allocated memory may be unused.

### Linked list

Memory usage per element: element size + pointer size(s).

For a singly linked list, add one pointer per element. For doubly linked, add two. On 64-bit systems, each pointer is 8 bytes. For small elements (like integers), the pointer overhead can exceed the data size.

### Stack and queue

Memory usage depends on the underlying implementation:
- Array-based: Same as array, potentially with unused capacity.
- Linked-list-based: Same as linked list, with per-element pointer overhead.

### Hash table

Memory usage: array of buckets + stored entries + collision handling overhead.

Load factor (entries / buckets) controls the trade-off between memory and performance. Lower load factors use more memory but have fewer collisions. Typical load factors are 0.5 to 0.75.

## Navigating the sub-pages

Each basic data structure has its own detail page where you will find implementation guidance, code examples, and deeper exploration of trade-offs.

**Arrays** covers static versus dynamic arrays, resizing strategies, multi-dimensional arrays, and common array algorithms.

**Linked Lists** covers singly and doubly linked variants, sentinel nodes, circular lists, and implementation patterns.

**Stacks** covers array-based and linked-list implementations, common applications like expression evaluation, and related algorithms.

**Queues** covers simple queues, circular buffers, deques, and the relationship to priority queues.

**Hash Tables** covers hash functions, collision resolution strategies, load factors, resizing, and practical implementation details.

Start with any structure that interests you or that you need for a current problem. The pages are designed to be read independently, though understanding arrays helps with understanding hash tables.

## Summary

Basic data structures are the foundation of practical programming. Mastering arrays, linked lists, stacks, queues, and hash tables gives you the tools to solve most everyday data organization problems efficiently.

Key takeaways:

- **Arrays** provide fast random access and cache-efficient traversal but struggle with insertions and deletions in the middle. Use them when you access by position and the size is relatively stable.

- **Linked lists** provide fast insertions and deletions at known positions but sacrifice random access and cache efficiency. Use them when you frequently modify the structure at positions where you have references.

- **Stacks** provide LIFO access with O(1) push and pop. Use them for undo operations, recursion, backtracking, and any situation where you need to reverse order.

- **Queues** provide FIFO access with O(1) enqueue and dequeue. Use them for task scheduling, message passing, breadth-first algorithms, and any situation where order of arrival matters.

- **Hash tables** provide O(1) average-case lookup, insertion, and deletion by key. Use them when you need fast access by key and do not require ordering.

These five structures are implemented using each other and share common trade-off patterns. Understanding them deeply prepares you for more advanced data structures and helps you make informed choices in your own code.
