# Time vs Space Complexity

Reading time: 10 minutes

Every algorithm consumes two fundamental resources: **time** (how long it runs) and **space** (how much memory it uses). When analyzing an algorithm, you need to consider both dimensions because optimizing for one often comes at the cost of the other. Understanding this trade-off is essential for making informed engineering decisions.

## What is time complexity?

**Time complexity** measures how the number of operations an algorithm performs grows as the input size increases. It answers the question: "How much longer will this take if I double the input?"

Time complexity is not about measuring seconds or milliseconds. Those depend on hardware, language, and countless other factors. Instead, time complexity counts the fundamental operations: comparisons, assignments, arithmetic operations, or function calls. The goal is to understand the growth pattern, not the exact duration.

For example, if an algorithm examines every element in a list once, its time complexity is linear, often written as O(n). If it examines every pair of elements, the complexity is quadratic, O(n²). The notation captures how the work scales.

## What is space complexity?

**Space complexity** measures how much memory an algorithm requires as the input size grows. This includes:

- **Input space:** The memory needed to store the input itself.
- **Auxiliary space:** The extra memory the algorithm uses beyond the input, such as temporary variables, data structures, or recursive call stacks.

When people discuss space complexity, they often mean auxiliary space, the additional memory your algorithm allocates. An algorithm that sorts an array in place uses O(1) auxiliary space, while one that creates a copy of the array uses O(n) auxiliary space.

Space complexity matters because memory is finite. An algorithm that uses too much memory might crash, trigger swapping to disk (which is extremely slow), or simply cost more to run in cloud environments where you pay for RAM.

## The time-space trade-off

One of the most fundamental principles in algorithm design is that you can often trade time for space or space for time:

**Trading space for time:** Use extra memory to make operations faster.

- **Caching/memoization:** Store computed results to avoid recalculating them. A recursive Fibonacci implementation without memoization is O(2ⁿ); with memoization it becomes O(n), but uses O(n) space to store results.
- **Lookup tables:** Precompute answers and store them in a table. Checking if a character is a vowel can be done with five comparisons, or with a single hash set lookup if you store the vowels in a set.
- **Indexing:** Build an index to speed up searches. A database index uses space but turns O(n) scans into O(log n) lookups.

**Trading time for space:** Use less memory at the cost of more computation.

- **Recomputation:** Calculate values on demand instead of storing them. A streaming algorithm might process data in a single pass without keeping the entire dataset in memory.
- **Compression:** Store data in compressed form and decompress when needed. This saves space but costs CPU time.
- **In-place algorithms:** Modify the input directly instead of creating copies. In-place sorting uses O(1) extra space but may require more complex logic.

## When to prioritize time

Optimize for time when:

- **Latency matters:** User-facing applications need fast responses. A web server that takes seconds to respond loses users.
- **Throughput is critical:** Batch processing jobs that run on large datasets benefit from faster algorithms because the time savings multiply across millions of records.
- **Memory is abundant:** Modern servers often have gigabytes of RAM. If memory is not a constraint, using extra space to gain speed is usually worthwhile.
- **The algorithm runs frequently:** A function called millions of times per second should be as fast as possible, even if it uses more memory.

## When to prioritize space

Optimize for space when:

- **Memory is constrained:** Embedded systems, mobile devices, and serverless functions often have strict memory limits. Exceeding them causes crashes or throttling.
- **Data is large:** Processing a dataset larger than available RAM requires streaming algorithms or external memory techniques.
- **Cost is a factor:** Cloud computing charges for memory. A memory-hungry algorithm running across thousands of instances can significantly increase costs.
- **Garbage collection pressure:** In languages with garbage collection, allocating many objects can cause pauses. Reducing allocations improves consistency.

## Analyzing both dimensions

When evaluating an algorithm, always consider both complexities:

| Algorithm | Time Complexity | Space Complexity | Notes |
|-----------|-----------------|------------------|-------|
| Linear search | O(n) | O(1) | Simple, no extra space |
| Binary search | O(log n) | O(1) | Requires sorted input |
| Hash table lookup | O(1) average | O(n) | Fast but uses space for the table |
| Merge sort | O(n log n) | O(n) | Stable, but needs extra array |
| Quick sort | O(n log n) average | O(log n) | In-place, stack space for recursion |
| Heap sort | O(n log n) | O(1) | In-place, but not stable |

The "best" algorithm depends on your constraints. If you need guaranteed O(n log n) time and have memory to spare, merge sort works well. If memory is tight and you can tolerate occasional O(n²) worst case, quick sort with good pivot selection might be better.

## Common patterns and their complexities

**Iteration vs. recursion:**

Iterative solutions typically use O(1) space (just loop variables), while recursive solutions use O(d) space where d is the maximum recursion depth. Deep recursion can cause stack overflow errors.

```javascript
// Iterative: O(n) time, O(1) space
function sumIterative(arr) {
  let total = 0;
  for (let i = 0; i < arr.length; i++) {
    total += arr[i];
  }
  return total;
}

// Recursive: O(n) time, O(n) space (call stack)
function sumRecursive(arr, index = 0) {
  if (index >= arr.length) return 0;
  return arr[index] + sumRecursive(arr, index + 1);
}
```

**Building new structures vs. modifying in place:**

Creating a new array or object uses O(n) space. Modifying the input uses O(1) auxiliary space but changes the original data.

```javascript
// Creates new array: O(n) time, O(n) space
function doubleValues(arr) {
  return arr.map(x => x * 2);
}

// Modifies in place: O(n) time, O(1) space
function doubleValuesInPlace(arr) {
  for (let i = 0; i < arr.length; i++) {
    arr[i] = arr[i] * 2;
  }
  return arr;
}
```

**Hash maps for fast lookup:**

Using a hash map trades O(n) space for O(1) lookup time.

```javascript
// Without hash map: O(n²) time, O(1) space
function hasDuplicateSlow(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) return true;
    }
  }
  return false;
}

// With hash map: O(n) time, O(n) space
function hasDuplicateFast(arr) {
  const seen = new Set();
  for (const item of arr) {
    if (seen.has(item)) return true;
    seen.add(item);
  }
  return false;
}
```

## Real-world considerations

**Memory hierarchy affects time:**

Not all memory access is equal. CPU caches, RAM, and disk have vastly different speeds. An algorithm that uses more memory but accesses it in cache-friendly patterns (sequential access) can be faster than one that uses less memory but jumps around randomly.

**Temporary allocations add up:**

Creating many small objects in a loop can be more expensive than creating one large object, due to allocation overhead and garbage collection. Sometimes preallocating a buffer and reusing it is faster even though it uses constant extra space.

**Space complexity includes the call stack:**

Recursive algorithms use stack space for each call. A recursive function processing a million elements might overflow the stack even if it does not explicitly allocate any data structures.

## Making the right trade-off

There is no universal answer to "should I optimize for time or space?" The right choice depends on:

1. **Your constraints:** What are the hard limits on time and space?
2. **Your workload:** How large are typical inputs? How often does the code run?
3. **Your environment:** How much memory is available? How fast does the response need to be?
4. **Maintainability:** Sometimes a slightly slower or more memory-hungry solution is worth it if the code is clearer.

Start by understanding both complexities, then make a deliberate choice based on your situation.

## Summary

- **Time complexity** measures how running time grows with input size.
- **Space complexity** measures how memory usage grows with input size.
- You can often **trade space for time** (caching, lookup tables) or **time for space** (recomputation, streaming).
- **Prioritize time** when latency, throughput, or execution frequency matter most.
- **Prioritize space** when memory is constrained, data is large, or cost is a concern.
- Always analyze both dimensions and choose based on your specific constraints and workload.
