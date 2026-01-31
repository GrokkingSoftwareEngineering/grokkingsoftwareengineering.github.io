# Polynomial

Reading time: 25 minutes

**Polynomial time complexity** includes complexities of the form O(n²), O(n³), O(n⁴), and so on, where the exponent is a constant. These algorithms are considered tractable because their running time, while potentially slow, remains manageable for reasonable input sizes. The most common polynomial complexity you will encounter is **quadratic**, O(n²).

## What polynomial complexity means

A polynomial algorithm has complexity O(nᵏ) where k is a fixed constant. The running time grows as a polynomial function of the input size:

- **O(n²) quadratic:** Time grows with the square of input
- **O(n³) cubic:** Time grows with the cube of input
- **O(n⁴) quartic:** Time grows with the fourth power

For a given k, doubling the input multiplies running time by 2ᵏ:
- O(n²): doubling n multiplies time by 4
- O(n³): doubling n multiplies time by 8
- O(n⁴): doubling n multiplies time by 16

This predictable scaling, while steep, remains fundamentally different from exponential growth.

## The mathematics of polynomial growth

A polynomial function has the form f(n) = aₖnᵏ + aₖ₋₁nᵏ⁻¹ + ... + a₁n + a₀, where k is the degree. In Big-O notation, only the highest-degree term matters because it dominates for large n.

For example, f(n) = 3n² + 5n + 7 is O(n²) because:
- When n = 10: 3(100) + 5(10) + 7 = 357, and n² = 100
- When n = 1000: 3(1,000,000) + 5(1000) + 7 ≈ 3,005,007, and n² = 1,000,000
- The n² term increasingly dominates as n grows

The key insight is that the exponent determines the growth rate. O(n²) grows slower than O(n³), which grows slower than O(n⁴), regardless of constant factors.

## Quadratic complexity: O(n²)

Quadratic complexity is the most common polynomial complexity. It typically arises from nested loops where both loops iterate over the input.

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
    let swapped = false;
    for (let j = 0; j < n - i - 1; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        swapped = true;
      }
    }
    if (!swapped) break;  // Optimization: stop if sorted
  }
  return arr;
}
// O(n²) worst and average case, O(n) best case (already sorted)
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
// O(n²) in all cases
```

```javascript
function insertionSort(arr) {
  for (let i = 1; i < arr.length; i++) {
    const key = arr[i];
    let j = i - 1;
    while (j >= 0 && arr[j] > key) {
      arr[j + 1] = arr[j];
      j--;
    }
    arr[j + 1] = key;
  }
  return arr;
}
// O(n²) worst case, O(n) best case (already sorted)
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

### String matching (naive)

```javascript
function naiveStringMatch(text, pattern) {
  const results = [];
  for (let i = 0; i <= text.length - pattern.length; i++) {
    let match = true;
    for (let j = 0; j < pattern.length; j++) {
      if (text[i + j] !== pattern[j]) {
        match = false;
        break;
      }
    }
    if (match) results.push(i);
  }
  return results;
}
// O(n × m) where n = text length, m = pattern length
// When m is proportional to n, this becomes O(n²)
```

### Building adjacency matrix

