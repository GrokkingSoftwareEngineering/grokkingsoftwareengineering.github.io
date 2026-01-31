# Algorithmic Complexity

Reading time: 25 minutes

**Algorithmic complexity** is how we measure the cost of running an algorithm. It tells you how much time and memory an algorithm requires as the size of its input grows. Understanding complexity is essential for writing efficient software: it lets you predict performance, compare different approaches, and avoid solutions that work fine in testing but fail at scale.

When you write code that processes data, you are making implicit trade-offs. A nested loop might be easy to write but slow with large inputs. A hash table lookup might be fast but use extra memory. Complexity analysis gives you a framework for reasoning about these trade-offs before you hit problems in production.

This page introduces the core concepts of algorithmic complexity. The sub-pages that follow explore each aspect in depth: time versus space trade-offs, how to calculate complexity, the notation used to express it, and the common complexity classes you will encounter.

## Why complexity matters

Every operation in your program has a cost. For small inputs, the difference between a good and bad algorithm might be milliseconds. For large inputs, the difference can be minutes, hours, or "never finishes."

### The scaling problem

Consider a feature that works perfectly in development:

- With 100 records, it runs in 10 milliseconds. Feels instant.
- With 1,000 records, it runs in 100 milliseconds. Still acceptable.
- With 10,000 records, it runs in 10 seconds. Users start to notice.
- With 100,000 records, it runs in 1,000 seconds (over 16 minutes). Completely broken.

This pattern—working fine at small scale, failing at large scale—is the most common performance problem in software. Complexity analysis predicts these failures before they happen.

The scaling behavior depends on the algorithm's structure. Some algorithms scale gracefully; their cost grows slowly as input increases. Others scale poorly; their cost explodes as input grows. Knowing which category your algorithm falls into tells you whether it will survive in production.

### Comparing approaches before building

Complexity analysis lets you compare approaches without implementing them all. If you know that:

- Approach A has complexity that grows proportionally to input size
- Approach B has complexity that grows proportionally to the square of input size

You can predict that A will be dramatically faster for large inputs, even without writing any code. This saves time and helps you make informed architecture decisions.

### Identifying bottlenecks

When a system is slow, complexity analysis helps you find the bottleneck. If a function runs millions of times and has quadratic complexity, it is probably the problem. If another function has the same complexity but runs only once, it is probably not the issue.

Understanding complexity lets you focus optimization effort where it matters instead of speeding up code that is already fast enough.

## The core idea: growth rate

Complexity is not about the exact number of operations an algorithm performs. It is about how that number grows as input size increases.

### Why exact counts do not matter

Imagine two algorithms:

- Algorithm A performs `3n + 10` operations for input size `n`.
- Algorithm B performs `n²` operations for input size `n`.

For very small inputs, B might actually be faster:
- When n = 2: A does 16 operations, B does 4
- When n = 3: A does 19 operations, B does 9

But watch what happens as n grows:

| Input size (n) | Algorithm A (3n + 10) | Algorithm B (n²) |
|----------------|----------------------|------------------|
| 10             | 40                   | 100              |
| 100            | 310                  | 10,000           |
| 1,000          | 3,010                | 1,000,000        |
| 10,000         | 30,010               | 100,000,000      |
| 100,000        | 300,010              | 10,000,000,000   |

At n = 100,000, Algorithm B performs 33,000 times more work than Algorithm A. The constant factors (the 3 and 10 in Algorithm A) become irrelevant compared to the shape of the growth curve.

### The dominant term

Every complexity expression has a **dominant term**—the part that grows fastest and eventually overwhelms everything else.

In `3n + 10`:
- The term `3n` grows as n increases.
- The constant `10` stays fixed.
- As n gets large, `3n` dominates and `10` becomes negligible.

In `2n² + 5n + 100`:
- The term `2n²` grows fastest.
- The term `5n` grows, but slower than n².
- The constant `100` stays fixed.
- As n gets large, `2n²` dominates everything else.

Complexity analysis focuses on the dominant term because that is what determines behavior at scale.

### Ignoring constants

Complexity analysis also ignores constant multipliers. Whether an algorithm does `2n` or `10n` operations, it is classified the same way: linear complexity.

This might seem imprecise, but it is intentional. Constants depend on:
- Hardware speed
- Compiler optimizations
- Implementation details
- Programming language

These vary between systems. The growth rate—how cost changes with input size—is a more fundamental property of the algorithm itself.

## The two dimensions: time and space

Algorithms consume two resources: time (CPU cycles) and space (memory). Complexity analysis applies to both.

### Time complexity

**Time complexity** measures how the running time grows with input size. When people discuss "complexity" without qualification, they usually mean time complexity.

