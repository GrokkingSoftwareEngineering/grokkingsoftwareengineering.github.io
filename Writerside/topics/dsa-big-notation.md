# Big-θ Notation

Reading time: 25 minutes

**Big-Theta (Θ) notation** provides a tight bound on an algorithm's growth rate. While Big-O gives an upper bound ("no worse than") and Big-Omega gives a lower bound ("no better than"), Big-Theta says the algorithm grows at exactly this rate, asymptotically speaking.

When you can characterize an algorithm with Big-Theta, you have the most precise description of its complexity. This page covers Big-Theta in depth, including when it applies, how to prove it, and how it relates to Big-O and Big-Omega.

## What Big-Theta means

An algorithm is **Θ(f(n))** if its growth rate is bounded both above and below by f(n). This means:

- The algorithm grows **at least as fast** as f(n) (lower bound).
- The algorithm grows **no faster than** f(n) (upper bound).
- The bounds match, so the algorithm truly grows at this rate.

For example, if an algorithm is Θ(n), it will always do work proportional to n—neither significantly more nor significantly less. There is no input that makes it faster than linear, and no input that makes it slower than linear.

### The precision advantage

Big-Theta is more informative than Big-O or Big-Omega alone:

- Saying an algorithm is O(n²) tells you it is no worse than quadratic, but it might be linear.
- Saying an algorithm is Ω(n) tells you it takes at least linear time, but it might be exponential.
- Saying an algorithm is Θ(n log n) tells you exactly how it scales.

When you can prove a tight bound, Big-Theta is the notation to use.

## The formal definition

**f(n) = Θ(g(n))** if and only if there exist positive constants **c₁**, **c₂**, and **n₀** such that:

**c₁ · g(n) ≤ f(n) ≤ c₂ · g(n)** for all **n ≥ n₀**

In plain language: f(n) is Θ(g(n)) if f(n) is sandwiched between two constant multiples of g(n) for sufficiently large n.

### Equivalent definition

Equivalently, f(n) = Θ(g(n)) if and only if:
- f(n) = O(g(n)), AND
- f(n) = Ω(g(n))

Big-Theta is the intersection of Big-O and Big-Omega. To prove a tight bound, you must prove both the upper and lower bounds with the same function.

### The constants explained

**The constant c₁** provides the lower bound. It says f(n) is always at least c₁ times g(n).

**The constant c₂** provides the upper bound. It says f(n) is never more than c₂ times g(n).

**The threshold n₀** handles small inputs. For small n, the bounds might not hold due to constants or lower-order terms. The definition focuses on large-n behavior.

## Proving Big-Theta bounds

To prove f(n) = Θ(g(n)), find constants c₁, c₂, and n₀ that satisfy both bounds.

### Example 1: Prove 3n + 5 = Θ(n)

We need: c₁ · n ≤ 3n + 5 ≤ c₂ · n for all n ≥ n₀

**Upper bound (Big-O):**
- For n ≥ 5: 3n + 5 ≤ 3n + n = 4n
- Choose c₂ = 4, and the upper bound holds for n ≥ 5

**Lower bound (Big-Omega):**
- For all n ≥ 1: 3n + 5 ≥ 3n
- Choose c₁ = 3, and the lower bound holds for n ≥ 1

**Combined:**
- Choose c₁ = 3, c₂ = 4, n₀ = 5
- For n ≥ 5: 3n ≤ 3n + 5 ≤ 4n ✓

Therefore, 3n + 5 = Θ(n).

### Example 2: Prove n² + 10n + 1000 = Θ(n²)

**Upper bound:**
- For n ≥ 100: 10n ≤ n² and 1000 ≤ n²
- So n² + 10n + 1000 ≤ n² + n² + n² = 3n²
- Choose c₂ = 3

**Lower bound:**
- For all n ≥ 1: n² + 10n + 1000 ≥ n²
- Choose c₁ = 1

