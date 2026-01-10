# Big-O Notation

Reading time: 10 minutes

**Big-O notation** is the most widely used tool for describing algorithmic complexity. It provides an upper bound on the growth rate of an algorithm, answering the question: "In the worst case, how does this algorithm's resource usage scale with input size?"

When engineers discuss algorithm performance, they almost always use Big-O. Understanding it is essential for technical interviews, code reviews, and system design discussions.

## What Big-O means

Big-O notation describes an upper bound on an algorithm's growth rate. When we say an algorithm is **O(n)**, we mean:

- The algorithm's running time (or space usage) grows **at most** linearly with input size.
- For sufficiently large inputs, the actual cost is bounded above by some constant multiple of n.
- The algorithm might be faster than O(n) in some cases, but it will never be slower.

The "O" stands for "order of" and is sometimes read as "on the order of." O(n²) means "on the order of n squared."

## The formal definition

For the mathematically inclined, here is the precise definition:

**f(n) = O(g(n))** if and only if there exist positive constants **c** and **n₀** such that:

**f(n) ≤ c · g(n)** for all **n ≥ n₀**

In plain language: f(n) is O(g(n)) if, past some starting point n₀, f(n) is always bounded above by a constant multiple of g(n).

For example, the function f(n) = 3n + 100 is O(n) because:
- Choose c = 4 and n₀ = 100
- For n ≥ 100: 3n + 100 ≤ 3n + n = 4n
- Therefore 3n + 100 ≤ 4n for all n ≥ 100

The constants c and n₀ exist, so f(n) = O(n).

## Why we drop constants and lower-order terms

Big-O focuses on growth rate, not exact values. This is why we simplify:

- **O(2n) = O(n)**: The factor of 2 is a constant.
- **O(n + 100) = O(n)**: The 100 is a lower-order term.
- **O(n² + n) = O(n²)**: The n term grows slower than n².
- **O(5n³ + 2n² + 7n + 1000) = O(n³)**: Only the dominant term matters.

This simplification is justified because:

1. **Constants depend on hardware and implementation.** The same algorithm runs at different speeds on different machines, but the growth rate stays the same.

2. **Lower-order terms become negligible.** When n is large, n² dominates n. When n = 1,000,000, the term n² is a trillion while n is only a million.

3. **We care about scalability.** Big-O tells us how an algorithm behaves as data grows, which is more useful than exact operation counts.

## Common Big-O complexities

Here are the complexity classes you will encounter most often, from fastest to slowest:

| Big-O | Name | Example Operations |
|-------|------|-------------------|
| O(1) | Constant | Array access, hash lookup |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Linear search, single loop |
| O(n log n) | Linearithmic | Efficient sorting (merge sort, heap sort) |
| O(n²) | Quadratic | Nested loops, simple sorting |
| O(n³) | Cubic | Triple nested loops |
| O(2ⁿ) | Exponential | Recursive subsets |
| O(n!) | Factorial | Generating permutations |

## Rules for calculating Big-O

### Rule 1: Drop constants

```javascript
// Both are O(n), not O(2n) or O(3n)
function doubleLoop(arr) {
  for (let i = 0; i < arr.length; i++) { /* ... */ }
  for (let i = 0; i < arr.length; i++) { /* ... */ }
}
```

Two sequential O(n) loops make O(2n), which simplifies to O(n).

### Rule 2: Drop lower-order terms

```javascript
// O(n²), not O(n² + n)
function nestedWithExtra(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length; j++) { /* O(n²) total */ }
  }
  for (let i = 0; i < arr.length; i++) { /* O(n) total */ }
}
```

O(n²) + O(n) = O(n² + n) = O(n²).

### Rule 3: Different inputs use different variables

```javascript
// O(a + b), not O(n)
function processBoth(arrA, arrB) {
  for (const item of arrA) { /* ... */ }  // O(a)
  for (const item of arrB) { /* ... */ }  // O(b)
}

// O(a × b), not O(n²)
function processNested(arrA, arrB) {
  for (const itemA of arrA) {
    for (const itemB of arrB) { /* ... */ }
  }
}
```

When inputs are independent, keep their sizes separate.

### Rule 4: Nested loops multiply

```javascript
// O(n × m)
function printGrid(rows, cols) {
  for (let i = 0; i < rows; i++) {      // O(rows)
    for (let j = 0; j < cols; j++) {    // O(cols) per row
      console.log(i, j);
    }
  }
}
```

