# What are Data Structures?

Reading time: 25 minutes

**Data structures** are ways to organize data so you can perform operations on it efficiently and correctly. They define how data is stored, how it is related, and which operations are fast or slow. In practice, a data structure is the concrete layout (like an array or linked nodes) plus a set of rules (like ordering or uniqueness) that the code preserves.

Think of a data structure as a contract between your data and your algorithms. It tells your program what is allowed (invariants) and what is easy (operations), which is why choosing the right one can simplify code and improve performance. This page gives you the conceptual foundation you need before diving into specific structures.

## Why data structures matter

Data structures matter because most software is about storing, finding, updating, or summarizing data. The same algorithm can go from fast to painfully slow depending on how the data is laid out.

Consider three scenarios:

- A list of items stored in an array makes indexed access fast. If you know you want the fifth element, you get it instantly.
- A set of items stored in a hash table makes membership checks fast. Asking "is X in the set?" takes nearly constant time regardless of how many items exist.
- A hierarchy stored as a tree makes parent-child traversal natural. Finding all descendants of a node follows the structure itself.

Each structure excels at certain operations and struggles with others. An array lets you jump to any index instantly, but inserting in the middle forces you to shift everything after it. A linked list handles insertions gracefully, but finding the fifth element means walking through four nodes first.

This is not about memorizing which structure is "best." It is about understanding what trade-offs each structure makes so you can match it to your actual workload.

## Data structure vs. algorithm vs. abstract data type

These three terms are related but distinct. Confusing them leads to muddy thinking, so let's separate them clearly.

### Abstract data type (ADT)

An **abstract data type** describes behavior without specifying implementation. It answers "what can I do?" but not "how is it done?"

Examples of ADTs:
- A **stack** supports push (add to top) and pop (remove from top).
- A **queue** supports enqueue (add to back) and dequeue (remove from front).
- A **set** supports add, remove, and membership checking.
- A **map** (or dictionary) supports storing and retrieving values by key.

ADTs are contracts. They promise certain operations will exist and behave in specific ways. They say nothing about memory layout, performance characteristics, or implementation strategy.

### Data structure

A **data structure** is a concrete implementation of an ADT. It answers "how is the data actually stored?"

The same ADT can have multiple implementations:
- A stack can be implemented with an array or a linked list.
- A set can be implemented with a hash table, a balanced tree, or even a sorted array.
- A map can be implemented with a hash table or various tree structures.

Each implementation makes different trade-offs. An array-based stack has better memory locality but may need to resize. A linked-list stack never resizes but uses more memory per element due to pointers.

### Algorithm

An **algorithm** is a step-by-step procedure that operates on data. It answers "what sequence of operations solves this problem?"

Algorithms and data structures are deeply intertwined. Binary search requires sorted data. Dijkstra's shortest path algorithm requires a graph representation. The efficiency of an algorithm often depends entirely on the data structure it uses.

### Why the distinction matters

When someone says "use a queue," they might mean the ADT (behavior) or a specific implementation (data structure). When designing systems, think in terms of ADTs first—what operations do you need? Then choose a data structure that implements those operations efficiently for your use case.

## Core operations and their costs

Most data structures support a small set of fundamental operations. Understanding these operations and their typical costs helps you reason about any structure you encounter.

### Insert

Adding an item to the structure. The key questions are:
- Where can you insert? (Only at ends? Anywhere? At a specific position?)
- Does insertion maintain some ordering or invariant?
- Does the structure need to resize or rebalance?

### Delete

Removing an item from the structure. The key questions are:
- How do you specify which item to delete? (By position? By value? By key?)
- Does deletion require reorganizing remaining elements?
- Can deletion leave the structure in an invalid state that needs repair?

### Search / Find

Locating an item in the structure. The key questions are:
- What are you searching by? (Position? Value? Key?)
- Does the structure maintain any ordering that speeds up search?
- Do you need exact matches or range queries?

### Update

Modifying an existing item. This is often a combination of find and replace, but some structures support efficient in-place updates.

### Traverse

Visiting items in some order. The key questions are:
- What orderings are natural for this structure? (Insertion order? Sorted order? Level order?)
- Can you traverse without extra memory, or do you need auxiliary storage?
- Is traversal destructive (like popping a stack) or read-only?

### Access

Retrieving an item without searching. Some structures support direct access by position (arrays) or by key (hash maps), while others require traversal.

