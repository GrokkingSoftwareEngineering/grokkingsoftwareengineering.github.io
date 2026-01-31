# Logarithmic

Reading time: 25 minutes

**Logarithmic time complexity**, written as **O(log n)**, describes algorithms that reduce the problem size by a constant fraction with each step. These algorithms are remarkably efficient: doubling the input size adds only one more step. This makes logarithmic algorithms practical even for massive datasets.

## What O(log n) means

A logarithmic algorithm eliminates a constant fraction of the remaining work at each step. The most common case is halving: each step cuts the problem in half. To reduce n elements to 1 by halving requires log₂(n) steps.

For example:
- 1,000 elements → about 10 steps
- 1,000,000 elements → about 20 steps
- 1,000,000,000 elements → about 30 steps

Doubling the input adds just one step. This is why logarithmic algorithms handle large datasets so well.

## The mathematics of logarithms

The logarithm answers: "How many times must I divide n by b to get 1?"

- log₂(8) = 3 because 8 ÷ 2 ÷ 2 ÷ 2 = 1
- log₂(1024) = 10 because 2¹⁰ = 1024
- log₁₀(1000) = 3 because 10³ = 1000

The logarithm is the inverse of exponentiation. If bˣ = n, then log_b(n) = x.

### Key properties

Understanding logarithm properties helps analyze algorithms:

**Product rule:** log(ab) = log(a) + log(b)

**Quotient rule:** log(a/b) = log(a) - log(b)

**Power rule:** log(aⁿ) = n × log(a)

**Change of base:** log_a(n) = log_b(n) / log_b(a)

The change of base formula is why logarithm bases do not matter in Big-O notation.

### Base independence in Big-O

In Big-O notation, the base of the logarithm does not matter because logarithms of different bases differ only by a constant factor:

log₂(n) = log₁₀(n) / log₁₀(2) ≈ 3.32 × log₁₀(n)

Since Big-O ignores constants, O(log₂ n) = O(log₁₀ n) = O(ln n) = O(log n).

When analyzing algorithms, we typically use base 2 because halving is the most common pattern. But the base is mathematically irrelevant to the complexity class.

## Binary search: the classic O(log n) algorithm

Binary search finds a target in a sorted array by repeatedly halving the search space:

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      return mid;
    } else if (arr[mid] < target) {
      left = mid + 1;   // Eliminate left half
    } else {
      right = mid - 1;  // Eliminate right half
    }
  }

  return -1;  // Not found
}
```

Each iteration eliminates half the remaining elements. For an array of 1 million elements, binary search needs at most 20 comparisons.

| Array Size | Maximum Comparisons |
|------------|---------------------|
| 100 | 7 |
| 1,000 | 10 |
| 1,000,000 | 20 |
| 1,000,000,000 | 30 |

### Why binary search works

Binary search relies on the sortedness of the array. When we check the middle element:

- If it equals the target, we are done
- If it is less than the target, the target must be in the right half (if it exists)
- If it is greater than the target, the target must be in the left half (if it exists)

Each comparison eliminates half the remaining candidates. This halving property is the essence of logarithmic algorithms.

### Common binary search variants

**Finding insertion point:**

```javascript
function findInsertionPoint(sortedArr, value) {
  let left = 0;
  let right = sortedArr.length;

  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (sortedArr[mid] < value) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }

  return left;  // Index where value should be inserted
}
```

**Finding first occurrence:**

```javascript
function findFirstOccurrence(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  let result = -1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) {
      result = mid;
      right = mid - 1;  // Continue searching left for earlier occurrence
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}
```

**Finding last occurrence:**

```javascript
function findLastOccurrence(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  let result = -1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) {
      result = mid;
      left = mid + 1;  // Continue searching right for later occurrence
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}
```

**Counting occurrences:**

```javascript
function countOccurrences(arr, target) {
  const first = findFirstOccurrence(arr, target);
  if (first === -1) return 0;
  const last = findLastOccurrence(arr, target);
  return last - first + 1;
}
// O(log n) - two binary searches
```

## Balanced tree operations

Balanced binary trees (AVL trees, red-black trees) maintain O(log n) height, making search, insert, and delete operations O(log n):

```javascript
// Conceptual tree search
function treeSearch(node, target) {
  if (node === null) {
    return null;
  }

  if (target === node.value) {
    return node;
  } else if (target < node.value) {
    return treeSearch(node.left, target);   // Eliminate right subtree
  } else {
    return treeSearch(node.right, target);  // Eliminate left subtree
  }
}
```

Each comparison eliminates roughly half the remaining nodes. A balanced tree with 1 million nodes has height about 20.

### Why balance matters

An unbalanced tree can degrade to a linked list:

```
Balanced tree (height 3):       Unbalanced tree (height 7):
        4                        1
       / \                        \
      2   6                        2
     / \ / \                        \
    1  3 5  7                        3
                                      \
                                       4
                                        \
                                         ...