If both dimensions come from the same input, this becomes O(n²).

### Rule 5: Sequential operations add

```javascript
// O(n) + O(n log n) + O(n) = O(n log n)
function processData(arr) {
  const sum = arr.reduce((a, b) => a + b, 0);  // O(n)
  arr.sort((a, b) => a - b);                    // O(n log n)
  for (const item of arr) { console.log(item); } // O(n)
}
```

The dominant term O(n log n) wins.

## Big-O for common operations

Knowing the Big-O of standard operations helps you analyze code quickly:

**Array operations:**
- Access by index: O(1)
- Push/pop (end): O(1) amortized
- Shift/unshift (start): O(n)
- includes, indexOf, find: O(n)
- slice, concat: O(n)
- sort: O(n log n)

**Object/Map operations:**
- Get/set by key: O(1) average
- Delete: O(1) average
- Object.keys/values: O(n)

**Set operations:**
- add, has, delete: O(1) average

**String operations:**
- Access by index: O(1)
- Concatenation: O(n) where n is result length
- slice, substring: O(n)
- includes, indexOf: O(n × m) where m is search string length

## Analyzing code examples

**Example 1: Simple search**

```javascript
function contains(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return true;
  }
  return false;
}
// O(n) - worst case checks every element
```

**Example 2: Finding duplicates**

```javascript
function hasDuplicate(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) return true;
    }
  }
  return false;
}
// O(n²) - compares every pair
```

**Example 3: Using a hash set**

```javascript
function hasDuplicateFast(arr) {
  const seen = new Set();
  for (const item of arr) {
    if (seen.has(item)) return true;
    seen.add(item);
  }
  return false;
}
// O(n) - single pass with O(1) lookups
```

**Example 4: Recursive Fibonacci**

```javascript
function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
}
// O(2ⁿ) - exponential due to repeated calculations
```

**Example 5: Memoized Fibonacci**

```javascript
function fibMemo(n, memo = {}) {
  if (n in memo) return memo[n];
  if (n <= 1) return n;
  memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
  return memo[n];
}
// O(n) - each value computed once
```

## Big-O vs. actual performance

Big-O describes asymptotic behavior, not actual speed. Keep in mind:

**Constants matter for practical sizes.** An O(n) algorithm with a constant factor of 1000 is slower than an O(n log n) algorithm with a constant factor of 1 for inputs smaller than about 10,000.

**Cache behavior matters.** An O(n) algorithm that accesses memory sequentially can be faster than an O(1) algorithm that accesses memory randomly, due to CPU cache effects.

**Big-O describes worst case.** An algorithm that is O(n²) in the worst case might typically run in O(n) time for real inputs. Quick sort is O(n²) worst case but O(n log n) average case.

**Language and runtime overhead.** The same algorithm has different constant factors in different languages. JavaScript objects have more overhead than C structs.

Use Big-O for comparing algorithms and predicting scalability. Use benchmarking for measuring actual performance on your specific inputs.

## Common mistakes

**Assuming O(1) means "fast."** An O(1) operation might still be slow if the constant is large. But it will not get slower as input grows.

**Ignoring hidden loops.** Methods like `includes`, `indexOf`, and `slice` are O(n). Calling them inside a loop creates O(n²).

```javascript
// Accidentally O(n²)
function removeAll(arr, target) {
  while (arr.includes(target)) {  // O(n) per call
    arr.splice(arr.indexOf(target), 1);  // O(n) per call
  }
}
```

**Confusing best and worst case.** When asked for Big-O, give the worst case unless specified otherwise. Linear search is O(n), not O(1).

**Forgetting recursion depth.** Recursive calls add to space complexity. A recursive function with depth n uses O(n) stack space even if it does not allocate any data structures.

## Summary

- **Big-O notation** describes an upper bound on algorithmic growth rate.
- It answers: "In the worst case, how does resource usage scale with input size?"
- **Drop constants** (O(2n) = O(n)) and **lower-order terms** (O(n² + n) = O(n²)).
- **Nested loops multiply**, **sequential operations add**.
- Use **different variables** for independent inputs (O(a × b), not O(n²)).
- Big-O describes scalability, not actual speed; benchmarking measures real performance.
- Watch for **hidden loops** in library methods that appear to be O(1).