```javascript
function buildAdjacencyMatrix(edges, n) {
  const matrix = Array.from({ length: n }, () =>
    new Array(n).fill(0)
  );

  for (const [from, to, weight] of edges) {
    matrix[from][to] = weight;
    matrix[to][from] = weight;  // Undirected graph
  }

  return matrix;
}
// O(n²) space, O(E) time to build
// But many operations on the matrix are O(n²)
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

Matrix multiplication is fundamental to many algorithms. The naive O(n³) algorithm is often sufficient, though faster algorithms exist (Strassen's O(n^2.807)).

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

This can be reduced to O(n²) with sorting and two-pointer technique:

```javascript
function findTripleWithSumOptimized(arr, target) {
  arr.sort((a, b) => a - b);  // O(n log n)

  for (let i = 0; i < arr.length - 2; i++) {
    let left = i + 1;
    let right = arr.length - 1;

    while (left < right) {
      const sum = arr[i] + arr[left] + arr[right];
      if (sum === target) {
        return [arr[i], arr[left], arr[right]];
      }
      if (sum < target) left++;
      else right--;
    }
  }
  return null;
}
// O(n²) - significantly better
```

### Floyd-Warshall algorithm

Finding shortest paths between all pairs of vertices:

```javascript
function floydWarshall(graph) {
  const n = graph.length;
  const dist = graph.map(row => [...row]);

  for (let k = 0; k < n; k++) {
    for (let i = 0; i < n; i++) {
      for (let j = 0; j < n; j++) {
        if (dist[i][k] + dist[k][j] < dist[i][j]) {
          dist[i][j] = dist[i][k] + dist[k][j];
        }
      }
    }
  }

  return dist;
}
// O(n³) - optimal for dense graphs
```

### LU decomposition

Solving systems of linear equations:

```javascript
function luDecomposition(A) {
  const n = A.length;
  const L = Array.from({ length: n }, () => new Array(n).fill(0));
  const U = Array.from({ length: n }, () => new Array(n).fill(0));

  for (let i = 0; i < n; i++) {
    // Upper triangular
    for (let k = i; k < n; k++) {
      let sum = 0;
      for (let j = 0; j < i; j++) {
        sum += L[i][j] * U[j][k];
      }
      U[i][k] = A[i][k] - sum;
    }

    // Lower triangular
    for (let k = i; k < n; k++) {
      if (i === k) {
        L[i][i] = 1;
      } else {
        let sum = 0;
        for (let j = 0; j < i; j++) {
          sum += L[k][j] * U[j][i];
        }
        L[k][i] = (A[k][i] - sum) / U[i][i];
      }
    }
  }

  return { L, U };
}
// O(n³)
```

## Higher-order polynomial complexity

O(n⁴) and higher arise from deeper nesting:

```javascript
// Finding quadruples that sum to target
function findQuadrupleWithSum(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      for (let k = j + 1; k < arr.length; k++) {
        for (let l = k + 1; l < arr.length; l++) {
          if (arr[i] + arr[j] + arr[k] + arr[l] === target) {
            return [arr[i], arr[j], arr[k], arr[l]];
          }
        }
      }
    }
  }
  return null;
}
// O(n⁴)
```

This can be reduced to O(n²) using hash tables:

```javascript
function findQuadrupleOptimized(arr, target) {
  const pairSums = new Map();

  // Store all pair sums: O(n²)
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      const sum = arr[i] + arr[j];
      if (!pairSums.has(sum)) {
        pairSums.set(sum, []);
      }
      pairSums.get(sum).push([i, j]);
    }
  }

  // Find complement pairs: O(n²)
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      const complement = target - arr[i] - arr[j];
      if (pairSums.has(complement)) {
        for (const [k, l] of pairSums.get(complement)) {
          if (k > j) {  // Ensure no index overlap
            return [arr[i], arr[j], arr[k], arr[l]];
          }
        }
      }
    }
  }

  return null;
}
// O(n²) - much better than O(n⁴)
```

## The growth of polynomial complexity

Polynomial algorithms become slow quickly as n increases:

| n | O(n) | O(n²) | O(n³) | O(n⁴) |
|---|------|-------|-------|-------|
| 10 | 10 | 100 | 1,000 | 10,000 |
| 100 | 100 | 10,000 | 1,000,000 | 100,000,000 |
| 1,000 | 1,000 | 1,000,000 | 1,000,000,000 | 10¹² |
| 10,000 | 10,000 | 100,000,000 | 10¹² | 10¹⁶ |

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

### Real-world guidelines

**For interactive applications (< 100ms):**
- O(n²): n ≤ 3,000
- O(n³): n ≤ 500

**For batch processing (< 1 hour):**
- O(n²): n ≤ 600,000
- O(n³): n ≤ 15,000

## Recognizing polynomial complexity

**O(n²) patterns:**

- Two nested loops over the same collection
- For each element, examining all other elements
- Building an n×n matrix or table
- Comparing all pairs

**O(n³) patterns:**

- Three nested loops
- Matrix operations on n×n matrices
- For each pair, examining all other elements
- Dynamic programming with three dimensions

**Key indicator:** The number of nested loops that iterate over input-dependent ranges equals the exponent.

```javascript
// O(n²) - two nested loops
for (let i = 0; i < n; i++) {
  for (let j = 0; j < n; j++) {
    // O(1) work
  }
}

