# Big-O Notation

Reading time: 25 minutes

**Big-O notation** is the most widely used tool for describing algorithmic complexity. It provides an upper bound on the growth rate of an algorithm, answering the question: "In the worst case, how does this algorithm's resource usage scale with input size?"

When engineers discuss algorithm performance, they almost always use Big-O. Understanding it is essential for technical interviews, code reviews, and system design discussions. This page covers Big-O in depth, from the formal definition to practical application.

## What Big-O means

Big-O notation describes an upper bound on an algorithm's growth rate. When we say an algorithm is **O(n)**, we mean:

- The algorithm's running time (or space usage) grows **at most** linearly with input size.
- For sufficiently large inputs, the actual cost is bounded above by some constant multiple of n.
- The algorithm might be faster than O(n) in some cases, but it will never be slower.

The "O" stands for "order of" and is sometimes read as "on the order of." O(n²) means "on the order of n squared."

### Upper bound, not exact bound

A crucial point: Big-O gives an upper bound, not an exact bound. If an algorithm is O(n²):
- It might actually be Θ(n²) — exactly quadratic
- It might be Θ(n) — linear, which is also bounded by n²
- It might even be O(1) — constant, which is also bounded by n²

When we say "this algorithm is O(n²)," we are stating a guarantee: it will not be worse than quadratic. We are not necessarily saying it is exactly quadratic.

In practice, engineers try to give the tightest upper bound they can prove. Saying binary search is O(n) is technically true but unhelpful; saying it is O(log n) is more informative.

## The formal definition

For the mathematically inclined, here is the precise definition:

**f(n) = O(g(n))** if and only if there exist positive constants **c** and **n₀** such that:

**f(n) ≤ c · g(n)** for all **n ≥ n₀**

In plain language: f(n) is O(g(n)) if, past some starting point n₀, f(n) is always bounded above by a constant multiple of g(n).

### Understanding the constants

**The constant c** accounts for implementation differences. One implementation might do 2n operations while another does 10n operations. Both are O(n) because they differ only by a constant factor.

**The threshold n₀** accounts for small-input behavior. For small n, lower-order terms or constants might dominate. The definition only cares about large-n behavior.

### Proving Big-O bounds

To prove f(n) = O(g(n)), find specific values of c and n₀ that satisfy the inequality.

**Example 1:** Prove 3n + 100 = O(n)

We need: 3n + 100 ≤ c·n for all n ≥ n₀

Choose c = 4 and n₀ = 100:
- For n ≥ 100: 3n + 100 ≤ 3n + n = 4n ✓
- (We used the fact that 100 ≤ n when n ≥ 100)

Therefore, 3n + 100 = O(n).

**Example 2:** Prove n² + 5n + 1000 = O(n²)

We need: n² + 5n + 1000 ≤ c·n² for all n ≥ n₀

Choose c = 7 and n₀ = 15:
- For n ≥ 15: 5n ≤ n² (since 5 ≤ n)
- For n ≥ 15: 1000 ≤ 5n² (since 1000/5 = 200 ≤ n² = 225)
- So n² + 5n + 1000 ≤ n² + n² + 5n² = 7n² ✓

Therefore, n² + 5n + 1000 = O(n²).

**Example 3:** Prove 2ⁿ is NOT O(n²)

Suppose 2ⁿ = O(n²). Then there exist c and n₀ such that 2ⁿ ≤ c·n² for all n ≥ n₀.

But 2ⁿ grows faster than any polynomial. For any fixed c, there exists some n where 2ⁿ > c·n².

Contradiction. Therefore, 2ⁿ ≠ O(n²).

## Why we drop constants and lower-order terms

Big-O focuses on growth rate, not exact values. This is why we simplify:

- **O(2n) = O(n)**: The factor of 2 is a constant.
- **O(n + 100) = O(n)**: The 100 is a lower-order term.
- **O(n² + n) = O(n²)**: The n term grows slower than n².
- **O(5n³ + 2n² + 7n + 1000) = O(n³)**: Only the dominant term matters.

