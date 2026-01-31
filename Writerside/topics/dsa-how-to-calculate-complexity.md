# How to Calculate Complexity?

Reading time: 25 minutes

Calculating complexity is a skill that improves with practice. The goal is to look at code and determine how its resource usage grows as input size increases. This page teaches you a systematic approach to analyzing both time and space complexity, with examples that build from simple to complex.

By the end of this page, you will be able to analyze loops, nested loops, recursive functions, and common algorithm patterns to derive their Big-O complexity.

## The basic approach

To calculate complexity, follow these steps:

1. **Identify the input size.** What variable represents "n"? It might be the length of an array, the number of nodes in a tree, or the digits in a number.

2. **Count the fundamental operations.** Focus on the operations that dominate: loop iterations, recursive calls, comparisons, or data structure operations.

3. **Express the count as a function of n.** Write down how many times the key operations execute in terms of the input size.

4. **Simplify using asymptotic notation.** Drop constants and lower-order terms to get the Big-O expression.

Let's apply this systematically to increasingly complex examples.

## Analyzing simple statements

Individual statements that do not depend on input size are **O(1)**, constant time:

```javascript
// All O(1) operations
let x = 5;
let y = x + 10;
let z = arr[0];  // Array index access
let exists = set.has(value);  // Hash set lookup (average case)
let first = list.shift();  // BUT this is O(n) for arrays!
```

No matter how large the input, constant-time operations take the same amount of work. Even if a line does complex arithmetic, if it does not depend on input size, it is O(1).

**Important caveats:**

- Array index access `arr[i]` is O(1).
- Hash table operations (`get`, `set`, `has`) are O(1) average case, but O(n) worst case.
- Array methods like `shift`, `unshift`, `splice`, `indexOf`, `includes` are O(n) because they may scan or shift elements.
- String operations often iterate through characters and are O(length).

Always understand what built-in operations actually do under the hood.

## Analyzing loops

Loops are where complexity usually comes from. The key question is: how many times does the loop body execute?

### Simple loop over n elements: O(n)

```javascript
function sum(arr) {
  let total = 0;
  for (let i = 0; i < arr.length; i++) {  // Runs n times
    total += arr[i];  // O(1) per iteration
  }
  return total;
}
// Total: O(n)
```

The loop runs `n` times (where n is the array length), and each iteration does O(1) work. Total: O(n).

### Loop that skips elements: still O(n)

```javascript
function sumEveryOther(arr) {
  let total = 0;
  for (let i = 0; i < arr.length; i += 2) {  // Runs n/2 times
    total += arr[i];
  }
  return total;
}
// Total: O(n/2) = O(n)
```

This runs n/2 times, but O(n/2) simplifies to O(n). Constants are dropped in Big-O.

### Loop with early exit: O(n) worst case

```javascript
function findIndex(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;  // Might exit early
  }
  return -1;
}
// Worst case: O(n), Best case: O(1)
```

The best case is O(1) if the target is first, but the worst case (target not present) is O(n). We typically report worst-case complexity unless stated otherwise.

### Multiple sequential loops: O(n)

```javascript
function processArray(arr) {
  // First pass: O(n)
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
  }

  // Second pass: O(n)
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i] * 2);
  }

  // Third pass: O(n)
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i] * 3);
  }
}
// Total: O(n) + O(n) + O(n) = O(3n) = O(n)
```

Three sequential O(n) loops are still O(n). The constant 3 is dropped.

## Analyzing nested loops

Nested loops multiply their iteration counts:

### Two nested loops: O(n²)

```javascript
function printPairs(arr) {
  for (let i = 0; i < arr.length; i++) {      // n iterations
    for (let j = 0; j < arr.length; j++) {    // n iterations each
      console.log(arr[i], arr[j]);
    }
  }
}
// Total: O(n × n) = O(n²)
```

The outer loop runs n times. For each outer iteration, the inner loop runs n times. Total: n × n = n².

### Triangular nested loop: still O(n²)

```javascript
function printUniquePairs(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {  // Starts at i+1
      console.log(arr[i], arr[j]);
    }
  }
}
// Total: O(n²)
```

The inner loop runs n-1, then n-2, ..., then 1, then 0 times.

Total iterations: (n-1) + (n-2) + ... + 1 + 0 = n(n-1)/2 = (n² - n)/2

This simplifies to O(n²/2) = O(n²). The constant 1/2 is dropped.

### Triple nested loop: O(n³)

```javascript
function printTriples(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length; j++) {
      for (let k = 0; k < arr.length; k++) {
        console.log(arr[i], arr[j], arr[k]);
      }
    }
  }
}
// Total: O(n³)
```

