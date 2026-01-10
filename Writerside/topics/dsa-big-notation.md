# Big-θ Notation

Reading time: 8 minutes

**Big-Theta (Θ) notation** provides a tight bound on an algorithm's growth rate. While Big-O gives an upper bound ("no worse than") and Big-Omega gives a lower bound ("no better than"), Big-Theta says the algorithm grows at exactly this rate, asymptotically speaking.

When you can characterize an algorithm with Big-Theta, you have the most precise description of its complexity.

## What Big-Theta means

An algorithm is **Θ(f(n))** if its growth rate is bounded both above and below by f(n). This means:

- The algorithm grows **at least as fast** as f(n) (lower bound).
- The algorithm grows **no faster than** f(n) (upper bound).
- The bounds are tight: the algorithm truly grows at this rate.

For example, if an algorithm is Θ(n), it will always do work proportional to n, neither significantly more nor significantly less.

## The formal definition

**f(n) = Θ(g(n))** if and only if there exist positive constants **c₁**, **c₂**, and **n₀** such that:

**c₁ · g(n) ≤ f(n) ≤ c₂ · g(n)** for all **n ≥ n₀**

In plain language: f(n) is Θ(g(n)) if f(n) is sandwiched between two constant multiples of g(n) for sufficiently large n.

Equivalently, f(n) = Θ(g(n)) if and only if:
- f(n) = O(g(n)), AND
- f(n) = Ω(g(n))

Big-Theta is the intersection of Big-O and Big-Omega.

## Example: proving Θ(n)

Consider the function f(n) = 3n + 5.

To show f(n) = Θ(n), we need both bounds:

**Upper bound (Big-O):**
- For n ≥ 5: 3n + 5 ≤ 3n + n = 4n
- So f(n) ≤ 4n, meaning f(n) = O(n)

**Lower bound (Big-Omega):**
- For all n ≥ 1: 3n + 5 ≥ 3n
- So f(n) ≥ 3n, meaning f(n) = Ω(n)

Since f(n) = O(n) and f(n) = Ω(n), we conclude f(n) = Θ(n).

The function 3n + 5 grows exactly linearly, no matter what constants we add.

## When Big-Theta applies

Big-Theta applies when an algorithm's best, worst, and average cases all have the same growth rate, or when we are characterizing a specific case precisely.

**Algorithms with tight bounds:**

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

This function performs n² iterations no matter what, making it Θ(n²).

**Merge sort is Θ(n log n):**

Merge sort always divides the array in half (log n levels) and does linear work at each level (n comparisons). This holds for any input, making merge sort Θ(n log n) in all cases.

## When Big-Theta does not apply

Big-Theta is inappropriate when best and worst cases differ:

```javascript
// O(n), but not Θ(n)
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) return i;
  }
  return -1;
}
```

This function is:
- **Best case: Θ(1)** when the target is first
- **Worst case: Θ(n)** when the target is last or absent

The algorithm is O(n) (upper bound), but not Θ(n) because the best case is better than linear. You could say it is Θ(n) in the worst case specifically.

**Quick sort varies by case:**
- Best/average case: Θ(n log n)
- Worst case: Θ(n²)

Since the cases differ, we cannot give a single Big-Theta for quick sort without specifying which case.

## Big-Theta vs. Big-O in practice

In everyday engineering discussions, Big-O dominates for several reasons:

1. **Worst case focus.** Engineers typically want to know "how bad can it get?" Big-O answers this directly.

2. **Conservative estimates.** Big-O is safe; it never underestimates. If an algorithm is O(n²), it will not suddenly become worse than quadratic.

3. **Convenience.** Saying "O(n)" is easier than explaining "Θ(n) in the worst case but Θ(1) in the best case."

4. **Interview conventions.** Technical interviews almost always expect Big-O answers.

However, Big-Theta is more precise when it applies. Saying merge sort is Θ(n log n) is more informative than saying it is O(n log n), because the latter technically allows it to be O(n) or O(1), which it is not.

## Misuse of Big-O when Big-Theta applies

Technically, you can give loose Big-O bounds:
- Merge sort is O(n³) (true but useless)
- Binary search is O(n) (true but misleading)

These statements are mathematically correct because Big-O is an upper bound. But they hide the algorithm's true performance.

When you know a tight bound, prefer Big-Theta or at least use Big-O to indicate the tight bound:
- "Merge sort is O(n log n)" (understood to mean Θ(n log n) in practice)
- "Binary search is O(log n)" (understood to be tight)

The convention is that when engineers say "O(f(n))," they usually mean the tightest known upper bound, which often equals Θ(f(n)).

## Big-Theta for well-known algorithms

| Algorithm | Time Complexity |
|-----------|-----------------|
| Array access | Θ(1) |
| Binary search | Θ(log n) worst case |
| Linear scan | Θ(n) |
| Merge sort | Θ(n log n) all cases |
| Heap sort | Θ(n log n) all cases |
| Matrix multiplication (naive) | Θ(n³) |

These algorithms have the same complexity across all cases, making Big-Theta the right notation.

## Summary

- **Big-Theta (Θ)** describes a tight bound: the algorithm grows at exactly this rate.
- An algorithm is Θ(f(n)) if it is both O(f(n)) and Ω(f(n)).
- Use Big-Theta when **all cases** (best, worst, average) have the same growth rate.
- When best and worst cases differ, Big-Theta applies only to a specific case.
- In practice, **Big-O is more common**, but Big-Theta is more precise when applicable.
- Many algorithms (merge sort, heap sort, linear scan) are naturally Θ because their work is predictable.
