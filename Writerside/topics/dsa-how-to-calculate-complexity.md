# How to Calculate Complexity?

Reading time: 12 minutes

Calculating complexity is a skill that improves with practice. The goal is to look at code and determine how its resource usage grows as input size increases. This page teaches you a systematic approach to analyzing both time and space complexity, with examples that build from simple to complex.

## The basic approach

To calculate complexity, follow these steps:

1. **Identify the input size.** What variable represents "n"? It might be the length of an array, the number of nodes in a tree, or the digits in a number.

2. **Count the fundamental operations.** Focus on the operations that dominate: loop iterations, recursive calls, comparisons, or data structure operations.

3. **Express the count as a function of n.** Write down how many times the key operations execute in terms of the input size.

4. **Simplify using asymptotic notation.** Drop constants and lower-order terms to get the Big-O expression.

## Analyzing simple statements

Individual statements that do not depend on input size are **O(1)**, constant time:

```javascript
// All O(1) operations
let x = 5;
let y = x + 10;
let z = arr[0];  // Array index access
let exists = set.has(value);  // Hash set lookup
```

No matter how large the input, these operations take the same amount of time. Even if a line does "a lot" (like a complex arithmetic expression), if it does not depend on input size, it is O(1).

## Analyzing loops

Loops are where complexity usually comes from. The key question is: how many times does the loop body execute?

**Simple loop over n elements: O(n)**

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

**Loop that skips elements: still O(n)**

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

**Loop with early exit: O(n) worst case**

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

## Analyzing nested loops

Nested loops multiply their iteration counts:

**Two nested loops: O(n²)**

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

**Triangular nested loop: still O(n²)**

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

This runs n-1 + n-2 + ... + 1 + 0 = n(n-1)/2 times. That is O(n²/2), which simplifies to O(n²).

**Triple nested loop: O(n³)**

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

## Analyzing loops with different bounds

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

If rows and cols are both derived from the same input n, this becomes O(n²). If they are independent, keep them separate.

## Analyzing logarithmic loops

Loops that cut the problem size in half each iteration are **O(log n)**:

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

**Loop that doubles: also O(log n)**

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

Starting at 1 and doubling until you reach n takes log₂(n) steps.

## Analyzing O(n log n) patterns

A common pattern is a loop that runs n times, with each iteration doing O(log n) work:

```javascript
function sortAndSearch(arr, targets) {
  arr.sort((a, b) => a - b);  // O(n log n) for efficient sort

  for (const target of targets) {  // Assume targets.length = n
    binarySearch(arr, target);     // O(log n) per search
  }
}
// Total: O(n log n) + O(n log n) = O(n log n)
```

Another pattern is divide-and-conquer algorithms like merge sort, which split the input in half (log n levels) and do O(n) work at each level.

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
// Total: O(n) + O(n²) + O(n) = O(n²)
```

When adding complexities, the largest term dominates. O(n) + O(n²) = O(n²).

## Analyzing recursive functions

For recursion, determine:
1. How many recursive calls are made?
2. How much work is done per call (excluding recursive calls)?
3. What is the depth of recursion?

**Linear recursion: O(n)**

```javascript
function factorial(n) {
  if (n <= 1) return 1;
  return n * factorial(n - 1);  // One recursive call
}
// Calls: n, Work per call: O(1)
// Total: O(n)
```

There are n calls, each doing O(1) work. Total: O(n).

**Binary recursion (tree recursion): O(2ⁿ)**

```javascript
function fibonacci(n) {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);  // Two recursive calls
}
// Total: O(2ⁿ)
```

Each call spawns two more calls, creating an exponential tree of calls.

**Divide and conquer: varies**

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));   // T(n/2)
  const right = mergeSort(arr.slice(mid));     // T(n/2)

  return merge(left, right);  // O(n) work
}
// Recurrence: T(n) = 2T(n/2) + O(n)
// Solution: O(n log n)
```

The recurrence T(n) = 2T(n/2) + O(n) solves to O(n log n). This is a common pattern for divide-and-conquer algorithms.

## Analyzing space complexity

Space complexity counts memory usage. Consider:

**Variables and fixed allocations: O(1)**

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
// Space: O(1) - only a few variables regardless of input size
```

**Creating new data structures: O(n)**

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

**Recursive call stack: O(depth)**

```javascript
function countdown(n) {
  if (n <= 0) return;
  console.log(n);
  countdown(n - 1);
}
// Space: O(n) - call stack grows to depth n
```

Each recursive call adds a frame to the call stack. The space is proportional to the maximum recursion depth.

**Hash maps and sets: O(unique elements)**

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

## Common complexity patterns cheat sheet

| Pattern | Time | Space | Example |
|---------|------|-------|---------|
| Single loop | O(n) | O(1) | Linear search |
| Nested loops | O(n²) | O(1) | Bubble sort |
| Loop halving input | O(log n) | O(1) | Binary search |
| Loop × log operation | O(n log n) | varies | Sorting then searching |
| Two recursive calls | O(2ⁿ) | O(n) | Naive Fibonacci |
| Divide and conquer | O(n log n) | O(n) or O(log n) | Merge sort |
| All subsets | O(2ⁿ) | O(n) | Power set |
| All permutations | O(n!) | O(n) | Permutation generation |

## Tips for accurate analysis

1. **Focus on the worst case** unless the problem specifically asks for average or best case.

2. **Identify the dominant term.** In O(n² + n), the n² term dominates, so it simplifies to O(n²).

3. **Drop constants.** O(2n) is just O(n). O(n²/2) is just O(n²).

4. **Watch for hidden loops.** Array methods like `slice`, `concat`, `indexOf`, and `includes` are O(n). String operations often iterate through characters.

5. **Consider data structure operations.** Hash table operations are O(1) average. Tree operations are often O(log n). Array insertion at the beginning is O(n).

6. **Trace through with small examples.** If unsure, manually trace execution with n = 4 or n = 8 and count operations.

## Summary

- **Identify input size** (what is n?) and **count fundamental operations**.
- **Loops** contribute their iteration count: single loop is O(n), nested is O(n²), halving is O(log n).
- **Sequential sections** add together; the largest term dominates.
- **Recursive functions** require analyzing call count, work per call, and recursion depth.
- **Space complexity** includes variables, allocated structures, and the call stack.
- **Drop constants and lower-order terms** to get the final Big-O expression.