### Why this simplification is valid

**Constants depend on hardware and implementation.** The same algorithm runs at different speeds on different machines. A fast computer might have c = 1 while a slow one has c = 100. Both execute the same algorithm with the same growth rate.

**Lower-order terms become negligible.** When n is large, the dominant term overwhelms everything else:
- At n = 1,000: n² = 1,000,000 while n = 1,000. The ratio is 1000:1.
- At n = 1,000,000: n² = 10¹² while n = 10⁶. The ratio is 1,000,000:1.

**We care about scalability.** Big-O tells us how an algorithm behaves as data grows. The question "will this work with 10x more data?" is more important than "exactly how many milliseconds will it take?"

### When constants DO matter

While Big-O ignores constants, real performance does not:

**For small inputs:** An O(n²) algorithm with small constants might beat an O(n log n) algorithm with large constants for inputs under a few hundred elements.

**Within the same complexity class:** When comparing two O(n) algorithms, constants decide which is faster.

**In tight performance requirements:** When you need sub-millisecond response times, constant factors matter significantly.

Use Big-O for strategic decisions (which algorithm?). Use benchmarking for tactical decisions (which implementation?).

## Common Big-O complexity classes

Here are the complexity classes you will encounter most often, from fastest to slowest:

| Big-O | Name | Example Operations | Practical Limit |
|-------|------|-------------------|-----------------|
| O(1) | Constant | Array access, hash lookup | Unlimited |
| O(log n) | Logarithmic | Binary search | Billions |
| O(n) | Linear | Linear search, single loop | Millions |
| O(n log n) | Linearithmic | Efficient sorting | Millions |
| O(n²) | Quadratic | Nested loops, simple sorting | Thousands |
| O(n³) | Cubic | Triple nested loops | Hundreds |
| O(2ⁿ) | Exponential | Recursive subsets | ~25 |
| O(n!) | Factorial | Generating permutations | ~12 |

The "practical limit" column shows roughly how large n can be before the algorithm becomes impractically slow (assuming ~10⁸ operations per second and a few seconds of acceptable wait time).

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

When inputs are independent, keep their sizes separate. Only use O(n²) when both dimensions come from the same input.

### Rule 4: Nested operations multiply

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

If both dimensions come from the same input (like a square matrix where rows = cols = n), this becomes O(n²).

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

### Rule 6: Conditional branches take the worst case

```javascript
// O(n²) because of the worst-case branch
function conditionalProcess(arr, flag) {
  if (flag) {
    // O(n) branch
    for (const item of arr) { console.log(item); }
  } else {
    // O(n²) branch
    for (let i = 0; i < arr.length; i++) {
      for (let j = 0; j < arr.length; j++) {
        console.log(arr[i], arr[j]);
      }
    }
  }
}
```

Big-O describes worst case. Even if the O(n²) branch rarely executes, the function is O(n²).

## Big-O for common operations

Knowing the Big-O of standard operations helps you analyze code quickly:

### Array operations

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| `arr[i]` (access) | O(1) | Direct memory access |
| `arr.push(x)` | O(1) amortized | Occasionally O(n) for resize |
| `arr.pop()` | O(1) | Remove from end |
| `arr.shift()` | O(n) | Must shift all elements |
| `arr.unshift(x)` | O(n) | Must shift all elements |
| `arr.splice(i, k)` | O(n) | May need to shift elements |
| `arr.includes(x)` | O(n) | Linear search |
| `arr.indexOf(x)` | O(n) | Linear search |
| `arr.find(fn)` | O(n) | Scans until found |
| `arr.filter(fn)` | O(n) | Scans all elements |
| `arr.map(fn)` | O(n) | Transforms all elements |
| `arr.slice()` | O(n) | Copies elements |
| `arr.concat(other)` | O(n + m) | Creates new array |
| `arr.sort()` | O(n log n) | Comparison-based sort |