## Invariants: the rules structures maintain

An **invariant** is a rule that must always be true for a data structure to function correctly. Invariants give you predictable behavior, but they cost time to maintain during modifications.

### Examples of invariants

- A **binary search tree** keeps smaller values on the left and larger values on the right. This invariant enables fast search but requires careful insertion and deletion.
- A **heap** keeps the minimum (or maximum) value at the root. This invariant enables fast access to the extreme value but requires rebalancing after modifications.
- A **balanced tree** keeps its height logarithmic relative to its size. This invariant prevents degradation into a linked list but requires rotation operations.
- A **hash table** maintains a relationship between keys and bucket positions. This invariant enables fast lookups but requires rehashing when the table grows.

### The cost of invariants

Every invariant you maintain costs time during writes. A simple unsorted list has no ordering invariant, so insertions are trivially fast—just append. A sorted list maintains an ordering invariant, so every insertion must find the right position and potentially shift elements.

When choosing a structure, consider:
- Which invariants give you the operations you need?
- How often do you modify the structure versus query it?
- Can you afford the maintenance cost of stronger invariants?

### Breaking invariants

If you break a structure's invariants, algorithms that depend on them will give incorrect results. A binary search on an unsorted array will miss elements. A heap extraction on a corrupted heap will not return the minimum.

Some structures are self-healing: they detect and repair violations automatically. Others trust you to maintain invariants yourself. Know which you are working with.

## How data structures are implemented

Most implementations are built from two basic storage patterns. Understanding these patterns helps you reason about any structure's performance characteristics.

### Contiguous storage (arrays)

Items are stored next to each other in memory. This has several important consequences:

**Advantages:**
- **Fast indexed access.** Computing the address of element i is simple arithmetic: base address plus i times element size. This takes constant time regardless of array size.
- **Cache efficiency.** Modern CPUs load memory in chunks called cache lines. When elements are contiguous, accessing one element often loads its neighbors into cache, making sequential access very fast.
- **Predictable memory usage.** The total memory is simply element size times count, plus a small fixed overhead.

**Disadvantages:**
- **Expensive middle insertions/deletions.** Adding or removing an element in the middle requires shifting all subsequent elements. For an array of n elements, this takes O(n) time in the worst case.
- **Resizing overhead.** Fixed-size arrays cannot grow. Dynamic arrays grow by allocating larger memory and copying all elements, which is expensive when it happens.
- **Wasted space or fragmentation.** Dynamic arrays often over-allocate to avoid frequent resizing, wasting memory. Fixed arrays may be too small or too large for actual needs.

### Linked storage (nodes + pointers)

Each item is stored in a separate node that contains the data plus references (pointers) to related nodes. This has complementary trade-offs:

**Advantages:**
- **Cheap insertions/deletions.** When you have a reference to a node, adding or removing a neighbor just updates a few pointers. No shifting required.
- **Dynamic sizing.** The structure grows and shrinks naturally as nodes are added and removed. No resizing or reallocation needed.
- **Flexible connections.** Nodes can connect in arbitrary patterns: linear chains, trees, graphs, or custom topologies.

**Disadvantages:**
- **Slow indexed access.** Finding the ith element requires walking through i-1 nodes. There is no arithmetic shortcut.
- **Poor cache efficiency.** Nodes are allocated separately and may be scattered in memory. Following pointers often causes cache misses, which are orders of magnitude slower than cache hits.
- **Memory overhead.** Each node stores pointers in addition to data. For small data items, this overhead can double or triple memory usage.

### Hybrid approaches

Many practical data structures combine both patterns:

- **Hash tables** use an array of buckets, where each bucket may contain a linked list of entries that hashed to the same position.
- **B-trees** store multiple keys in each node (array-like) but connect nodes with pointers (linked-like). This balances cache efficiency with flexible growth.
- **Array-based heaps** store a tree structure in an array by using index arithmetic to find parents and children. This gets the benefits of both: tree semantics with array performance.
- **Deques** (double-ended queues) are often implemented as arrays of fixed-size blocks, with pointers connecting blocks. This allows efficient operations at both ends.

## Common families of data structures

This section provides a mental map of the major categories. Each family shares common characteristics and trade-offs.

### Linear structures

Items are arranged in a sequence with a clear beginning and end. Each item (except the first and last) has exactly one predecessor and one successor.

