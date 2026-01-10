# Polynomial

Reading time: 9 minutes

**Polynomial time complexity** includes complexities of the form O(n²), O(n³), O(n⁴), and so on, where the exponent is a constant. These algorithms are considered tractable because their running time, while potentially slow, remains manageable for reasonable input sizes. The most common polynomial complexity you will encounter is **quadratic**, O(n²).

## What polynomial complexity means

A polynomial algorithm has complexity O(nᵏ) where k is a fixed constant. The running time grows as a polynomial function of the input size:

- **O(n²) quadratic:** Time grows with the square of input
- **O(n³) cubic:** Time grows with the cube of input
- **O(n⁴) quartic:** Time grows with the fourth power

For a given k, doubling the input multiplies running time by 2ᵏ:
- O(n²): doubling n multiplies time by 4
- O(n³): doubling n multiplies time by 8

## Quadratic complexity: O(n²)

Quadratic complexity is the most common polynomial complexity. It typically arises from nested loops where both loops iterate over the input:

### Comparing all pairs

```javascript
function findPairWithSum(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] + arr[j] === target) {
        return [i, j];
      }
    }
  }
  return null;
}
// O(n²) - checks every pair
```

The outer loop runs n times, the inner loop runs up to n-1 times each. Total comparisons: n(n-1)/2 = O(n²).

### Simple sorting algorithms

Bubble sort, selection sort, and insertion sort are all O(n²):

```javascript
function bubbleSort(arr) {
  const n = arr.length;
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
      }
    }
  }
  return arr;
}
// O(n²) worst and average case
```

```javascript
function selectionSort(arr) {
  for (let i = 0; i < arr.length; i++) {
    let minIdx = i;
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[j] < arr[minIdx]) {
        minIdx = j;
      }
    }
    [arr[i], arr[minIdx]] = [arr[minIdx], arr[i]];
  }
  return arr;
}
// O(n²)
```

### Checking for duplicates (naive)

```javascript
function hasDuplicateNaive(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        return true;
      }
    }
  }
  return false;
}
// O(n²) - can be reduced to O(n) with a hash set
```

## Cubic complexity: O(n³)

Cubic complexity arises from three nested loops:

### Matrix multiplication (naive)

```javascript
function matrixMultiply(A, B) {
  const n = A.length;
  const C = Array(n).fill(null).map(() => Array(n).fill(0));

  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      for (let k = 0; k < n; k++) {
        C[i][j] += A[i][k] * B[k][j];
      }
    }
  }
  return C;
}
// O(n³) for n×n matrices
```

### Finding triplets

```javascript
function findTripleWithSum(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      for (let k = j + 1; k < arr.length; k++) {
        if (arr[i] + arr[j] + arr[k] === target) {
          return [arr[i], arr[j], arr[k]];
        }
      }
    }
  }
  return null;
}
// O(n³)
```

## The growth of polynomial complexity

Polynomial algorithms become slow quickly as n increases:

| n | O(n) | O(n²) | O(n³) |
|---|------|-------|-------|
| 10 | 10 | 100 | 1,000 |
| 100 | 100 | 10,000 | 1,000,000 |
| 1,000 | 1,000 | 1,000,000 | 1,000,000,000 |
| 10,000 | 10,000 | 100,000,000 | 1,000,000,000,000 |

Assuming 10⁸ operations per second:
- O(n²) with n = 10,000 takes about 1 second
- O(n²) with n = 100,000 takes about 100 seconds
- O(n³) with n = 1,000 takes about 10 seconds
- O(n³) with n = 10,000 takes about 10,000 seconds (almost 3 hours)

## Practical limits for polynomial algorithms

| Complexity | Practical limit (1 second) |
|------------|---------------------------|
| O(n²) | n ≈ 10,000 |
| O(n³) | n ≈ 1,000 |
| O(n⁴) | n ≈ 300 |
| O(n⁵) | n ≈ 100 |