### Object and Map operations

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| `obj[key]` | O(1) average | Hash table lookup |
| `obj.key = val` | O(1) average | Hash table insert |
| `delete obj.key` | O(1) average | Hash table delete |
| `key in obj` | O(1) average | Hash table lookup |
| `Object.keys(obj)` | O(n) | Iterates all keys |
| `Object.values(obj)` | O(n) | Iterates all values |
| `Object.entries(obj)` | O(n) | Iterates all entries |
| `Map.get(key)` | O(1) average | Hash table lookup |
| `Map.set(key, val)` | O(1) average | Hash table insert |
| `Map.has(key)` | O(1) average | Hash table lookup |

### Set operations

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| `Set.add(x)` | O(1) average | Hash table insert |
| `Set.has(x)` | O(1) average | Hash table lookup |
| `Set.delete(x)` | O(1) average | Hash table delete |

### String operations

| Operation | Time Complexity | Notes |
|-----------|-----------------|-------|
| `str[i]` | O(1) | Direct access |
| `str.length` | O(1) | Stored property |
| `str + other` | O(n + m) | Creates new string |
| `str.slice(i, j)` | O(j - i) | Creates substring |
| `str.indexOf(sub)` | O(n × m) | Worst case for naive search |
| `str.includes(sub)` | O(n × m) | Worst case |
| `str.split(sep)` | O(n) | Scans entire string |

## Analyzing code examples

### Example 1: Simple linear search

```javascript
function contains(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return true;
  }
  return false;
}
// O(n) - worst case checks every element
```

The loop runs at most n times, with O(1) work per iteration. Total: O(n).

### Example 2: Finding duplicates with nested loops

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

The nested loops compare n(n-1)/2 pairs = O(n²).

### Example 3: Finding duplicates with a hash set

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

One loop, O(1) operations per iteration. Total: O(n).

### Example 4: Recursive Fibonacci (exponential)

```javascript
function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
}
// O(2ⁿ) - exponential due to repeated calculations
```

Each call spawns two more calls, creating a binary tree with 2ⁿ nodes.

### Example 5: Memoized Fibonacci (linear)

```javascript
function fibMemo(n, memo = {}) {
  if (n in memo) return memo[n];
  if (n <= 1) return n;
  memo[n] = fibMemo(n - 1, memo) + fibMemo(n - 2, memo);
  return memo[n];
}
// O(n) - each value computed once
```

Each of the n values is computed exactly once. Total: O(n).

### Example 6: Binary search

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  return -1;
}
// O(log n) - halves search space each iteration
```

Each iteration eliminates half the remaining elements. After log₂(n) iterations, only one element remains.

### Example 7: Merge sort

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));

  return merge(left, right);
}

function merge(left, right) {
  const result = [];
  let i = 0, j = 0;

  while (i < left.length && j < right.length) {
    if (left[i] <= right[j]) {
      result.push(left[i++]);
    } else {
      result.push(right[j++]);
    }
  }

  return result.concat(left.slice(i)).concat(right.slice(j));
}
// O(n log n) - log n levels, O(n) work per level
```

The recursion has depth log n. At each level, the total work across all calls is O(n). Total: O(n log n).

## Big-O vs. actual performance

Big-O describes asymptotic behavior, not actual speed. Keep in mind:

### Constants matter for practical sizes

An O(n) algorithm with a constant factor of 1000 is slower than an O(n log n) algorithm with a constant factor of 1 for inputs smaller than about 10,000.

For small inputs, the "slower" algorithm might be faster in practice.

### Cache behavior matters

Modern CPUs access memory hierarchically. Cache hits are 100x faster than cache misses.

An O(n) algorithm that accesses memory sequentially can vastly outperform an O(n) algorithm that accesses memory randomly, because the sequential version benefits from cache prefetching.

### Big-O describes worst case (usually)

An algorithm that is O(n²) in the worst case might typically run in O(n) time for real inputs:
- Quick sort is O(n²) worst case but O(n log n) average case
- Hash table lookup is O(n) worst case but O(1) average case

Know when average case is more relevant than worst case.

### Language and runtime overhead