// O(n³) - three nested loops
for (let i = 0; i < n; i++) {
  for (let j = 0; j < n; j++) {
    for (let k = 0; k < n; k++) {
      // O(1) work
    }
  }
}
```

## Avoiding unnecessary polynomial complexity

Many O(n²) algorithms can be improved:

### Finding duplicates: O(n²) → O(n)

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

### Two-sum: O(n²) → O(n)

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

### Sorting: O(n²) → O(n log n)

Replace bubble/selection/insertion sort with merge sort, quick sort, or heap sort for large datasets.

### Closest pair of points: O(n²) → O(n log n)

The naive algorithm checks all pairs. A divide-and-conquer approach achieves O(n log n).

## When polynomial complexity is acceptable

O(n²) and higher complexities are acceptable when:

1. **Input size is known to be small:** If n ≤ 100, O(n²) algorithms run instantly.

2. **The problem requires it:** Some problems have no known better algorithms. All-pairs shortest path (Floyd-Warshall) is inherently O(n³) for dense graphs.

3. **Simplicity matters:** O(n²) algorithms are often simpler to implement and debug. For small inputs, this tradeoff can be worth it.

4. **Better algorithms have high constants:** An O(n²) algorithm with a small constant can beat O(n log n) for moderate n.

5. **Memory constraints:** Sometimes the space-efficient O(n²) solution is better than the O(n) solution that requires O(n) extra space.

### Insertion sort vs merge sort

Insertion sort (O(n²)) is often faster than merge sort (O(n log n)) for small arrays:

```javascript
function hybridSort(arr) {
  if (arr.length < 32) {
    return insertionSort(arr);  // O(n²) but fast for small n
  }
  return mergeSort(arr);  // O(n log n) for large n
}
```

Many production sorting implementations use this hybrid approach.

## The tractability divide

Polynomial algorithms (O(n), O(n²), O(n³)) are considered **tractable** in complexity theory. They may be slow, but they are fundamentally solvable for reasonable inputs.

**Exponential** algorithms (O(2ⁿ), O(n!)) are **intractable**. They quickly become unsolvable regardless of hardware improvements. The gap between O(n³) and O(2ⁿ) is enormous.

| n | O(n³) | O(2ⁿ) |
|---|-------|-------|
| 10 | 1,000 | 1,024 |
| 20 | 8,000 | 1,048,576 |
| 30 | 27,000 | 1,073,741,824 |
| 50 | 125,000 | ~10¹⁵ |

At n = 50, O(n³) is still fast (125,000 operations), while O(2ⁿ) is computationally infeasible.

This distinction matters because:
- Polynomial problems can be solved exactly for practical inputs
- Exponential problems often require approximation algorithms or heuristics
- The P vs NP question asks whether this gap is fundamental

## Optimizing polynomial algorithms

Even within O(n²), constant factors matter:

### Early termination

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

### Avoiding redundant work

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

### Cache-friendly memory access

```javascript
// Cache-unfriendly: column-major access in row-major array
for (let j = 0; j < n; j++) {
  for (let i = 0; i < n; i++) {
    process(matrix[i][j]);
  }
}

// Cache-friendly: row-major access
for (let i = 0; i < n; i++) {
  for (let j = 0; j < n; j++) {
    process(matrix[i][j]);
  }
}
```

### Loop unrolling

```javascript
// Without unrolling
for (let i = 0; i < n; i++) {
  sum += arr[i];
}

// With unrolling (if n is divisible by 4)
for (let i = 0; i < n; i += 4) {
  sum += arr[i] + arr[i+1] + arr[i+2] + arr[i+3];
}
```

## Space-time tradeoffs

Sometimes you can trade O(n) space for a lower time complexity:

```javascript
// O(n²) time, O(1) space
function hasDuplicateNoSpace(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) return true;
    }
  }
  return false;
}

// O(n) time, O(n) space
function hasDuplicateWithSpace(arr) {
  const seen = new Set();
  for (const item of arr) {
    if (seen.has(item)) return true;
    seen.add(item);
  }
  return false;
}
```

The right choice depends on your constraints:
- Memory-limited systems might prefer O(n²) time with O(1) space
- Time-critical applications prefer O(n) time with O(n) space

## Summary

- **Polynomial complexity** includes O(n²), O(n³), and higher powers of n.
- **Quadratic O(n²)** is most common, arising from nested loops over input.
- **Cubic O(n³)** appears in matrix operations and three-way comparisons.
- Polynomial algorithms are **tractable** but become slow for large inputs.
- Practical limits: O(n²) for n ≤ 10,000, O(n³) for n ≤ 1,000.
- Many O(n²) problems can be solved in O(n) or O(n log n) with better algorithms.
- O(n²) is acceptable for small inputs or when no better algorithm exists.
- The gap between polynomial and exponential is the fundamental tractability divide.
- Within polynomial complexity, optimize constant factors through early termination, cache-friendly access, and avoiding redundant work.
