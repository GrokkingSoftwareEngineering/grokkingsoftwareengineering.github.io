# Logarithmic

Reading time: 9 minutes

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

In Big-O notation, the base of the logarithm does not matter because logarithms of different bases differ only by a constant factor:

log₂(n) = log₁₀(n) / log₁₀(2) ≈ 3.32 × log₁₀(n)

Since Big-O ignores constants, O(log₂ n) = O(log₁₀ n) = O(ln n) = O(log n).

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
      right = mid1 - 1;
    } else if (target > arr[mid2]) {
      left = mid2 + 1;
    } else {
      left = mid1 + 1;
      right = mid2 - 1;
    }
  }

  return -1;
}
```

Ternary search is O(log₃ n), which equals O(log n) since the base does not matter.

## Exponentiation by squaring

Computing xⁿ can be done in O(log n) multiplications instead of O(n):

```javascript
function power(x, n) {
  if (n === 0) return 1;
  if (n === 1) return x;

  if (n % 2 === 0) {
    const half = power(x, n / 2);
    return half * half;  // x^n = (x^(n/2))^2
  } else {
    return x * power(x, n - 1);
  }
}
```

To compute x¹⁰⁰⁰, this needs about 10 multiplications instead of 1000. The algorithm works by observing that x¹⁰⁰⁰ = (x⁵⁰⁰)², x⁵⁰⁰ = (x²⁵⁰)², and so on.

## Finding in sorted structures

Any operation that can eliminate a constant fraction of candidates is O(log n):

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

  return left;
}
```

**Finding first/last occurrence:**

```javascript
function findFirstOccurrence(arr, target) {
  let left = 0;
  let right = arr.length - 1;
  let result = -1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) {
      result = mid;
      right = mid - 1;  // Continue searching left
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return result;
}
```

## O(log n) vs other complexities

Logarithmic growth is dramatically slower than linear:

| n | O(log n) | O(n) | O(n log n) | O(n²) |
|---|----------|------|------------|-------|
| 10 | 3 | 10 | 33 | 100 |
| 100 | 7 | 100 | 664 | 10,000 |
| 1,000 | 10 | 1,000 | 9,966 | 1,000,000 |
| 1,000,000 | 20 | 1,000,000 | 19,931,569 | 10¹² |

For 1 million elements, O(log n) is 50,000 times faster than O(n).

## When O(log n) is not possible

Logarithmic complexity requires:

1. **Sorted or structured data:** Binary search needs sorted input. Tree operations need a tree structure.

2. **Random access:** Binary search needs O(1) array access. Linked lists cannot be binary searched efficiently.

3. **Comparable elements:** The algorithm must decide which half to eliminate based on comparisons.

If you must examine every element (finding the sum, checking if any element matches a condition), O(log n) is impossible. The best you can do is O(n).

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

## Practical considerations

**Constant factors matter at small scale:**

For small arrays (< 100 elements), the overhead of binary search (calculating midpoint, branch prediction misses) can make linear search faster. Many implementations switch to linear search below a threshold.

**Cache behavior:**

Binary search jumps around memory unpredictably, causing cache misses. Linear search accesses memory sequentially, which is cache-friendly. For small datasets, linear search's cache advantage can outweigh its algorithmic disadvantage.

**Preprocessing cost:**

Binary search requires sorted data. If you sort once and search many times, the O(n log n) sorting cost is amortized. If you search only once, linear search on unsorted data may be faster overall.

## Summary

- **O(log n)** algorithms eliminate a constant fraction of work at each step.
- Doubling input size adds only one step, making these algorithms highly scalable.
- The logarithm base does not matter in Big-O notation.
- **Binary search** is the classic example: O(log n) to find an element in sorted data.
- O(log n) requires structured data and random access; it cannot apply to unsorted data.
- For small inputs, O(n) may be faster due to lower overhead and better cache behavior.