Time complexity matters because:
- Slow programs frustrate users
- Slow background jobs delay results
- Slow operations block other work
- At scale, slow algorithms may never finish

Time is the most visible resource. Users experience slowness directly.

### Space complexity

**Space complexity** measures how much memory an algorithm needs. This includes:
- Memory for input data
- Additional memory the algorithm allocates
- Memory for intermediate results
- Memory for output

Space complexity matters because:
- Memory is finite; exceed it and programs crash
- Memory is expensive, especially in cloud environments
- Memory access patterns affect cache performance
- Memory pressure affects other programs on the system

### Time-space trade-offs

Many algorithms offer a trade-off: use more memory to run faster, or use less memory at the cost of speed.

Consider finding duplicates in a list:

**Space-efficient approach:** For each element, scan the rest of the list to check for duplicates. Uses minimal extra memory but requires comparing every pair of elements. Time grows quadratically; space stays constant.

**Time-efficient approach:** Build a hash set of seen elements. For each element, check if it is in the set. Uses extra memory for the set but checks duplicates quickly. Time grows linearly; space also grows linearly.

Neither approach is universally better. The right choice depends on your constraints:
- If memory is scarce, use the space-efficient approach
- If time is critical, use the time-efficient approach
- If both matter, find a compromise

The "Time vs Space Complexity" sub-page explores these trade-offs in detail.

## Best case, worst case, and average case

An algorithm can perform differently depending on its input. The same code might run quickly on one input and slowly on another.

### Defining the cases

**Best case:** The input that causes the algorithm to do the least work. For a search algorithm, this might be when the target is the first element checked.

**Worst case:** The input that causes the algorithm to do the most work. For a search, this is when the target is last or not present at all.

**Average case:** The expected performance across all possible inputs, weighted by how likely each input is. This requires assumptions about the input distribution.

### Which case matters?

**Worst-case analysis** is most common for several reasons:

- It provides a guarantee. If the worst case is acceptable, all cases are acceptable.
- It is easier to analyze. You do not need to assume anything about input distributions.
- It is safer. Adversaries may intentionally provide worst-case inputs.

When you see complexity quoted without qualification (like "binary search is O(log n)"), it typically refers to the worst case.

**Average-case analysis** matters when:

- Worst cases are very rare
- You have reliable knowledge about typical inputs
- Worst-case bounds are too pessimistic to be useful

Hash tables are a classic example. Their worst-case lookup is slow (every element in one bucket), but their average-case lookup is fast (elements spread evenly). In practice, we rely on average-case behavior because worst cases are rare with good hash functions.

**Best-case analysis** is rarely useful. Knowing that an algorithm is fast on the most favorable input tells you little about its general behavior. Best-case complexity is often trivially small (like O(1) for finding an element that happens to be first).

### Amortized analysis

Some algorithms have occasional expensive operations embedded in a sequence of cheap ones. Dynamic arrays are the classic example:

- Most appends are cheap: just store the element.
- Occasionally, the array is full and must resize, copying all elements.

If you analyze each operation independently, you might conclude that appends are expensive. But **amortized analysis** spreads the cost of expensive operations across many cheap ones.

For dynamic arrays, the expensive resize operations are rare enough that when averaged over many appends, each append costs O(1). This is the **amortized cost**.

Amortized analysis is useful when:
- You care about total throughput, not individual operation latency
- Expensive operations happen infrequently
- The algorithm is designed to spread costs over time

The key insight is that amortized cost is not the same as average case. Amortized analysis considers a specific sequence of operations, not random inputs.

## Asymptotic notation: the language of complexity

Computer scientists use mathematical notations to describe growth rates precisely. These notations capture the shape of growth curves while abstracting away implementation details.

### Big-O: upper bound

**Big-O notation** (written O(...)) gives an upper bound on growth rate. When you say an algorithm is O(n²), you mean:

"As input size grows, the cost grows no faster than proportionally to n²."

This is a worst-case guarantee. The algorithm might be faster, but it will not be worse.

Big-O is the most common notation in everyday engineering. When developers discuss "complexity," they almost always mean Big-O complexity. It answers the question: "How bad can this get?"

### Big-Omega: lower bound

**Big-Omega notation** (written Ω(...)) gives a lower bound on growth rate. When you say an algorithm is Ω(n), you mean:

"As input size grows, the cost grows at least proportionally to n."

This guarantees the algorithm takes at least a certain amount of work. It answers the question: "How good can this get?" or equivalently, "What is the inherent difficulty of this problem?"

### Big-Theta: tight bound

**Big-Theta notation** (written Θ(...)) gives a tight bound. When you say an algorithm is Θ(n log n), you mean:

"As input size grows, the cost grows exactly proportionally to n log n—no faster, no slower."