**Combined:**
- Choose c₁ = 1, c₂ = 3, n₀ = 100
- For n ≥ 100: n² ≤ n² + 10n + 1000 ≤ 3n² ✓

Therefore, n² + 10n + 1000 = Θ(n²).

### Example 3: Prove n log n = Θ(n log n)

This is trivially true by choosing c₁ = c₂ = 1 and n₀ = 1. Any function is Θ of itself.

### Example 4: Prove n² is NOT Θ(n)

For n² to be Θ(n), we would need c₁ · n ≤ n² ≤ c₂ · n for large n.

The lower bound c₁ · n ≤ n² simplifies to c₁ ≤ n. This holds for any c₁ when n is large enough.

But the upper bound n² ≤ c₂ · n simplifies to n ≤ c₂. This fails for n > c₂.

No matter what c₂ we choose, eventually n exceeds it. Contradiction.

Therefore, n² ≠ Θ(n).

## When Big-Theta applies

Big-Theta applies when an algorithm's complexity is the same across all cases—or when you are precisely characterizing a specific case.

### Algorithms with uniform complexity

Some algorithms do the same amount of work regardless of input content (only depending on input size):

```javascript
// Θ(n) - always examines every element
function sum(arr) {
  let total = 0;
  for (const num of arr) {
    total += num;
  }
  return total;
}
```

This function is Θ(n) because it always performs exactly n additions, regardless of the input values. There is no best or worst case that changes the growth rate.

```javascript
// Θ(n²) - always examines every pair
function allPairs(arr) {
  const pairs = [];
  for (let i = 0; i < arr.length; i++) {
    for (let j = 0; j < arr.length; j++) {
      pairs.push([arr[i], arr[j]]);
    }
  }
  return pairs;
}
```

This function performs n² iterations no matter what the array contains, making it Θ(n²).

### Divide-and-conquer with predictable structure

Many divide-and-conquer algorithms have tight bounds because their structure is fixed:

**Merge sort is Θ(n log n):**

Merge sort always divides the array in half (log n levels) and does linear work at each level (n comparisons total per level). This structure holds for any input, making merge sort Θ(n log n) in all cases.

```javascript
function mergeSort(arr) {
  if (arr.length <= 1) return arr;

  const mid = Math.floor(arr.length / 2);
  const left = mergeSort(arr.slice(0, mid));
  const right = mergeSort(arr.slice(mid));

  return merge(left, right);  // O(n) per level
}
// Θ(n log n) - structure is fixed regardless of input values
```

**Heap sort is Θ(n log n):**

Building the heap is Θ(n), and extracting all elements is Θ(n log n). The total is Θ(n log n), and this holds for all inputs.

### Matrix operations

Matrix algorithms often have tight bounds based on matrix dimensions:

```javascript
// Θ(n × m) - must touch every element
function sumMatrix(matrix) {
  let total = 0;
  for (let i = 0; i < matrix.length; i++) {
    for (let j = 0; j < matrix[i].length; j++) {
      total += matrix[i][j];
    }
  }
  return total;
}

// Θ(n³) for n×n matrix multiplication (naive algorithm)
function multiplyMatrices(A, B) {
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
```

## When Big-Theta does not apply

Big-Theta is inappropriate when best and worst cases have different growth rates:

### Linear search

```javascript
// O(n), but not Θ(n) for all cases
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;
  }
  return -1;
}
```

This function has:
- **Best case: Θ(1)** when the target is first
- **Worst case: Θ(n)** when the target is last or absent

The algorithm is O(n) overall, but it is not Θ(n) because some inputs allow O(1) performance. You can say it is Θ(n) **in the worst case specifically**.

### Quick sort

Quick sort's complexity varies dramatically:
- **Best/average case: Θ(n log n)**
- **Worst case: Θ(n²)**

Since the cases have different growth rates, we cannot give a single Big-Theta for quick sort without specifying which case we mean.