Each additional nesting level multiplies by n.

### Nested loops with different bounds

When loops iterate over different variables, multiply their sizes:

```javascript
function printGrid(rows, cols, grid) {
  for (let i = 0; i < rows; i++) {        // r iterations
    for (let j = 0; j < cols; j++) {      // c iterations
      console.log(grid[i][j]);
    }
  }
}
// Total: O(rows × cols) or O(r × c)
```

If rows and cols are both derived from the same input n (like a square matrix), this becomes O(n²). If they are independent, keep them separate: O(r × c).

### Nested loop with inner loop depending on outer

```javascript
function triangularPrint(n) {
  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= i; j++) {  // j goes up to i, not n
      console.log(i, j);
    }
  }
}
// Total: 1 + 2 + 3 + ... + n = n(n+1)/2 = O(n²)
```

The sum 1 + 2 + 3 + ... + n is n(n+1)/2, which is O(n²).

## Analyzing logarithmic loops

Loops that cut the problem size in half (or multiply by a constant) each iteration are **O(log n)**:

### Halving loop: O(log n)

```javascript
function countHalvings(n) {
  let count = 0;
  while (n > 1) {
    n = Math.floor(n / 2);  // Halve each iteration
    count++;
  }
  return count;
}
// Total: O(log n)
```

Starting at n and halving until reaching 1 takes log₂(n) steps.

### Doubling loop: O(log n)

```javascript
function countDoublings(n) {
  let count = 0;
  let i = 1;
  while (i < n) {
    i = i * 2;  // Doubles each iteration
    count++;
  }
  return count;
}
// Total: O(log n)
```

Starting at 1 and doubling until reaching n also takes log₂(n) steps.

### Binary search: O(log n)

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) {
      left = mid + 1;  // Eliminate left half
    } else {
      right = mid - 1;  // Eliminate right half
    }
  }
  return -1;
}
// Total: O(log n)
```

Each iteration halves the search space. To reduce n elements to 1, you need log₂(n) halvings.

### Dividing by constants other than 2

```javascript
function divideByThree(n) {
  let count = 0;
  while (n > 1) {
    n = Math.floor(n / 3);
    count++;
  }
  return count;
}
// Total: O(log₃ n) = O(log n)
```

Dividing by 3 gives log₃(n) iterations. But log₃(n) = log₂(n) / log₂(3), and 1/log₂(3) is just a constant. So O(log₃ n) = O(log n).

The base of the logarithm does not matter in Big-O notation because different bases differ only by a constant factor.

## Analyzing O(n log n) patterns

The complexity O(n log n) arises from several common patterns:

### Linear loop with logarithmic inner work

```javascript
function processWithBinarySearch(arr, targets) {
  arr.sort((a, b) => a - b);  // O(n log n)

  let count = 0;
  for (const target of targets) {  // O(n) iterations (if targets.length = n)
    if (binarySearch(arr, target) !== -1) {  // O(log n) per search
      count++;
    }
  }
  return count;
}
// Total: O(n log n) + O(n × log n) = O(n log n)
```

### Logarithmic outer loop with linear inner work

```javascript
function processLevels(arr) {
  let size = arr.length;
  while (size > 1) {  // O(log n) iterations
    for (let i = 0; i < arr.length; i++) {  // O(n) work per level
      // Process element
    }
    size = Math.floor(size / 2);
  }
}
// Total: O(log n × n) = O(n log n)
```

### Divide and conquer

Algorithms that split the problem in half, solve both halves, and combine results in linear time are O(n log n):

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));   // T(n/2)
  const right = mergeSort(arr.slice(mid));     // T(n/2)

  return merge(left, right);  // O(n) to merge
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
// Total: O(n log n)
```

The recurrence is T(n) = 2T(n/2) + O(n), which solves to O(n log n).

## Analyzing sequential code

When code sections run one after another, add their complexities:

```javascript
function process(arr) {
  // Section 1: O(n)
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
  }

  // Section 2: O(n²)
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length; j++) {
      console.log(arr[i], arr[j]);
    }
  }

  // Section 3: O(n)
  for (let i = 0; i < arr.length; i++) {
    console.log(arr[i]);
  }
}
// Total: O(n) + O(n²) + O(n) = O(n² + 2n) = O(n²)
```

When adding complexities, the largest term dominates. O(n) + O(n²) = O(n²).

## Analyzing recursive functions

Recursion requires careful analysis. Determine:
1. How many recursive calls are made per invocation?
2. How does the input size change with each call?
3. How much work is done per call (excluding recursive calls)?
4. What is the maximum depth of recursion?

### Linear recursion: O(n)