**Array:** The most fundamental structure. Stores elements in contiguous memory with fast index-based access. Best when you know the size upfront and primarily access elements by position.

**Linked list:** Stores elements in nodes connected by pointers. Best when you frequently insert or delete at known positions and rarely need index-based access. Variants include singly linked (forward pointers only), doubly linked (forward and backward), and circular (last connects to first).

**Stack (LIFO):** Last in, first out. You can only add to and remove from the top. Think of a stack of plates. Useful for tracking state that you need to reverse, like undo operations or function call frames.

**Queue (FIFO):** First in, first out. You add to the back and remove from the front. Think of a line at a store. Useful for processing items in the order they arrived, like task scheduling or message handling.

**Deque (double-ended queue):** Supports adding and removing at both ends. Useful when you need flexibility about which end to use, like implementing both stack and queue behavior.

### Associative structures

These structures store key-value pairs and support efficient lookup by key.

**Hash map (hash table):** Uses a hash function to map keys to array positions. Provides average-case constant-time insert, delete, and lookup. Best for large collections with unpredictable access patterns. Sensitive to hash function quality and requires handling of collisions.

**Tree map (balanced search tree):** Stores key-value pairs in a tree ordered by key. Provides logarithmic-time operations and supports ordered iteration and range queries. Best when you need both fast lookup and key ordering.

### Set structures

These structures store unique values and support efficient membership testing.

**Hash set:** Implemented like a hash map but only stores keys, not values. Provides average-case constant-time add, remove, and membership check.

**Tree set:** Implemented as a balanced tree storing only keys. Provides logarithmic operations and ordered iteration.

### Tree structures

Trees organize data hierarchically, with a single root and parent-child relationships.

**Binary tree:** Each node has at most two children. The simplest tree structure, forming the basis for many specialized variants.

**Binary search tree (BST):** A binary tree with an ordering invariant: left descendants are smaller, right descendants are larger. Enables fast search, but performance degrades if the tree becomes unbalanced.

**Balanced trees (AVL, Red-Black, B-trees):** BST variants that maintain balance invariants to guarantee logarithmic height. More complex to implement but provide consistent performance.

**Heap / Priority queue:** A tree (usually binary) where each parent is smaller (or larger) than its children. The root is always the minimum (or maximum). Enables fast access to the extreme value and efficient insert. Used for scheduling, event processing, and graph algorithms.

**Trie (prefix tree):** A tree where paths from root to leaves spell out strings. Enables efficient prefix matching, like autocomplete or IP routing tables.

### Graph structures

Graphs model relationships where any node can connect to any other. Unlike trees, graphs can have cycles and multiple paths between nodes.

**Directed graph:** Edges have direction. An edge from A to B does not imply an edge from B to A. Models one-way relationships like web links, dependencies, or state machines.

**Undirected graph:** Edges have no direction. A connection between A and B works both ways. Models symmetric relationships like friendships or physical connections.

**Weighted graph:** Edges have associated values (weights). Used to model costs, distances, or capacities.

Common representations:
- **Adjacency list:** Each node stores a list of its neighbors. Memory-efficient for sparse graphs.
- **Adjacency matrix:** A 2D array where entry (i,j) indicates whether nodes i and j are connected. Time-efficient for dense graphs and for checking specific edges.

### Specialized structures

These structures are optimized for specific operations or domains.

**Disjoint set (Union-Find):** Maintains a collection of non-overlapping sets. Supports fast operations to merge sets and check if elements are in the same set. Used in network connectivity, image processing, and graph algorithms like Kruskal's.

**Segment tree:** Stores intervals and supports fast range queries (like sum, minimum, or maximum over a range) and point updates. Used in computational geometry and database indexing.

**Fenwick tree (Binary Indexed Tree):** A simpler alternative to segment trees for cumulative frequency queries. Uses less memory and has lower constant factors.

**Skip list:** A probabilistic structure that layers linked lists to enable logarithmic search. Simpler to implement than balanced trees with similar performance. Used in databases and concurrent data structures.

**Bloom filter:** A probabilistic set that can definitively say "not present" but may have false positives for "present." Uses very little memory. Used for caching, spam filtering, and database optimization.

## Trade-offs you always balance

Every data structure makes trade-offs. Understanding common trade-off axes helps you evaluate structures for your needs.

### Time vs. space

You can often reduce operation time by using more memory. Examples:

- Hash tables use extra space (empty buckets) to achieve fast average-case operations.
- Caching computed results trades memory for speed by avoiding recomputation.
- Storing redundant indexes enables multiple fast lookup paths.

Conversely, memory-constrained environments may force slower algorithms that use less space.

### Read vs. write performance

Structures that make reading fast often make writing slow, and vice versa.

- A sorted array enables fast binary search (reads) but slow insertion (writes).
- An unsorted list enables fast insertion (writes) but slow search (reads).
- Read-heavy workloads favor sorted structures; write-heavy workloads favor simpler ones.

Consider your actual access patterns. A structure that is 10x slower for writes but only 2x faster for reads is a bad trade if you write frequently.

### Simplicity vs. specialization

Simple structures are easier to understand, debug, and maintain. Specialized structures offer better performance for specific operations.

- An array is simple and works well for many tasks. It is rarely the optimal choice for any specific task, but it is good enough for many.
- A Fibonacci heap offers better theoretical complexity for certain operations but is much harder to implement correctly.

Start simple. Specialize only when you have evidence that the simple approach is a bottleneck.

### Order vs. speed

Maintaining order costs time.

- A hash set has no ordering but offers constant-time operations.
- A tree set maintains sorted order but has logarithmic operations.
- An insertion-ordered map (like LinkedHashMap) maintains the order items were added but uses extra memory.

Only pay for ordering if you need it.

### Flexibility vs. performance

Generic structures work for many types and sizes. Specialized structures exploit constraints for better performance.

- A generic dynamic array works for any element type and grows as needed.
- A bit array for boolean values uses 8x less memory than a byte array.
- A small fixed array may outperform a dynamic one due to allocation and cache effects.

## Memory hierarchy and cache effects

Understanding how memory hardware works explains why some data structures perform better than theory predicts and others perform worse.

### The memory hierarchy

Modern computers have a hierarchy of storage:
1. **CPU registers:** Fastest, smallest. Holds data being actively computed.
2. **L1 cache:** Very fast, small (32-64 KB). Holds recently accessed data.
3. **L2 cache:** Fast, medium (256 KB - 1 MB). Backs up L1.
4. **L3 cache:** Moderate speed, larger (several MB). Shared across cores.
5. **Main memory (RAM):** Slower, large (GB). The primary working storage.
6. **Disk/SSD:** Much slower, very large (TB). Persistent storage.

Each level is roughly 10-100x slower than the one above it.

### Cache-friendly vs. cache-hostile structures

When you access memory, the CPU loads a whole cache line (typically 64 bytes), not just the byte you requested. This has profound implications:

**Array traversal is fast** because elements are contiguous. Loading one element often loads several neighbors for free. Sequential access patterns can approach memory bandwidth limits.

**Linked list traversal is slow** because nodes may be scattered in memory. Each node access potentially misses cache, waiting for main memory. A linked list can be 10-100x slower than an array for sequential access, even though big-O analysis shows the same O(n).

**Tree layouts matter.** A binary tree implemented with pointers suffers from cache misses. The same tree stored in an array (like a heap) is much faster. B-trees explicitly optimize for cache by storing many keys per node.

### Implications for structure choice

When comparing structures, big-O notation tells part of the story. Cache effects tell another part:

- For small datasets, an O(n) array search may beat an O(log n) tree search because the entire array fits in cache.
- For random access patterns, cache effects matter less because nothing stays in cache anyway.
- For sequential patterns, prefer contiguous structures even if big-O looks worse.

## How to choose a data structure

Selecting the right data structure starts with understanding your requirements. Ask these questions:

### What operations are most frequent?

Profile your expected workload:
- If you mostly search, optimize for search performance.
- If you mostly insert and rarely search, optimize for insertion.
- If you need both frequently, you need a balanced structure.

### Do you need ordering?

Different kinds of ordering have different costs:
- **Sorted order:** Enables binary search and range queries. Costs time on every insert.
- **Insertion order:** Enables reproducing the sequence items arrived. Costs extra memory.
- **No order:** Simplest and often fastest. Acceptable when order does not matter.

### Is the size known in advance?

- **Known size:** Fixed arrays are simple and efficient.
- **Unknown size:** Dynamic structures must resize. Consider amortized costs.
- **Bounded size:** Ring buffers or fixed pools may work.

### What are you looking up by?

