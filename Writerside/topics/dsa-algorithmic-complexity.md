# Algorithmic Complexity

Reading time: 10 minutes

**Algorithmic complexity** is how we measure the cost of running an algorithm. It tells you how much time and memory an algorithm requires as the size of its input grows. Understanding complexity is essential for writing efficient software: it lets you predict performance, compare different approaches, and avoid solutions that work fine in testing but fail at scale.

When you write code that processes data, you are making implicit trade-offs. A nested loop might be easy to write but slow with large inputs. A hash table lookup might be fast but use extra memory. Complexity analysis gives you a framework for reasoning about these trade-offs before you hit problems in production.

## Why complexity matters

Every operation in your program has a cost. For small inputs, the difference between a good and bad algorithm might be milliseconds. For large inputs, the difference can be minutes, hours, or "never finishes."

Consider searching for a value in a collection:

- Scanning every element one by one works, but the time grows linearly with the collection size.
- Using a sorted structure with binary search grows much slower as size increases.
- Using a hash table can give near-instant lookups regardless of size.

Complexity analysis quantifies these differences. It helps you answer questions like "Will this approach still work when we have a million users?" or "Why did the report that ran in testing take hours in production?"

## The core idea: growth rate

Complexity is not about the exact number of operations an algorithm performs. It is about how that number grows as input size increases.

Imagine two algorithms:

- Algorithm A performs `3n + 10` operations for input size `n`.
- Algorithm B performs `n²` operations for input size `n`.

For small inputs, B might actually be faster (when n = 2, A does 16 operations and B does 4). But as n grows, B quickly becomes impractical:

| Input size (n) | Algorithm A (3n + 10) | Algorithm B (n²) |
|----------------|----------------------|------------------|
| 10             | 40                   | 100              |
| 100            | 310                  | 10,000           |
| 1,000          | 3,010                | 1,000,000        |
| 10,000         | 30,010               | 100,000,000      |

The constant factors (like the 3 and 10 in Algorithm A) matter less and less as input grows. What matters is the dominant term, the part of the formula that grows fastest. This insight is the foundation of asymptotic analysis.

## Time complexity vs. space complexity

Algorithms consume two resources: time (CPU cycles) and space (memory). Complexity analysis applies to both.

**Time complexity** measures how the running time grows with input size. Most discussions of "complexity" default to time complexity because slow programs are the most visible problem.

**Space complexity** measures how much memory an algorithm needs. Some algorithms trade time for space (caching results to avoid recomputation) while others trade space for time (using compact representations that require more processing).

The best choice depends on your constraints. A memory-limited embedded device might need space-efficient algorithms even if they are slower. A server with abundant RAM might prefer faster algorithms that use more memory.

## Best case, worst case, and average case

An algorithm can perform differently depending on its input:

- **Best case** is the most favorable input. For many algorithms, this is trivial and not very informative.
- **Worst case** is the most expensive input. This is what you usually care about because it bounds how bad things can get.
- **Average case** is the expected performance across typical inputs. This requires assumptions about what "typical" means.

When you see complexity quoted (like "binary search is O(log n)"), it usually refers to the worst case unless stated otherwise. Worst-case analysis is conservative: if you design for the worst, you know the system will handle anything.

However, average-case analysis matters when worst cases are rare or avoidable. Hash tables, for example, have poor worst-case performance but excellent average-case performance under reasonable assumptions.

## Asymptotic notation: the language of complexity

To describe growth rates, computer scientists use a family of mathematical notations:

- **Big-O (O)** gives an upper bound. It says "the algorithm grows no faster than this."
- **Big-Omega (Ω)** gives a lower bound. It says "the algorithm grows at least this fast."
- **Big-Theta (Θ)** gives a tight bound. It says "the algorithm grows exactly at this rate."

Big-O is by far the most common in everyday engineering discussions. When someone says "this algorithm is O(n log n)," they mean its running time grows at most proportionally to n log n as input size increases.

These notations abstract away constant factors and lower-order terms. They capture the shape of the growth curve, not its exact position. This makes them useful for comparing algorithms at scale, even when the exact constants depend on hardware or implementation details.

## Common complexity classes

Algorithms tend to fall into a handful of common complexity classes. Knowing these gives you an intuition for what is fast and what is slow:

- **Constant O(1):** Cost does not depend on input size. Hash table lookups and array indexing are examples.
- **Logarithmic O(log n):** Cost grows slowly as input doubles. Binary search is the classic example.
- **Linear O(n):** Cost grows proportionally to input size. Scanning every element of a list is linear.
- **Linearithmic O(n log n):** Slightly worse than linear. Efficient sorting algorithms like merge sort fall here.
- **Polynomial O(n²), O(n³):** Cost grows as a power of input size. Nested loops often produce polynomial complexity.
- **Exponential O(2ⁿ):** Cost doubles with each additional input element. Brute-force solutions to combinatorial problems often have exponential complexity.
- **Factorial O(n!):** Cost grows faster than exponential. Generating all permutations is factorial.

The gap between these classes is dramatic. An O(n) algorithm might process a million items in a second, while an O(n²) algorithm takes over 11 days for the same input. Understanding where your algorithm falls helps you predict its limits.

## Complexity in practice

Theoretical complexity is a guide, not a guarantee. In practice, several factors affect real performance:

- **Constant factors:** An O(n) algorithm with a large constant might be slower than an O(n log n) algorithm with a small constant for realistic input sizes.
- **Cache behavior:** Memory access patterns affect performance. Array-based algorithms often outperform pointer-based ones due to cache locality, even when their theoretical complexity is the same.
- **Implementation overhead:** The simplest algorithm is sometimes fastest for small inputs, even if it has worse asymptotic complexity.
- **Input characteristics:** Some algorithms perform very differently depending on the input distribution.

Complexity analysis tells you what matters at scale. Benchmarking tells you what matters for your specific inputs and hardware. Both are useful.

## Amortized complexity

Some operations are usually cheap but occasionally expensive. Dynamic arrays (like JavaScript arrays or Python lists) are a good example: most appends are constant time, but occasionally the array must resize, which is linear.

**Amortized analysis** spreads the cost of expensive operations across many cheap ones. A dynamic array has O(1) amortized append time because the expensive resizes are rare enough that they average out.

This concept matters when you care about throughput over many operations rather than the latency of any single operation.

## How this section is organized

This section builds your understanding of complexity from the ground up:

1. **Time vs Space Complexity** explores the two dimensions of algorithmic cost and when to prioritize each.

2. **How to Calculate Complexity** teaches you to analyze code and derive its complexity step by step.

3. **Asymptotic Notation** covers Big-O, Big-Theta, and Big-Omega in depth, explaining what each notation means and when to use it.

4. **Common Runtimes** examines each complexity class in detail, from constant to factorial, with examples of algorithms that fall into each category.

By the end of this section, you will be able to look at an algorithm and estimate its complexity, compare different approaches quantitatively, and make informed decisions about which solution fits your constraints.

## Summary

- **Algorithmic complexity** measures how an algorithm's resource consumption grows with input size.
- **Time complexity** measures running time growth; **space complexity** measures memory growth.
- **Asymptotic notation** (Big-O, Big-Theta, Big-Omega) describes growth rates while ignoring constant factors.
- Algorithms fall into common **complexity classes** that differ dramatically in scalability.
- Complexity analysis guides decisions about which algorithm to use, especially at scale.
- Practical performance also depends on constant factors, cache behavior, and input characteristics.