```javascript
function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);  // One recursive call, n decreases by 1
}
// Calls: n, Work per call: O(1)
// Total: O(n)
```

There are n calls, each doing O(1) work. Total: O(n).

### Binary recursion (tree recursion): O(2ⁿ)

```javascript
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);  // Two recursive calls
}
// Total: O(2ⁿ)
```

Each call spawns two more calls, creating a binary tree of calls. The tree has depth n, so approximately 2ⁿ nodes (calls) total.

### Divide and conquer with two halves: O(n log n)

```javascript
function divideAndConquer(arr, left, right) {
  if (left >= right) return;

  const mid = Math.floor((left + right) / 2);

  divideAndConquer(arr, left, mid);       // T(n/2)
  divideAndConquer(arr, mid + 1, right);  // T(n/2)

  // O(n) work to combine
  for (let i = left; i <= right; i++) {
    // Process element
  }
}
// Recurrence: T(n) = 2T(n/2) + O(n)
// Total: O(n log n)
```

### Single recursive call with linear work: O(n²)

```javascript
function recursiveSum(arr, index = 0) {
  if (index >= arr.length) return 0;

  // O(n) work at each level
  let sum = 0;
  for (let i = index; i < arr.length; i++) {
    sum += arr[i];
  }

  return sum + recursiveSum(arr, index + 1);  // One recursive call
}
// Recurrence: T(n) = T(n-1) + O(n)
// Total: O(n²)
```

The recurrence T(n) = T(n-1) + O(n) gives O(n + (n-1) + (n-2) + ... + 1) = O(n²).

## The Master Theorem

The **Master Theorem** provides a formula for solving recurrences of the form:

**T(n) = a × T(n/b) + O(n^d)**

Where:
- **a** = number of recursive calls
- **b** = factor by which input shrinks
- **d** = exponent of work done outside recursion

The solution depends on comparing log_b(a) with d:

| Condition | Solution |
|-----------|----------|
| log_b(a) < d | T(n) = O(n^d) |
| log_b(a) = d | T(n) = O(n^d × log n) |
| log_b(a) > d | T(n) = O(n^(log_b(a))) |

### Master Theorem examples

**Merge Sort:** T(n) = 2T(n/2) + O(n)
- a = 2, b = 2, d = 1
- log₂(2) = 1 = d
- Solution: O(n log n) ✓

**Binary Search:** T(n) = T(n/2) + O(1)
- a = 1, b = 2, d = 0
- log₂(1) = 0 = d
- Solution: O(log n) ✓

**Strassen's Matrix Multiplication:** T(n) = 7T(n/2) + O(n²)
- a = 7, b = 2, d = 2
- log₂(7) ≈ 2.807 > 2
- Solution: O(n^2.807) ✓

**Linear Recursive Scan:** T(n) = 2T(n/2) + O(1)
- a = 2, b = 2, d = 0
- log₂(2) = 1 > 0
- Solution: O(n) ✓

## Analyzing space complexity

Space complexity counts memory usage beyond the input. Consider:

### Variables and fixed allocations: O(1)

```javascript
function average(arr) {
  let sum = 0;      // O(1)
  let count = 0;    // O(1)
  for (const num of arr) {
    sum += num;
    count++;
  }
  return sum / count;
}
// Space: O(1) - constant number of variables regardless of input
```

### Creating new data structures: O(n)

```javascript
function duplicate(arr) {
  const result = [];  // Will hold n elements
  for (const item of arr) {
    result.push(item);
  }
  return result;
}
// Space: O(n) - result array grows with input
```

### Recursive call stack: O(depth)

```javascript
function countdown(n) {
  if (n <= 0) return;
  console.log(n);
  countdown(n - 1);
}
// Space: O(n) - call stack grows to depth n
```

Each recursive call adds a frame to the call stack. The space is proportional to the maximum recursion depth.

For divide and conquer:

```javascript
function binaryRecursive(arr, left, right) {
  if (left > right) return;
  const mid = Math.floor((left + right) / 2);
  // Process mid
  binaryRecursive(arr, left, mid - 1);
  binaryRecursive(arr, mid + 1, right);
}
// Space: O(log n) - maximum stack depth is log n
```

The recursion depth is O(log n) because we halve the range each time.

### Hash maps and sets: O(unique elements)

```javascript
function uniqueCount(arr) {
  const seen = new Set();
  for (const item of arr) {
    seen.add(item);
  }
  return seen.size;
}
// Space: O(n) worst case (all unique), O(1) best case (all same)
```

### Combined time and space analysis

```javascript
function buildFrequencyMap(arr) {
  const freq = new Map();  // Space: O(k) where k = unique elements

  for (const item of arr) {  // Time: O(n)
    freq.set(item, (freq.get(item) || 0) + 1);  // O(1) average
  }

  return freq;
}
// Time: O(n), Space: O(k) where k ≤ n
```

