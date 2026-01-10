# What are Data Structures?

Reading time: 9 minutes

Data structures are ways to organize data so you can perform operations on it efficiently and correctly. They define how data is stored, how it is related, and which operations are fast or slow. In practice, a data structure is the concrete layout (like an array or linked nodes) plus a set of rules (like ordering or uniqueness) that the code preserves.

Think of a data structure as a contract between your data and your algorithms. It tells your program what is allowed (invariants) and what is easy (operations), which is why choosing the right one can simplify code and improve performance.

## Why they matter

Data structures matter because most software is about storing, finding, updating, or summarizing data. The same algorithm can go from fast to painfully slow depending on how the data is laid out.

Examples:
- A list of items stored in an array makes indexed access fast.
- A set of items stored in a hash table makes membership checks fast.
- A hierarchy stored as a tree makes parent-child traversal natural.

## Data structure vs. algorithm vs. abstract data type

These terms are related but distinct:

- **Abstract data type (ADT):** The behavior you want (stack, queue, set, map).
- **Data structure:** The concrete layout that implements that behavior (array, linked list, hash table).
- **Algorithm:** The step-by-step procedure that uses the data structure.

For example, a stack is an ADT. It can be implemented with an array or a linked list. Pushing and popping are the algorithms that operate on that structure.

## Core operations and invariants

Most data structures support a small set of core operations:

- **Insert:** Add an item.
- **Delete:** Remove an item.
- **Search/Find:** Locate an item.
- **Update:** Modify an item.
- **Traverse:** Visit items in some order.

An invariant is a rule that is always true for the structure. A binary search tree, for example, must keep everything smaller on the left and larger on the right. Invariants give you predictable behavior, but they also add maintenance costs during inserts and deletes.

## How data structures are implemented

Most implementations are built from two basic storage patterns:

### Contiguous storage (arrays)

Items are stored next to each other in memory. This makes indexed access fast, but insertions or deletions in the middle can be expensive because you must shift elements.

### Linked storage (nodes + pointers)

Each item stores a reference to the next (and sometimes previous) item. This makes inserts and deletes cheap when you have the node, but searching is usually slower because you must walk the links.

These patterns are often combined. Hash tables use arrays of buckets that link to nodes. Trees use node links but can be stored in arrays when the shape is predictable (like heaps).

## Common families of data structures

This is the minimal vocabulary most developers rely on:

### Linear structures

Items are arranged in a sequence.

- **Array:** Fixed or resizable, fast index access.
- **Linked list:** Fast insert/delete at a known position.
- **Stack (LIFO):** Last in, first out.
- **Queue (FIFO):** First in, first out.
- **Deque:** Double-ended queue.

### Hash-based structures

Use a hashing function to map keys to buckets.

- **Hash set:** Store unique values with fast membership checks.
- **Hash map:** Key-value pairs with fast lookups by key.

### Tree structures

Items are organized hierarchically.

- **Binary search tree:** Ordered hierarchy for fast search.
- **Heap / priority queue:** Fast access to min or max.
- **Trie:** Efficient prefix lookups (like autocomplete).
- **B-tree:** Balanced tree optimized for disk access.

### Graph structures

Model relationships where any node can connect to any other.

- **Graph (directed/undirected):** Social networks, road maps, dependency graphs.
- **Adjacency list/matrix:** Common representations.

### Specialized structures

Designed for specific constraints or performance goals.

- **Disjoint set (union-find):** Fast connectivity checks.
- **Segment tree / Fenwick tree:** Fast range queries.
- **Skip list:** Balanced search with probabilistic layering.

## Trade-offs you always balance

When choosing a data structure, you are trading off:

- **Time vs. space:** Extra memory can buy faster operations.
- **Read vs. write performance:** Some structures are optimized for search, others for updates.
- **Simplicity vs. specialization:** A simpler structure may be slower but easier to reason about.
- **Order vs. speed:** Keeping data sorted speeds up search but costs time on insertion.

No single structure is best for every scenario. Your goal is to match the structure to the operations that matter most for your workload.

## Complexity and cost model

Choosing a structure is usually a complexity decision. Big-O gives you a high-level view of time and space, but there are a few subtleties that matter in real programs:

- **Worst vs. average case:** Hash tables are fast on average, but can degrade if many keys collide.
- **Amortized costs:** Dynamic arrays make most appends O(1), but resizing occasionally makes a single append more expensive.
- **Memory locality:** Arrays are cache-friendly; linked structures can be slower due to pointer chasing.
- **Space overhead:** Pointers, buckets, and balancing metadata add memory overhead beyond the raw data.

## How to choose a data structure

Ask these questions before you pick one:

- **What operations are most frequent?** (Search? Inserts? Deletes?)
- **Do you need ordering?** (Sorted, insertion order, or any order?)
- **Is the data size known in advance?** (Fixed arrays vs. dynamic growth.)
- **Do you need fast lookups by key?** (Hash maps or trees.)
- **Do you need to model relationships?** (Graphs or trees.)

Examples:
- Maintaining a “most recent” list: use a deque or linked list.
- Fast membership checks: use a hash set.
- Range queries on numeric data: use a balanced tree or segment tree.
- Top-k retrieval: use a heap.

## Real-world mappings

Mapping problems to structures helps you internalize the trade-offs:

- **Undo/redo history:** Stack or two stacks.
- **Task scheduling:** Queue or priority queue.
- **Autocomplete or prefix search:** Trie.
- **Social graph or dependency graph:** Graph + adjacency list.
- **Cache with eviction:** Hash map + linked list (for LRU).

## Common pitfalls

Even good structures can fail when misused:

- **Ignoring worst-case behavior:** Some structures degrade if data is adversarial.
- **Over-optimizing early:** A simple array is often enough for small sizes.
- **Forgetting invariants:** If you break the rules, algorithms give incorrect results.
- **Assuming constant time is always constant:** Hash tables still pay for collisions and resizing.

## Summary

Data structures are the building blocks for efficient, correct programs. They define how data is stored, the rules it follows, and the operations you can perform quickly. Learning them is less about memorizing lists and more about understanding trade-offs so you can pick the right structure for the job.