```javascript
function quickSort(arr, left = 0, right = arr.length - 1) {
  if (left >= right) return;

  const pivotIndex = partition(arr, left, right);
  quickSort(arr, left, pivotIndex - 1);
  quickSort(arr, pivotIndex + 1, right);
}
// Θ(n log n) average, but Θ(n²) worst case
```

### Binary search

Binary search also varies by case:
- **Best case: Θ(1)** when the target is at the middle
- **Worst case: Θ(log n)** when the target is at a leaf or absent

```javascript
function binarySearch(arr, target) {
  let left = 0, right = arr.length - 1;
  while (left <= right) {
    const mid = Math.floor((left + right) / 2);
    if (arr[mid] === target) return mid;
    if (arr[mid] < target) left = mid + 1;
    else right = mid - 1;
  }
  return -1;
}
// O(log n) overall, Θ(log n) in worst case, Θ(1) in best case
```

### Hash table operations

Hash table lookup complexity depends on collisions:
- **Average case: Θ(1)** with good hash distribution
- **Worst case: Θ(n)** if all keys hash to the same bucket

We typically say hash table lookup is O(1) average case, not Θ(1), because worst case is different.

## Big-Theta vs. Big-O in practice

In everyday engineering discussions, Big-O dominates for several reasons:

### Worst-case focus

Engineers typically want to know "how bad can it get?" Big-O answers this directly. Big-Theta answers "exactly how does it behave?", which requires more analysis and may not apply when cases differ.

### Conservative estimates

Big-O is safe—it never underestimates. If an algorithm is O(n²), it will not suddenly become worse than quadratic. You can design systems around this guarantee.

### Convenience

Saying "O(n)" is easier than explaining "Θ(n) in the worst case but Θ(1) in the best case." For quick communication, Big-O suffices.

### Interview conventions

Technical interviews almost always expect Big-O answers. Mentioning Big-Theta can show deeper understanding, but Big-O is the expected baseline.

### When Big-Theta adds value

However, Big-Theta is more precise when it applies:

- Saying merge sort is Θ(n log n) is more informative than saying it is O(n log n). The latter technically allows it to be O(n) or O(1), which merge sort is not.
- When comparing algorithms, tight bounds clarify the comparison. Two algorithms that are both "O(n log n)" might actually be Θ(n log n) and Θ(n) respectively.

## Loose Big-O vs. tight Big-O

Technically, you can give very loose Big-O bounds:
- Merge sort is O(n³) (true but useless)
- Binary search is O(n) (true but misleading)
- Array access is O(2ⁿ) (true but absurd)

These statements are mathematically correct because Big-O is an upper bound. But they hide the algorithm's true performance and are not useful.

When engineers say "O(f(n))," they usually mean the tightest known upper bound, which often equals Θ(f(n)). The convention is:

- "Merge sort is O(n log n)" is understood to mean it is also Ω(n log n), i.e., Θ(n log n).
- "Binary search is O(log n)" is understood to be the tight worst-case bound.

This informal usage treats Big-O as if it were Big-Theta when the tight bound is known. Technically imprecise, but common.

## Relationship to Big-O and Big-Omega

The three notations form a hierarchy:

### Big-Theta implies both

If f(n) = Θ(g(n)), then:
- f(n) = O(g(n)) — the upper bound holds
- f(n) = Ω(g(n)) — the lower bound holds

You can use either notation, but Θ is most informative.

### Both imply Big-Theta

If f(n) = O(g(n)) AND f(n) = Ω(g(n)), then f(n) = Θ(g(n)).

This is the standard proof strategy: prove both bounds separately, then conclude the tight bound.

### Summary table

| Notation | Meaning | Implication |
|----------|---------|-------------|
| f = Θ(g) | f grows exactly like g | f = O(g) AND f = Ω(g) |
| f = O(g) | f grows no faster than g | f could be Θ(g), Θ(log g), Θ(1), etc. |
| f = Ω(g) | f grows at least as fast as g | f could be Θ(g), Θ(g²), Θ(2^g), etc. |