These are rough estimates assuming 10⁸ operations per second and reasonable constants.

## Recognizing polynomial complexity

**O(n²) patterns:**

- Two nested loops over the same collection
- For each element, examining all other elements
- Building an n×n matrix or table

**O(n³) patterns:**

- Three nested loops
- Matrix operations on n×n matrices
- For each pair, examining all other elements

**Key indicator:** The number of nested loops that iterate over input-dependent ranges equals the exponent.

## Avoiding unnecessary polynomial complexity

Many O(n²) algorithms can be improved:

**Finding duplicates: O(n²) → O(n)**

```javascript
// O(n²)
function hasDuplicateSlow(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) return true;
    }
  }
  return false;
}

// O(n)
function hasDuplicateFast(arr) {
  const seen = new Set();
  for (const item of arr) {
    if (seen.has(item)) return true;
    seen.add(item);
  }
  return false;
}
```

**Two-sum: O(n²) → O(n)**

```javascript
// O(n²)
function twoSumSlow(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] + arr[j] === target) return [i, j];
    }
  }
  return null;
}

// O(n)
function twoSumFast(arr, target) {
  const seen = new Map();
  for (let i = 0; i < arr.length; i++) {
    const complement = target - arr[i];
    if (seen.has(complement)) return [seen.get(complement), i];
    seen.set(arr[i], i);
  }
  return null;
}
```

**Sorting: O(n²) → O(n log n)**

Replace bubble/selection/insertion sort with merge sort, quick sort, or heap sort for large datasets.

## When polynomial complexity is acceptable

O(n²) and higher complexities are acceptable when:

1. **Input size is known to be small:** If n ≤ 100, O(n²) algorithms run instantly.

2. **The problem requires it:** Some problems have no known better algorithms. All-pairs shortest path (Floyd-Warshall) is inherently O(n³).

3. **Simplicity matters:** O(n²) algorithms are often simpler to implement and debug. For small inputs, this tradeoff can be worth it.

4. **Better algorithms have high constants:** An O(n²) algorithm with a small constant can beat O(n log n) for moderate n.

## The tractability divide

Polynomial algorithms (O(n), O(n²), O(n³)) are considered **tractable** in complexity theory. They may be slow, but they are fundamentally solvable for reasonable inputs.

**Exponential** algorithms (O(2ⁿ), O(n!)) are **intractable**. They quickly become unsolvable regardless of hardware improvements. The gap between O(n³) and O(2ⁿ) is enormous.

This distinction matters because:
- Polynomial problems can be solved exactly for practical inputs
- Exponential problems often require approximation algorithms or heuristics
- The P vs NP question asks whether this gap is fundamental

## Optimizing polynomial algorithms

Even within O(n²), constant factors matter:

**Early termination:**

```javascript
function hasPairWithSum(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] + arr[j] === target) {
        return true;  // Exit early when found
      }
    }
  }
  return false;
}
```

**Avoiding redundant work:**

```javascript
// Slower: recalculates i*i each j iteration
for (let i = 0; i < n; i++) {
  for (let j = 0; j < n; j++) {
    result += i * i + j;
  }
}

// Faster: calculates i*i once per i
for (let i = 0; i < n; i++) {
  const iSquared = i * i;
  for (let j = 0; j < n; j++) {
    result += iSquared + j;
  }
}
```

## Summary

- **Polynomial complexity** includes O(n²), O(n³), and higher powers of n.
- **Quadratic O(n²)** is most common, arising from nested loops over input.
- Polynomial algorithms are **tractable** but become slow for large inputs.
- Practical limits: O(n²) for n ≤ 10,000, O(n³) for n ≤ 1,000.
- Many O(n²) problems can be solved in O(n) or O(n log n) with better algorithms.
- O(n²) is acceptable for small inputs or when no better algorithm exists.
- The gap between polynomial and exponential is the fundamental tractability divide.