```

The balanced tree provides O(log n) search. The unbalanced tree provides O(n) search. Balancing algorithms (AVL rotations, red-black tree rules) maintain the logarithmic height guarantee.

### Common balanced tree operations

```javascript
class BinarySearchTree {
  constructor() {
    this.root = null;
  }

  insert(value) {
    const newNode = { value, left: null, right: null };
    if (this.root === null) {
      this.root = newNode;
      return;
    }
    this.insertNode(this.root, newNode);
  }

  insertNode(node, newNode) {
    if (newNode.value < node.value) {
      if (node.left === null) {
        node.left = newNode;
      } else {
        this.insertNode(node.left, newNode);  // O(log n) if balanced
      }
    } else {
      if (node.right === null) {
        node.right = newNode;
      } else {
        this.insertNode(node.right, newNode);  // O(log n) if balanced
      }
    }
  }

  search(value) {
    return this.searchNode(this.root, value);
  }

  searchNode(node, value) {
    if (node === null) return null;
    if (value === node.value) return node;
    if (value < node.value) {
      return this.searchNode(node.left, value);  // O(log n) if balanced
    }
    return this.searchNode(node.right, value);
  }

  findMin(node = this.root) {
    if (node === null) return null;
    while (node.left !== null) {
      node = node.left;  // O(log n) if balanced
    }
    return node;
  }

  findMax(node = this.root) {
    if (node === null) return null;
    while (node.right !== null) {
      node = node.right;  // O(log n) if balanced
    }
    return node;
  }
}
```

## Divide and conquer: the O(log n) pattern

Logarithmic complexity arises from the divide-and-conquer pattern where:

1. The problem is divided into smaller subproblems
2. Only one subproblem needs to be solved
3. The subproblem is a constant fraction of the original

Binary search divides by 2 and solves one half. Some algorithms divide by other factors:

```javascript
// Ternary search: O(log₃ n) = O(log n)
function ternarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const third = Math.floor((right - left) / 3);
    const mid1 = left + third;
    const mid2 = right - third;

    if (arr[mid1] === target) return mid1;
    if (arr[mid2] === target) return mid2;

    if (target < arr[mid1]) {
      right = mid1 - 1;      // Search left third
    } else if (target > arr[mid2]) {
      left = mid2 + 1;       // Search right third
    } else {
      left = mid1 + 1;       // Search middle third
      right = mid2 - 1;
    }
  }

  return -1;
}
```

Ternary search is O(log₃ n), which equals O(log n) since the base does not matter. In practice, binary search is usually faster because it requires fewer comparisons per iteration despite more iterations.

## Exponentiation by squaring

Computing xⁿ can be done in O(log n) multiplications instead of O(n):

```javascript
function power(x, n) {
  if (n === 0) return 1;
  if (n === 1) return x;

  if (n % 2 === 0) {
    const half = power(x, n / 2);
    return half * half;  // x^n = (x^(n/2))²
  } else {
    return x * power(x, n - 1);
  }
}
```

To compute x¹⁰⁰⁰, this needs about 10 multiplications instead of 1000. The algorithm works by observing that x¹⁰⁰⁰ = (x⁵⁰⁰)², x⁵⁰⁰ = (x²⁵⁰)², and so on.

### Iterative version

```javascript
function powerIterative(x, n) {
  let result = 1;
  let base = x;
  let exp = n;

  while (exp > 0) {
    if (exp % 2 === 1) {
      result *= base;  // Odd exponent: multiply once
    }
    base *= base;      // Square the base
    exp = Math.floor(exp / 2);  // Halve the exponent
  }

  return result;
}
// O(log n) multiplications
```

This technique applies to any associative operation, not just multiplication. Matrix exponentiation uses it to compute Fibonacci numbers in O(log n).

### Matrix exponentiation for Fibonacci

```javascript
function multiplyMatrices(a, b) {
  return [
    [a[0][0] * b[0][0] + a[0][1] * b[1][0], a[0][0] * b[0][1] + a[0][1] * b[1][1]],
    [a[1][0] * b[0][0] + a[1][1] * b[1][0], a[1][0] * b[0][1] + a[1][1] * b[1][1]]
  ];
}