## Big-Theta for common algorithms

Here are tight bounds for well-known algorithms:

### Always-tight bounds

These algorithms have the same complexity for all inputs:

| Algorithm | Time Complexity |
|-----------|-----------------|
| Array access by index | Θ(1) |
| Linear scan / Sum array | Θ(n) |
| Merge sort | Θ(n log n) all cases |
| Heap sort | Θ(n log n) all cases |
| Matrix multiplication (naive) | Θ(n³) |
| Counting sort (with range k) | Θ(n + k) |

### Case-dependent bounds

These algorithms need case specification:

| Algorithm | Best Case | Worst Case |
|-----------|-----------|------------|
| Binary search | Θ(1) | Θ(log n) |
| Linear search | Θ(1) | Θ(n) |
| Quick sort | Θ(n log n) | Θ(n²) |
| Insertion sort | Θ(n) | Θ(n²) |
| Bubble sort | Θ(n) | Θ(n²) |
| Hash table lookup | Θ(1) average | Θ(n) worst |

## Using Big-Theta correctly

### Do use Big-Theta when:

- The algorithm has uniform complexity across all cases
- You are analyzing a specific case precisely (e.g., "worst case is Θ(n²)")
- You want to emphasize that a bound is tight
- You are proving optimality (algorithm matches problem's lower bound)

### Do not use Big-Theta when:

- Best and worst cases differ and you are giving an overall characterization
- You only know the upper bound
- You are communicating casually and Big-O suffices

### Correct usage examples

- "Merge sort is Θ(n log n)" ✓ (uniform across all cases)
- "The worst case of quick sort is Θ(n²)" ✓ (specific case)
- "Linear search is Θ(n)" ✗ (best case is Θ(1), so this is imprecise)
- "Linear search is O(n)" ✓ (upper bound, applies to all cases)
- "The worst case of linear search is Θ(n)" ✓ (specific case)

## Proving optimality with Big-Theta

One powerful use of Big-Theta is proving algorithm optimality. If:
- You have an algorithm that is O(f(n))
- You can prove the problem requires Ω(f(n)) work

Then your algorithm is Θ(f(n)) and optimal.

### Example: Comparison-based sorting

**Lower bound on the problem:** Any comparison-based sorting algorithm requires Ω(n log n) comparisons in the worst case. This can be proven using decision tree analysis.

**Upper bound from algorithms:** Merge sort and heap sort achieve O(n log n).

**Conclusion:** Merge sort and heap sort are Θ(n log n) and optimal for comparison-based sorting.

### Example: Finding minimum

**Lower bound:** To find the minimum element, you must examine every element (any unexamined element could be the minimum). This requires Ω(n) work.

**Upper bound:** A single pass examining each element is O(n).

**Conclusion:** Finding the minimum is Θ(n) and the simple linear scan is optimal.

## Summary

Big-Theta provides the most precise characterization of algorithmic complexity by establishing both upper and lower bounds.

Key takeaways:

- **Big-Theta (Θ)** describes a tight bound: the algorithm grows at exactly this rate.
- **Formal definition:** c₁·g(n) ≤ f(n) ≤ c₂·g(n) for large n.
- An algorithm is **Θ(f(n)) if and only if** it is both **O(f(n))** and **Ω(f(n))**.
- Use Big-Theta when **all cases** have the same growth rate, or when characterizing a **specific case**.
- When best and worst cases differ, specify which case you mean.
- In practice, **Big-O is more common** due to worst-case focus and convenience.
- Many algorithms (merge sort, heap sort, linear scan) are **naturally Θ** because their work is input-independent.
- Big-Theta is valuable for **proving optimality**: algorithm O(f) + problem Ω(f) = optimal Θ(f).