## Hidden complexity in built-in operations

Many built-in functions hide complexity. Always know what they do:

### Array operations

| Operation | Time Complexity |
|-----------|-----------------|
| `arr[i]` (access) | O(1) |
| `arr.push(x)` | O(1) amortized |
| `arr.pop()` | O(1) |
| `arr.shift()` | O(n) |
| `arr.unshift(x)` | O(n) |
| `arr.splice(i, 1)` | O(n) |
| `arr.indexOf(x)` | O(n) |
| `arr.includes(x)` | O(n) |
| `arr.slice()` | O(n) |
| `arr.concat(other)` | O(n + m) |
| `arr.sort()` | O(n log n) |
| `arr.reverse()` | O(n) |

### String operations

| Operation | Time Complexity |
|-----------|-----------------|
| `str[i]` (access) | O(1) |
| `str.length` | O(1) |
| `str + other` (concatenation) | O(n + m) |
| `str.indexOf(substr)` | O(n × m) worst case |
| `str.slice()` | O(n) |
| `str.split()` | O(n) |

### Object and Map operations

| Operation | Time Complexity |
|-----------|-----------------|
| `obj[key]` or `obj.prop` | O(1) average |
| `map.get(key)` | O(1) average |
| `map.set(key, val)` | O(1) average |
| `map.has(key)` | O(1) average |
| `Object.keys(obj)` | O(n) |
| `Object.values(obj)` | O(n) |

## Common complexity patterns cheat sheet

| Pattern | Time | Space | Example |
|---------|------|-------|---------|
| Single loop | O(n) | O(1) | Linear search, sum |
| Two sequential loops | O(n) | O(1) | Two-pass algorithms |
| Nested loops | O(n²) | O(1) | Bubble sort, all pairs |
| Triangular nested loops | O(n²) | O(1) | Unique pairs |
| Triple nested loops | O(n³) | O(1) | All triples |
| Loop halving input | O(log n) | O(1) | Binary search |
| Loop doubling counter | O(log n) | O(1) | Logarithmic counting |
| Linear loop + log work | O(n log n) | varies | Sort then binary search |
| Divide and conquer (merge) | O(n log n) | O(n) | Merge sort |
| Divide and conquer (in-place) | O(n log n) | O(log n) | Quicksort (balanced) |
| Single recursive call, O(1) work | O(n) | O(n) | Factorial |
| Two recursive calls | O(2ⁿ) | O(n) | Naive Fibonacci |
| All subsets | O(2ⁿ) | O(n) | Power set generation |
| All permutations | O(n!) | O(n) | Permutation generation |
| Building hash map/set | O(n) | O(n) | Frequency counting |

## Tips for accurate analysis

1. **Focus on the worst case** unless the problem specifically asks for average or best case.

2. **Identify the dominant term.** In O(n² + n log n + n), the n² term dominates, so it simplifies to O(n²).

3. **Drop constants.** O(2n) is just O(n). O(n²/2) is just O(n²).

4. **Watch for hidden loops.** Array methods like `slice`, `concat`, `indexOf`, and `includes` are O(n). String concatenation in a loop can be O(n²) total.

5. **Consider data structure operations.** Know the complexity of the data structures you use.

6. **Trace through with small examples.** If unsure, manually trace execution with n = 4 or n = 8 and count operations.

7. **Use the Master Theorem for divide and conquer.** Recognize recurrence patterns.

8. **Do not forget space.** Count all allocated memory, including the call stack for recursion.

9. **Consider amortized analysis.** Some operations are occasionally expensive but cheap on average (like dynamic array resizing).

10. **Verify with different inputs.** Try edge cases: empty input, single element, large input, sorted input, reverse-sorted input.

## Summary

Calculating complexity requires systematically analyzing how work grows with input size.

Key techniques:

- **Simple statements** are O(1) if they do not depend on input size.
- **Single loops** over n elements are O(n).
- **Nested loops** multiply: two levels give O(n²), three give O(n³).
- **Loops that halve** (or double) are O(log n).
- **Sequential code** adds complexities; the largest term dominates.
- **Recursive functions** require analyzing call count, work per call, and depth.
- **The Master Theorem** solves T(n) = aT(n/b) + O(n^d) recurrences.
- **Space complexity** includes variables, data structures, and the call stack.
- **Hidden complexity** lurks in built-in operations; know your library.

With practice, you will develop intuition for recognizing patterns and quickly estimating complexity. Start by analyzing small examples manually, then generalize to derive the Big-O expression.