- **By position:** Arrays excel here.
- **By key:** Hash maps or tree maps.
- **By value:** Most structures require linear search unless sorted.
- **By multiple attributes:** May need multiple indexes or specialized structures.

### Do you need to model relationships?

- **Hierarchical:** Trees.
- **Arbitrary connections:** Graphs.
- **Containment:** Nested structures.

### What are your memory constraints?

- **Plenty of memory:** Use whatever is fastest.
- **Limited memory:** Favor compact structures over pointer-heavy ones.
- **Extreme constraints:** May need specialized compact representations.

## Real-world mappings

Seeing how common problems map to data structures helps build intuition.

### Undo/redo history

A stack stores operations. Undo pops the most recent operation and moves it to a redo stack. Redo reverses this.

### Task scheduling

A queue ensures tasks are processed in order of arrival. A priority queue processes tasks by urgency instead of arrival order.

### Autocomplete and prefix search

A trie stores words such that common prefixes share nodes. Finding all words with a given prefix means traversing to that prefix node and collecting all descendants.

### Social networks and recommendation

A graph models users as nodes and relationships as edges. Finding friends-of-friends is a graph traversal. Recommendation algorithms use graph structure to find related items.

### LRU cache

A combination of a hash map (for fast lookup by key) and a doubly linked list (for tracking recency and enabling fast removal). When the cache is full, you remove the least recently used item from the list end.

### Database indexes

B-trees provide ordered key lookup with good disk performance. Hash indexes provide even faster lookup when ordering is not needed.

### Network routing

Tries store IP prefixes for longest-prefix matching. Each lookup finds the most specific route.

### Event simulation

A priority queue (min-heap) stores future events ordered by time. The simulation repeatedly extracts the next event and may insert new events as consequences.

## Common pitfalls

Even good structures can fail when misused. Watch for these patterns:

### Ignoring worst-case behavior

Some structures have bad worst cases:
- Hash tables degrade to O(n) if all keys collide.
- Binary search trees degrade to O(n) if insertions are ordered.
- Quick sort degrades to O(n²) for certain inputs.

Know when worst cases matter (adversarial input, security-critical systems) and choose accordingly.

### Over-optimizing early

A simple array often outperforms complex structures for small data:
- Linear search on 20 elements is fine.
- Setup costs for fancy structures may exceed their benefits.

Benchmark before optimizing. The bottleneck is often elsewhere.

### Forgetting invariants

Structures work because they maintain rules:
- Sorting an array once is useless if you later add elements without re-sorting.
- A binary search tree is useless if you modify keys without rebalancing.

Design your system to maintain invariants, not just establish them.

### Assuming constant time is always constant

Constant time has fine print:
- Hash table lookups pay for collisions and occasionally for resizing.
- Dynamic array appends occasionally pay for growth.
- Memory allocation is often hidden but not free.

Understand what "constant" really means for your structure.

### Ignoring memory effects

Big-O ignores cache:
- A theoretically slower array operation may beat a theoretically faster tree operation due to cache locality.
- Pointer-chasing structures underperform in practice.

Consider memory access patterns, not just operation counts.

## Navigating the sub-pages

This site covers major data structure families in detail. Here is how to navigate:

- **Basic Data Structures** covers the fundamental structures every programmer should know: arrays, linked lists, stacks, queues, and hash tables.
- **Tree Data Structures** covers binary trees, search trees, heaps, tries, and traversal algorithms.
- **Graph Data Structures** covers representations, search algorithms, and shortest path algorithms.
- **Advanced Data Structures** covers specialized structures like segment trees and union-find.

Start with the basics if you are new to the topic. Jump to specific structures if you have a particular need. Each page goes deeper into implementation details and provides code examples.

## Summary

Data structures are the building blocks for efficient, correct programs. They define how data is stored, the rules it follows, and the operations you can perform quickly.

Key takeaways:
- **Abstract data types** describe behavior; **data structures** provide implementation; **algorithms** operate on structures.
- Most structures build on **contiguous storage** (arrays) or **linked storage** (nodes and pointers), with different trade-offs.
- **Invariants** give you guarantees but cost time to maintain.
- Every choice involves trade-offs: time vs. space, read vs. write, simplicity vs. performance.
- **Cache effects** often matter more than theoretical complexity for real performance.
- Choose structures based on your actual operations, not abstract "best" recommendations.

Understanding data structures is not about memorizing lists. It is about developing intuition for trade-offs so you can pick the right structure for each problem you face.
