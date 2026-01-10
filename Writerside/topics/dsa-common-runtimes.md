# Common Runtimes

Reading time: 8 minutes

Algorithms tend to fall into a small number of **complexity classes** based on their growth rates. Knowing these common runtimes gives you an intuition for what is efficient and what is not. When someone says an algorithm is "linear" or "quadratic," you should immediately understand what that means for performance at scale.

This page provides an overview of the complexity classes you will encounter most often. Each class has distinct characteristics, typical algorithms that fall into it, and practical limits on the input sizes it can handle.

## The complexity hierarchy

Complexity classes form a hierarchy from fastest to slowest growth:

| Complexity | Name | Growth Description |
|------------|------|-------------------|
| O(1) | Constant | Does not grow with input |
| O(log n) | Logarithmic | Grows very slowly |
| O(n) | Linear | Grows proportionally |
| O(n log n) | Linearithmic | Slightly faster than quadratic |
| O(n²) | Quadratic | Grows with square of input |
| O(n³) | Cubic | Grows with cube of input |
| O(2ⁿ) | Exponential | Doubles with each additional element |
| O(n!) | Factorial | Grows faster than exponential |

The gaps between these classes are enormous. Understanding them helps you predict when an algorithm will work and when it will fail.

## Visualizing the differences

Consider how many operations each complexity class requires for various input sizes:

| n | O(1) | O(log n) | O(n) | O(n log n) | O(n²) | O(2ⁿ) |
|---|------|----------|------|------------|-------|-------|
| 10 | 1 | 3 | 10 | 33 | 100 | 1,024 |
| 100 | 1 | 7 | 100 | 664 | 10,000 | 10³⁰ |
| 1,000 | 1 | 10 | 1,000 | 9,966 | 1,000,000 | 10³⁰¹ |
| 10,000 | 1 | 13 | 10,000 | 132,877 | 100,000,000 | enormous |

The O(1) and O(log n) columns barely change. The O(n) and O(n log n) columns grow manageably. The O(n²) column grows fast but remains computable. The O(2ⁿ) column explodes beyond any practical computation for even moderate n.

## Practical limits

Assuming a computer performs about 10⁸ (100 million) simple operations per second, here are rough limits for what can be computed in one second:

| Complexity | Maximum n (1 second) |
|------------|---------------------|
| O(1) | Any |
| O(log n) | Any |
| O(n) | ~10⁸ |
| O(n log n) | ~10⁷ |
| O(n²) | ~10⁴ |
| O(n³) | ~10² - 10³ |
| O(2ⁿ) | ~25-30 |
| O(n!) | ~10-12 |

These numbers are approximations, but they illustrate why complexity matters. An O(n²) algorithm that works perfectly on 1,000 items might take hours on 100,000 items.

## Why these specific classes?

These complexity classes are not arbitrary. They arise naturally from common algorithmic patterns:

- **O(1)** comes from direct access (array indexing, hash lookups).
- **O(log n)** comes from halving the problem each step (binary search, balanced trees).
- **O(n)** comes from examining each element once (linear scan, simple loops).
- **O(n log n)** comes from divide-and-conquer with linear merging (efficient sorting).
- **O(n²)** comes from comparing all pairs (nested loops).
- **O(2ⁿ)** comes from binary choices for each element (subsets, recursive branching).
- **O(n!)** comes from considering all orderings (permutations).

Recognizing these patterns helps you estimate complexity before doing detailed analysis.

## Polynomial vs. exponential: the tractability divide

A crucial distinction exists between **polynomial** complexity (O(n), O(n²), O(n³), etc.) and **exponential** complexity (O(2ⁿ), O(n!), etc.).

Polynomial algorithms are considered **tractable**. Even O(n³) can handle thousands of elements in reasonable time. As hardware improves, polynomial algorithms scale to larger inputs.

Exponential algorithms are **intractable** for large inputs. No amount of hardware improvement will make O(2ⁿ) practical for n = 100. The growth is simply too fast.

This divide is fundamental to computational complexity theory. Many important problems have no known polynomial-time algorithms, which is why understanding complexity classes matters beyond just optimizing code.

## Comparing algorithms in the same class

Two algorithms in the same complexity class can have very different real-world performance:

- An O(n) algorithm with constant factor 2 is twice as fast as one with constant factor 4.
- An O(n log n) sorting algorithm optimized for cache locality might beat a theoretically identical one that is not.
- Implementation details, memory access patterns, and language overhead all affect actual speed.

Complexity class tells you how algorithms scale relative to each other. Within a class, benchmarking tells you which is faster for your specific use case.

## How this section is organized

The following pages examine each major complexity class in detail:

1. **Constant O(1)** covers operations that take the same time regardless of input size.

2. **Logarithmic O(log n)** covers algorithms that eliminate half the problem at each step.

3. **Linear O(n)** covers algorithms that examine each element a fixed number of times.

4. **Polynomial O(n²), O(n³)** covers algorithms with nested iteration over the input.

5. **Exponential O(2ⁿ)** covers algorithms that explore all subsets or branches.

6. **Factorial O(n!)** covers algorithms that explore all permutations.

Each page includes examples, code patterns, and guidance on when these complexities are acceptable.

## Summary

- Algorithms fall into common **complexity classes** based on their growth rates.
- The hierarchy from fastest to slowest: O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ), O(n!).
- **Polynomial** complexities (up to O(n³) or so) are tractable; **exponential** complexities are not.
- The gaps between classes are dramatic, determining what input sizes are practical.
- Within a class, constant factors and implementation details affect real performance.
- Knowing common runtimes helps you predict algorithm behavior and choose appropriate solutions.
