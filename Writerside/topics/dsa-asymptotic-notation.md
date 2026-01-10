# Asymptotic Notation

Reading time: 8 minutes

**Asymptotic notation** is the mathematical language computer scientists use to describe how algorithms scale. Instead of measuring exact running times (which depend on hardware, language, and implementation details), asymptotic notation captures the growth rate of an algorithm's resource usage as input size approaches infinity.

The word "asymptotic" refers to the behavior of a function as its input grows without bound. When analyzing algorithms, we care less about how they perform on small inputs and more about their fundamental scaling characteristics. Asymptotic notation gives us precise tools to express these characteristics.

## Why we need special notation

Imagine you have two algorithms:

- Algorithm A takes `2n + 100` operations
- Algorithm B takes `n² + 5` operations

For small inputs (n < 10), Algorithm B might actually be faster because 100 is a larger constant than 5. But as n grows, the n² term in Algorithm B dominates, making it much slower than Algorithm A.

| n | Algorithm A (2n + 100) | Algorithm B (n² + 5) |
|---|------------------------|----------------------|
| 5 | 110 | 30 |
| 10 | 120 | 105 |
| 50 | 200 | 2,505 |
| 100 | 300 | 10,005 |
| 1,000 | 2,100 | 1,000,005 |

Asymptotic notation lets us express the essential truth: Algorithm A grows linearly while Algorithm B grows quadratically. We write A is O(n) and B is O(n²), ignoring the constants and lower-order terms that matter less at scale.

## The three main notations

Computer scientists use three related notations to describe algorithmic complexity:

**Big-O (O)** describes an upper bound. It says "this algorithm grows no faster than this rate." When someone says an algorithm is "O(n²)," they mean its running time is at most proportional to n² for large inputs. Big-O is the most commonly used notation in everyday engineering discussions.

**Big-Omega (Ω)** describes a lower bound. It says "this algorithm grows at least this fast." If an algorithm is Ω(n), it must examine at least a linear number of elements. Lower bounds are useful for proving that no algorithm can do better than a certain rate for a given problem.

**Big-Theta (Θ)** describes a tight bound. It says "this algorithm grows exactly at this rate." An algorithm is Θ(n log n) if it is both O(n log n) and Ω(n log n). Big-Theta gives the most precise characterization when both bounds match.

## How the notations relate

These notations form a hierarchy:

- If an algorithm is **Θ(f(n))**, it is also **O(f(n))** and **Ω(f(n))**.
- If an algorithm is **O(f(n))**, it might be faster (the bound might not be tight).
- If an algorithm is **Ω(f(n))**, it might be slower (it is at least this fast, possibly slower).

Think of it like describing a person's height:

- O(6 feet) means "at most 6 feet tall"
- Ω(5 feet) means "at least 5 feet tall"
- Θ(5.5 feet) means "exactly 5.5 feet tall" (within some tolerance)

Saying someone is O(6 feet) is technically true for anyone 6 feet or shorter, but it is not very precise. Θ gives you the exact answer when available.

## When to use each notation

**Use Big-O when:**
- Discussing worst-case performance (the most common scenario)
- You want to guarantee an upper limit on resource usage
- Communicating with other engineers (it is the industry standard)
- You do not have a matching lower bound

**Use Big-Omega when:**
- Proving lower bounds on problems (not just algorithms)
- Showing that a problem requires at least a certain amount of work
- Demonstrating that an algorithm cannot be improved beyond a point

**Use Big-Theta when:**
- You know both the upper and lower bounds match
- You want the most precise characterization
- Analyzing well-understood algorithms with known tight bounds

## The role of constants and lower-order terms

Asymptotic notation deliberately ignores:

- **Constant factors:** O(2n) = O(n) = O(100n)
- **Lower-order terms:** O(n² + n) = O(n² + 1000n + 5000) = O(n²)

This simplification is powerful because:

1. **Constants depend on implementation.** The same algorithm in C might be 10x faster than in Python, but both have the same growth rate.

2. **Lower-order terms become negligible.** When n is a million, the difference between n² and n² + n is less than 0.0001%.

3. **Growth rate is what matters at scale.** An O(n) algorithm will eventually beat an O(n²) algorithm, no matter how large the constants.

However, constants can matter in practice for moderate input sizes. An O(n) algorithm with a constant factor of 1000 is slower than an O(n log n) algorithm with a constant factor of 1 until n exceeds a threshold. Asymptotic analysis tells you what wins at scale; benchmarking tells you what wins for your specific inputs.

## Common misconceptions

**"Big-O means worst case."** Not exactly. Big-O describes an upper bound on growth rate. You can analyze best-case, average-case, or worst-case using any of the three notations. However, it is conventional to use Big-O when discussing worst-case complexity.

**"O(n) is always faster than O(n²)."** Only asymptotically. For small n or large constant factors, an O(n²) algorithm might be faster in practice. Asymptotic notation describes behavior as n approaches infinity.

**"Drop all constants."** While we drop constants in the final Big-O expression, we should understand them. An O(n) algorithm that makes 100 passes is meaningfully different from one that makes 2 passes, even though both are O(n).

## Formal mathematical definitions

For those who want precision, here are the formal definitions:

**Big-O:** f(n) = O(g(n)) if there exist positive constants c and n₀ such that f(n) ≤ c·g(n) for all n ≥ n₀.

**Big-Omega:** f(n) = Ω(g(n)) if there exist positive constants c and n₀ such that f(n) ≥ c·g(n) for all n ≥ n₀.

**Big-Theta:** f(n) = Θ(g(n)) if f(n) = O(g(n)) and f(n) = Ω(g(n)).

These definitions formalize the intuition: the bounds hold for "sufficiently large" n (beyond n₀), and we allow a constant factor c to account for implementation differences.

## How this section is organized

The following pages explore each notation in depth:

1. **Big-O Notation** covers the most widely used notation, with examples and rules for combining Big-O expressions.

2. **Big-Θ Notation** explains tight bounds and when they apply.

3. **Big-Ω Notation** covers lower bounds and their role in proving fundamental limits.

Understanding all three notations gives you a complete vocabulary for discussing algorithmic complexity with precision.

## Summary

- **Asymptotic notation** describes how algorithms scale as input size grows toward infinity.
- **Big-O (O)** gives an upper bound: the algorithm grows no faster than this rate.
- **Big-Omega (Ω)** gives a lower bound: the algorithm grows at least this fast.
- **Big-Theta (Θ)** gives a tight bound: the algorithm grows exactly at this rate.
- Constants and lower-order terms are ignored because growth rate is what matters at scale.
- Big-O is the most common in practice; Big-Theta is the most precise when available.