function matrixPower(matrix, n) {
  if (n === 1) return matrix;
  if (n % 2 === 0) {
    const half = matrixPower(matrix, n / 2);
    return multiplyMatrices(half, half);
  } else {
    return multiplyMatrices(matrix, matrixPower(matrix, n - 1));
  }
}

function fibonacciLogN(n) {
  if (n <= 1) return n;
  const base = [[1, 1], [1, 0]];
  const result = matrixPower(base, n - 1);
  return result[0][0];
}
// O(log n) - using matrix exponentiation
```

## Heap operations

Binary heaps provide O(log n) insertion and extraction:

```javascript
class MinHeap {
  constructor() {
    this.heap = [];
  }

  insert(value) {
    this.heap.push(value);
    this.bubbleUp(this.heap.length - 1);  // O(log n)
  }

  bubbleUp(index) {
    while (index > 0) {
      const parentIndex = Math.floor((index - 1) / 2);
      if (this.heap[parentIndex] <= this.heap[index]) break;
      [this.heap[parentIndex], this.heap[index]] =
        [this.heap[index], this.heap[parentIndex]];
      index = parentIndex;  // Move up one level
    }
    // Maximum iterations: tree height = log n
  }

  extractMin() {
    if (this.heap.length === 0) return undefined;
    const min = this.heap[0];
    const last = this.heap.pop();
    if (this.heap.length > 0) {
      this.heap[0] = last;
      this.bubbleDown(0);  // O(log n)
    }
    return min;
  }

  bubbleDown(index) {
    const length = this.heap.length;
    while (true) {
      const leftChild = 2 * index + 1;
      const rightChild = 2 * index + 2;
      let smallest = index;

      if (leftChild < length && this.heap[leftChild] < this.heap[smallest]) {
        smallest = leftChild;
      }
      if (rightChild < length && this.heap[rightChild] < this.heap[smallest]) {
        smallest = rightChild;
      }

      if (smallest === index) break;
      [this.heap[index], this.heap[smallest]] =
        [this.heap[smallest], this.heap[index]];
      index = smallest;  // Move down one level
    }
    // Maximum iterations: tree height = log n
  }

  peek() {
    return this.heap[0];  // O(1)
  }
}
```

Both bubbleUp and bubbleDown traverse at most log n levels of the heap.

## O(log n) vs other complexities

Logarithmic growth is dramatically slower than linear:

| n | O(log n) | O(n) | O(n log n) | O(n²) |
|---|----------|------|------------|-------|
| 10 | 3 | 10 | 33 | 100 |
| 100 | 7 | 100 | 664 | 10,000 |
| 1,000 | 10 | 1,000 | 9,966 | 1,000,000 |
| 1,000,000 | 20 | 1,000,000 | 19,931,569 | 10¹² |

For 1 million elements, O(log n) is 50,000 times faster than O(n).

### The practical impact

Consider searching through a phone book with 1 million entries:

- Linear search (O(n)): On average, examine 500,000 entries
- Binary search (O(log n)): Examine at most 20 entries

If each comparison takes 1 microsecond:
- Linear search: ~0.5 seconds average
- Binary search: ~0.00002 seconds (20 microseconds)

Binary search is 25,000 times faster.

## When O(log n) is not possible

Logarithmic complexity requires:

1. **Sorted or structured data:** Binary search needs sorted input. Tree operations need a tree structure. Without structure, you cannot eliminate half the candidates.

2. **Random access:** Binary search needs O(1) array access. Linked lists cannot be binary searched efficiently because finding the middle element requires O(n) traversal.

3. **Comparable elements:** The algorithm must decide which half to eliminate based on comparisons. This requires a total ordering on the elements.

If you must examine every element (finding the sum, checking if any element matches a condition), O(log n) is impossible. The best you can do is O(n).

### Why sorted data matters

```javascript
// O(log n) - only works on sorted data
function binarySearchSorted(sortedArr, target) {
  // Can eliminate half because we know ordering
}