This is the most precise statement. It says the upper and lower bounds match.

### Relationships between notations

If an algorithm is Θ(n²), it is also:
- O(n²) — its upper bound is n²
- O(n³) — n³ is also an upper bound (just not tight)
- Ω(n²) — its lower bound is n²
- Ω(n) — n is also a lower bound (just not tight)

Big-Theta is the most informative. Big-O is the most commonly used because upper bounds are often what we care about most.

### Why Big-O dominates discussions

In practice, Big-O is used far more than Big-Omega or Big-Theta:

- Upper bounds are usually what engineers care about ("how bad can this get?")
- Big-O is simpler to state and analyze
- Lower bounds often require more sophisticated mathematical analysis
- Casual usage treats Big-O as meaning Big-Theta anyway

When you hear "this algorithm is O(n)," the speaker often means "this algorithm is Θ(n)"—linear growth, no better and no worse. Technically imprecise, but common.

The "Asymptotic Notation" sub-page covers these notations rigorously with formal definitions and examples.

## Common complexity classes

Algorithms tend to fall into a handful of recognizable complexity classes. Knowing these gives you intuition for what is practical and what is not.

### Constant: O(1)

Cost does not change with input size. Whether you have 10 items or 10 million, the operation takes the same amount of work.

Characteristics:
- Fixed number of operations
- Independent of input size
- Often involves direct access (like array indexing)

Constant time is the ideal. When you can achieve O(1), you have eliminated scaling problems entirely.

### Logarithmic: O(log n)

Cost grows slowly as input increases. Every time input doubles, cost increases by a fixed amount.

Characteristics:
- Each step eliminates a fraction of the remaining work
- Often involves divide-and-conquer strategies
- Very scalable; handles large inputs gracefully

Logarithmic algorithms are highly desirable. Even for billions of items, O(log n) remains practical.

### Linear: O(n)

Cost grows proportionally to input size. Double the input, double the work.

Characteristics:
- Processes each element a constant number of times
- The baseline for "reasonable" algorithms
- Unavoidable when you must examine all input

Linear time is often the best you can achieve for problems that require looking at all input. It scales well for most practical purposes.

### Linearithmic: O(n log n)

Slightly worse than linear. Cost grows as n × log n.

Characteristics:
- Common for efficient sorting algorithms
- Divide-and-conquer with per-level linear work
- Still very practical for large inputs

O(n log n) is the theoretical lower bound for comparison-based sorting. Many important algorithms hit this complexity.

### Quadratic: O(n²)

Cost grows as the square of input size. Double the input, quadruple the work.

Characteristics:
- Common with nested loops over the input
- Becomes impractical for large inputs
- Often a sign that a better algorithm exists

Quadratic algorithms work for small inputs but fail at scale. A quadratic algorithm that runs in 1 second for 1,000 items takes over 11 days for 1 million items.

### Polynomial: O(n^k)

A generalization of quadratic: cost grows as n raised to some power k.

Characteristics:
- O(n²), O(n³), O(n⁴), etc.
- Higher powers become impractical very quickly
- Still considered "tractable" in complexity theory

In theoretical computer science, polynomial time is the boundary of "efficient" algorithms. In practice, anything above O(n²) or O(n³) is often too slow.

### Exponential: O(2^n) and worse

Cost doubles with each additional input element. Grows explosively.

Characteristics:
- Brute-force approaches to combinatorial problems
- Becomes impractical for even moderate inputs
- Often signals that no efficient algorithm is known

Exponential algorithms are typically impractical beyond very small inputs. O(2^n) for n = 30 is already over a billion operations.

### Factorial: O(n!)

Cost grows faster than exponential. Factorial of n is 1 × 2 × 3 × ... × n.

Characteristics:
- Generating all permutations
- Brute-force for certain optimization problems
- Impractical beyond tiny inputs

Factorial growth is extreme: 10! = 3,628,800; 20! ≈ 2.4 × 10^18. Even for n = 20, a factorial algorithm is completely impractical.

### The practical hierarchy

For practical purposes, think of complexity classes in tiers:

**Fast (practical for large inputs):**
- O(1) — constant
- O(log n) — logarithmic
- O(n) — linear
- O(n log n) — linearithmic

**Moderate (careful with input size):**
- O(n²) — quadratic
- O(n³) — cubic

**Slow (only for small inputs):**
- O(2^n) — exponential
- O(n!) — factorial

The "Common Runtimes" sub-page examines each class in detail with examples.

## Analyzing complexity in practice

Understanding complexity classes is only useful if you can analyze actual algorithms. Several patterns help you recognize complexity.

### Sequential operations

Operations that happen one after another have complexities that add:

If you first do an O(n) operation, then an O(n²) operation, the total is O(n + n²) = O(n²).

The dominant term wins. You only count the largest one because it overwhelms the rest at scale.

### Loops

A single loop iterating n times suggests O(n) complexity (assuming the loop body is O(1)).

Nested loops multiply: two nested loops each running n times suggest O(n × n) = O(n²).

This generalizes: three nested loops → O(n³), and so on.

### Divide and conquer

Algorithms that divide the problem in half at each step often have logarithmic factors:

- Binary search: O(log n) — divide in half, do O(1) work per level
- Merge sort: O(n log n) — divide in half, do O(n) work per level

The number of times you can halve n before reaching 1 is log n.

### Recursion

Recursive algorithms require analyzing the recurrence relation—how much work at each level and how many levels.

- If recursion depth is log n and each level does O(n) work: O(n log n)
- If recursion depth is n and each level does O(n) work: O(n²)
- If each call makes multiple recursive calls: potentially exponential

The "How to Calculate Complexity" sub-page teaches systematic methods for analyzing loops, recursion, and more complex patterns.

## Complexity in real systems

Theoretical complexity guides decisions, but real-world performance has additional considerations.

### Constant factors matter (sometimes)

An O(n) algorithm with a large constant factor might be slower than an O(n log n) algorithm with a small constant for realistic input sizes.

Theory ignores constants, but practice cannot. When comparing algorithms with the same or similar complexity classes, constants may decide the winner.

### Cache effects

Modern CPUs have hierarchical memory (L1/L2/L3 cache, RAM). Accessing data in cache is orders of magnitude faster than accessing RAM.

Algorithms that access memory sequentially (like scanning an array) benefit from cache prefetching. Algorithms that jump around memory (like following linked-list pointers) suffer cache misses.

Two algorithms with the same theoretical complexity can have vastly different real-world performance due to cache behavior.

### Input characteristics

Some algorithms have poor worst-case complexity but excellent average-case complexity. Others have complexity that varies with input structure.

For example:
- Quicksort is O(n²) worst case but O(n log n) average case, and in practice it is often faster than merge sort.
- Hash table operations are O(n) worst case but O(1) average case.

Knowing your input distribution helps you choose algorithms effectively.

### The right tool for the job

Complexity analysis helps you choose between algorithms, but it is not the only factor:

- Simplicity: A slightly slower algorithm that is easier to understand and maintain may be preferable.
- Implementation availability: Using a well-tested library is often better than implementing a theoretically superior algorithm.
- Development time: If the faster algorithm takes weeks to implement and the slower one works well enough, use the slower one.

Complexity is a tool for making decisions, not a mandate to always use the theoretically optimal algorithm.

## How this section is organized

This section builds your understanding of complexity from foundations to practical application:

**Time vs Space Complexity** explores the two dimensions of algorithmic cost. You will learn when to prioritize time over space, when to make the opposite trade-off, and how to think about problems that constrain both.

**How to Calculate Complexity** teaches you to analyze code and derive its complexity step by step. You will learn to count operations, handle loops and recursion, and simplify expressions to identify dominant terms.

**Asymptotic Notation** covers Big-O, Big-Theta, and Big-Omega rigorously. You will understand the formal definitions, how to prove complexity bounds, and how to communicate about complexity precisely.

**Common Runtimes** examines each complexity class from constant to factorial. Each page provides multiple examples of algorithms in that class, helping you recognize patterns and build intuition.

By the end of this section, you will be able to:
- Look at an algorithm and estimate its complexity
- Compare different approaches quantitatively
- Recognize when an algorithm is practical and when it is not
- Make informed decisions about which solution fits your constraints
- Communicate about performance using standard terminology

## Summary

Algorithmic complexity is the foundation for reasoning about performance. It lets you predict how algorithms behave at scale and compare approaches before building them.

Key takeaways:

- **Complexity measures growth rate**, not exact operation counts. The dominant term determines behavior at scale.
- **Time complexity** measures running time growth; **space complexity** measures memory growth. Both matter, and there are often trade-offs between them.
- **Worst-case analysis** provides guarantees; **average-case analysis** describes typical behavior; **amortized analysis** spreads costs over sequences of operations.
- **Asymptotic notation** (Big-O, Big-Theta, Big-Omega) describes growth rates precisely while ignoring constants and lower-order terms.
- Algorithms fall into **common complexity classes** (constant, logarithmic, linear, polynomial, exponential, factorial) that differ dramatically in scalability.
- **Practical performance** also depends on constant factors, cache behavior, and input characteristics. Theory guides decisions, but benchmarking confirms them.

Understanding complexity empowers you to write software that works not just on test data, but at whatever scale your users need.