The same algorithm has different constant factors in different languages:
- JavaScript objects have more overhead than C structs
- Python loops are slower than C loops
- JIT compilation can change performance characteristics

Big-O is language-agnostic; real performance is not.

## Common mistakes

### Assuming O(1) means "fast"

O(1) means constant time—it does not get slower as input grows. But the constant might be large. An O(1) operation that takes 100ms is still O(1), just slow.

O(1) guarantees scalability, not speed.

### Ignoring hidden loops

Many built-in methods contain hidden loops:

```javascript
// Accidentally O(n²)
function removeAll(arr, target) {
  while (arr.includes(target)) {  // O(n) per call
    arr.splice(arr.indexOf(target), 1);  // O(n) per call
  }
}
```

This looks like a simple loop, but `includes` and `indexOf` are each O(n). If there are k occurrences of target, this is O(k × n) = O(n²) worst case.

### Ignoring string concatenation in loops

```javascript
// O(n²) due to string immutability
function buildString(n) {
  let result = '';
  for (let i = 0; i < n; i++) {
    result += 'a';  // Creates new string each time
  }
  return result;
}

// O(n) - use array and join
function buildStringFast(n) {
  const chars = [];
  for (let i = 0; i < n; i++) {
    chars.push('a');
  }
  return chars.join('');
}
```

String concatenation creates a new string. In a loop, this can be O(1 + 2 + 3 + ... + n) = O(n²).

### Confusing best and worst case

When asked for Big-O without qualification, give the worst case:
- Linear search is O(n), not O(1) (even though best case is O(1))
- Quick sort is O(n²), not O(n log n) (unless you specify average case)

### Forgetting recursion stack space

Recursive calls consume stack space:

```javascript
function countdown(n) {
  if (n <= 0) return;
  console.log(n);
  countdown(n - 1);
}
// Time: O(n), Space: O(n) due to call stack
```

Even though the function allocates no data structures, the call stack grows to depth n.

### Treating all loops as O(n)

Not all loops are O(n):

```javascript
// O(log n), not O(n)
let i = n;
while (i > 1) {
  i = Math.floor(i / 2);
}

// O(√n), not O(n)
for (let i = 1; i * i <= n; i++) {
  // ...
}
```

Analyze how the loop variable changes to determine iteration count.

## Big-O in interviews

Technical interviews frequently test Big-O analysis. Common patterns:

### "What is the time complexity?"

Give the worst-case Big-O. Be prepared to explain your reasoning.

### "Can you do better?"

If you gave an O(n²) solution, the interviewer might be looking for O(n log n) or O(n). Consider:
- Can sorting help?
- Can a hash table reduce lookup time?
- Can two pointers eliminate a nested loop?

### "What about space complexity?"

Do not forget to analyze space. Trade-offs are common:
- O(n) space to achieve O(n) time (hash table)
- O(1) space at the cost of O(n²) time (no auxiliary storage)

### "What are the trade-offs?"

Be ready to discuss:
- Time vs. space
- Worst case vs. average case
- Simplicity vs. optimality

## Summary

Big-O notation describes an upper bound on algorithmic growth rate. It is the primary tool for discussing algorithm complexity.

Key takeaways:

- **Big-O gives an upper bound:** O(n²) means "no worse than quadratic."
- **Formal definition:** f(n) = O(g(n)) if f(n) ≤ c·g(n) for large n.
- **Drop constants:** O(2n) = O(n). O(100n) = O(n).
- **Drop lower-order terms:** O(n² + n) = O(n²).
- **Nested loops multiply:** Two nested loops are O(n²).
- **Sequential operations add:** O(n) + O(n log n) = O(n log n).
- **Use different variables for independent inputs:** O(a × b), not O(n²).
- **Watch for hidden loops:** Built-in methods often have O(n) complexity.
- **Big-O describes scalability, not speed:** Use benchmarking for actual performance.
- **In interviews:** Give worst case, analyze space too, discuss trade-offs.

Big-O is a thinking tool. It helps you reason about algorithms without getting lost in implementation details. Master it, and you will make better design decisions throughout your engineering career.