// O(n) - required for unsorted data
function linearSearchUnsorted(arr, target) {
  for (const element of arr) {
    if (element === target) return true;
  }
  return false;
  // Cannot eliminate any elements without checking them
}
```

Sorting takes O(n log n), so searching an unsorted array once is faster with linear search. But if you search the same array multiple times, sorting first (O(n log n)) followed by binary searches (O(log n) each) becomes worthwhile.

## Logarithmic space complexity

Some algorithms use O(log n) space:

**Recursive binary search:**

```javascript
function binarySearchRecursive(arr, target, left = 0, right = arr.length - 1) {
  if (left > right) return -1;

  const mid = Math.floor((left + right) / 2);

  if (arr[mid] === target) return mid;
  if (arr[mid] < target) {
    return binarySearchRecursive(arr, target, mid + 1, right);
  } else {
    return binarySearchRecursive(arr, target, left, mid - 1);
  }
}
// Space: O(log n) due to recursion stack
```

The iterative version uses O(1) space, making it preferable when space matters.

**Quick sort:**

Quick sort's recursion depth is O(log n) on average, giving O(log n) space complexity for the call stack.

```javascript
function quickSort(arr, low = 0, high = arr.length - 1) {
  if (low < high) {
    const pivotIndex = partition(arr, low, high);
    quickSort(arr, low, pivotIndex - 1);   // Recursive call
    quickSort(arr, pivotIndex + 1, high);  // Recursive call
  }
  return arr;
  // Stack depth: O(log n) average, O(n) worst case
}

function partition(arr, low, high) {
  const pivot = arr[high];
  let i = low - 1;
  for (let j = low; j < high; j++) {
    if (arr[j] <= pivot) {
      i++;
      [arr[i], arr[j]] = [arr[j], arr[i]];
    }
  }
  [arr[i + 1], arr[high]] = [arr[high], arr[i + 1]];
  return i + 1;
}
```

## Advanced logarithmic algorithms

### Binary search on the answer

Sometimes you binary search not on the array but on the possible answer values:

```javascript
// Find smallest x where f(x) is true
function binarySearchAnswer(low, high, predicate) {
  while (low < high) {
    const mid = Math.floor((low + high) / 2);
    if (predicate(mid)) {
      high = mid;  // Answer might be smaller
    } else {
      low = mid + 1;  // Answer must be larger
    }
  }
  return low;
}

// Example: Find minimum time to complete tasks
function minTimeToComplete(tasks, workers) {
  const predicate = (time) => canComplete(tasks, workers, time);
  return binarySearchAnswer(1, maxPossibleTime, predicate);
}
```

### Finding peak element

```javascript
function findPeakElement(arr) {
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] < arr[mid + 1]) {
      left = mid + 1;  // Peak is in right half
    } else {
      right = mid;     // Peak is at mid or in left half
    }
  }

  return left;  // Index of a peak element
}
// O(log n) - no sorting required!
```

### Square root by binary search

```javascript
function sqrt(n, precision = 0.0001) {
  let low = 0;
  let high = n;

  while (high - low > precision) {
    const mid = (low + high) / 2;
    if (mid * mid < n) {
      low = mid;
    } else {
      high = mid;
    }
  }

  return (low + high) / 2;
}
// O(log(n / precision))
```

## Practical considerations

**Constant factors matter at small scale:**

For small arrays (< 100 elements), the overhead of binary search (calculating midpoint, branch prediction misses) can make linear search faster. Many implementations switch to linear search below a threshold.

```javascript
function hybridSearch(arr, target) {
  if (arr.length < 64) {
    // Linear search for small arrays
    return arr.indexOf(target);
  }
  return binarySearch(arr, target);
}
```

**Cache behavior:**

Binary search jumps around memory unpredictably, causing cache misses. Linear search accesses memory sequentially, which is cache-friendly. For small datasets, linear search's cache advantage can outweigh its algorithmic disadvantage.

**Preprocessing cost:**

Binary search requires sorted data. If you sort once and search many times, the O(n log n) sorting cost is amortized. If you search only once, linear search on unsorted data may be faster overall.

```javascript
// Search once: linear search is faster
function singleSearch(arr, target) {
  return arr.includes(target);  // O(n)
}

// Search many times: sort first
function manySearches(arr, targets) {
  arr.sort((a, b) => a - b);  // O(n log n) once
  return targets.map(t => binarySearch(arr, t));  // O(log n) each
}
// Crossover point: when number of searches > log n
```

## Summary

- **O(log n)** algorithms eliminate a constant fraction of work at each step.
- Doubling input size adds only one step, making these algorithms highly scalable.
- The logarithm base does not matter in Big-O notation.
- **Binary search** is the classic example: O(log n) to find an element in sorted data.
- **Balanced trees** maintain O(log n) height for O(log n) operations.
- **Exponentiation by squaring** computes xⁿ in O(log n) multiplications.
- O(log n) requires structured data and random access; it cannot apply to unsorted data.
- For small inputs, O(n) may be faster due to lower overhead and better cache behavior.
- The difference between O(log n) and O(n) is dramatic: 20 steps vs 1,000,000 for a million elements.
