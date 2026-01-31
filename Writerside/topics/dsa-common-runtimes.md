# Common Runtimes

Reading time: 25 minutes

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

## The mathematical foundations

Understanding why certain complexity classes exist requires familiarity with the mathematical functions that define them. Each complexity class corresponds to a fundamental mathematical pattern that arises naturally from algorithmic structures.

### Constant functions

A constant function f(n) = c produces the same output regardless of n. In algorithms, this occurs when the work required does not depend on input size. Accessing an array element by index is constant because the memory address calculation takes the same time whether the array has 10 elements or 10 million.

### Logarithmic functions

The logarithm log_b(n) asks: "How many times must I multiply b to reach n?" Equivalently: "How many times can I divide n by b before reaching 1?" For binary logarithms (base 2):

- log₂(2) = 1
- log₂(4) = 2
- log₂(8) = 3
- log₂(1024) = 10
- log₂(1,000,000) ≈ 20

Logarithms grow incredibly slowly. Doubling n adds only 1 to log₂(n). This is why logarithmic algorithms scale so well.

### Linear functions

A linear function f(n) = cn grows proportionally with n. Double the input, double the work. This is the natural complexity for problems requiring examination of every element once.

### Linearithmic functions

The function n log n appears when an algorithm must do logarithmic work for each of n elements, or when divide-and-conquer splits the problem and performs linear work at each level. It grows faster than linear but much slower than quadratic.

### Polynomial functions

Polynomials like n², n³, and n⁴ arise from nested iteration. Two nested loops over n elements typically produce O(n²). Three nested loops produce O(n³). The exponent equals the depth of nesting.

### Exponential functions

Exponential functions like 2ⁿ grow by a constant factor for each unit increase in n. They arise when an algorithm must consider all subsets of a set (2ⁿ subsets for n elements) or when recursive calls branch without combining subproblems.

### Factorial functions

The factorial n! = n × (n-1) × (n-2) × ... × 1 counts the number of permutations of n elements. It grows even faster than exponential, making factorial algorithms impractical for n > 15 or so.

## Why these specific classes emerge

These complexity classes are not arbitrary classifications imposed by computer scientists. They arise naturally from fundamental algorithmic patterns and mathematical structures.

### Direct access yields constant time

When data is stored at a known location, retrieving it requires only calculating that location. Arrays provide this property through pointer arithmetic. Hash tables provide it probabilistically through hash functions. Any data structure that maps keys directly to storage locations achieves constant time access.

### Halving yields logarithmic time

Any algorithm that eliminates a constant fraction of remaining possibilities at each step exhibits logarithmic behavior. Binary search eliminates half. Ternary search eliminates two-thirds. The specific fraction affects only the constant factor, not the complexity class. The key insight is that reaching one element from n requires only log(n) halvings.

### Full traversal yields linear time

When an algorithm must examine every element at least once, it cannot be faster than linear. Finding the sum, the maximum, or verifying a property across all elements requires O(n) time. This is often the best achievable complexity for problems that depend on all input data.

### Divide and conquer with linear merge yields linearithmic time

Algorithms that split the problem in half, solve both halves recursively, and combine results in linear time achieve O(n log n). The recursion has log(n) levels, and each level does O(n) total work. Merge sort is the canonical example.

### Pairwise comparison yields quadratic time

When an algorithm must consider every pair of elements, it performs n × n work. Even if symmetry reduces this to n(n-1)/2, this is still O(n²). Matrix operations, naive duplicate detection, and simple sorting algorithms exhibit this pattern.

### Subset enumeration yields exponential time

When an algorithm must consider all possible combinations of including or excluding each element, it faces 2ⁿ possibilities. Each element contributes a binary choice, and n independent binary choices produce 2ⁿ outcomes.

### Permutation enumeration yields factorial time

When an algorithm must consider all possible orderings, it faces n! possibilities. The first position has n choices, the second has n-1, and so on.

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

### Time budgets for different complexities

Understanding practical limits helps you make algorithmic decisions before implementation:

**For interactive applications (response within 100ms):**
- O(1): unlimited
- O(log n): unlimited
- O(n): up to ~10⁷
- O(n log n): up to ~10⁶
- O(n²): up to ~3,000
- O(n³): up to ~500

**For batch processing (response within 1 hour):**
- O(1): unlimited
- O(log n): unlimited
- O(n): up to ~10¹¹
- O(n log n): up to ~10¹⁰
- O(n²): up to ~6 × 10⁵
- O(n³): up to ~15,000
- O(2ⁿ): up to ~45

These estimates assume modern hardware and well-implemented algorithms. Real performance varies based on constant factors, memory access patterns, and implementation quality.

## The polynomial/exponential divide

A crucial distinction exists between **polynomial** complexity (O(n), O(n²), O(n³), etc.) and **exponential** complexity (O(2ⁿ), O(n!), etc.).

Polynomial algorithms are considered **tractable**. Even O(n³) can handle thousands of elements in reasonable time. As hardware improves, polynomial algorithms scale to larger inputs.

Exponential algorithms are **intractable** for large inputs. No amount of hardware improvement will make O(2ⁿ) practical for n = 100. The growth is simply too fast.

This divide is fundamental to computational complexity theory. Many important problems have no known polynomial-time algorithms, which is why understanding complexity classes matters beyond just optimizing code.

### The computational cliff

Consider what happens as n increases from 50 to 100:

| Complexity | n = 50 | n = 100 | Growth factor |
|------------|--------|---------|---------------|
| O(n) | 50 | 100 | 2× |
| O(n²) | 2,500 | 10,000 | 4× |
| O(n³) | 125,000 | 1,000,000 | 8× |
| O(2ⁿ) | ~10¹⁵ | ~10³⁰ | 10¹⁵× |

Polynomial complexities grow by small constant factors. Exponential complexity grows by a factor larger than all the atoms in the observable universe. This is not a quantitative difference but a qualitative one: polynomial problems are solvable; exponential problems often are not.

### Implications for problem solving

The tractability divide affects how you approach problems:

**For polynomial problems:**
- Focus on reducing the exponent (O(n³) → O(n²) → O(n))
- Optimize constant factors for performance
- Expect the algorithm to scale with better hardware

**For exponential problems:**
- Determine if exact solutions are necessary
- Consider approximation algorithms with proven bounds
- Apply heuristics that work well in practice
- Use pruning to eliminate impossible solutions early
- Accept that input size limits are fundamental, not temporary

## Comparing algorithms in the same class

Two algorithms in the same complexity class can have very different real-world performance:

- An O(n) algorithm with constant factor 2 is twice as fast as one with constant factor 4.
- An O(n log n) sorting algorithm optimized for cache locality might beat a theoretically identical one that is not.
- Implementation details, memory access patterns, and language overhead all affect actual speed.

Complexity class tells you how algorithms scale relative to each other. Within a class, benchmarking tells you which is faster for your specific use case.

### Constant factors matter

Big-O notation deliberately hides constant factors because they do not affect growth rate. However, constant factors significantly impact real performance:

- An algorithm performing 100n operations is 100 times slower than one performing n operations, despite both being O(n).
- Cache-friendly memory access can provide 10-100× speedups over cache-unfriendly access.
- Predictable branching patterns enable CPU speculation, improving performance by 2-5×.

For algorithms in the same complexity class, the faster one in practice depends on:
- Constant factors in the operation count
- Memory access patterns (sequential vs. random)
- Branch predictability
- Parallelization potential
- Language and runtime efficiency

### When lower complexity loses

A higher-complexity algorithm can outperform a lower-complexity one for specific input sizes:

- Binary search (O(log n)) has overhead that makes linear search (O(n)) faster for arrays under 10-50 elements.
- Hash table insertion (O(1) amortized) involves overhead that makes array insertion faster for very small collections.
- Quick sort (O(n log n) average) often beats merge sort (also O(n log n)) due to better cache behavior, despite worse worst-case complexity.

The crossover point where theoretical complexity dominates depends on implementation details and hardware characteristics.

## Memory hierarchy effects

Modern computers have multiple levels of memory with vastly different speeds:

| Level | Size | Latency |
|-------|------|---------|
| L1 cache | 32-64 KB | ~1 ns |
| L2 cache | 256-512 KB | ~4 ns |
| L3 cache | 4-32 MB | ~12 ns |
| Main memory | 8-64 GB | ~100 ns |
| SSD storage | 256 GB - 4 TB | ~100,000 ns |
| Hard disk | 1-16 TB | ~10,000,000 ns |

These differences profoundly affect algorithm performance. An O(n) algorithm with poor cache behavior can be slower than an O(n log n) algorithm with good cache behavior.

### Cache-friendly algorithms

Sequential memory access is fast because processors fetch data in cache lines (typically 64 bytes). Accessing element i+1 after element i often costs nothing because it is already cached.

Random memory access is slow because each access potentially requires fetching a new cache line. An algorithm that jumps around memory pays the full latency cost repeatedly.

This is why:
- Array-based data structures often outperform pointer-based ones
- Merge sort can be slower than quick sort despite similar complexity
- Hash tables with open addressing outperform those with chaining for small load factors

### Working set size

An algorithm's **working set** is the amount of memory it actively uses. When the working set fits in cache, performance is excellent. When it exceeds cache size, performance degrades sharply.

This creates effective complexity thresholds:
- n ≤ 10,000: Most data fits in L3 cache; algorithms run at near-theoretical speed
- n ≤ 1,000,000: Data exceeds L3 cache; memory bandwidth becomes limiting
- n ≤ 100,000,000: Data exceeds main memory; disk access dominates

These thresholds shift the practical limits of each complexity class.

## Space complexity considerations

Every algorithm uses both time and space. Space complexity measures memory requirements as a function of input size. The same complexity classes apply:

| Space Complexity | Meaning |
|------------------|---------|
| O(1) | Fixed memory regardless of input |
| O(log n) | Memory grows very slowly with input |
| O(n) | Memory proportional to input |
| O(n²) | Memory grows quadratically |

### Time-space tradeoffs

Many algorithmic improvements trade space for time:

- Hash tables: O(n) space enables O(1) lookups instead of O(n)
- Dynamic programming: O(n) or O(n²) space can reduce exponential time to polynomial
- Precomputation: O(n) space for lookup tables reduces repeated computation

The optimal tradeoff depends on available memory and access patterns.

### In-place algorithms

An **in-place** algorithm uses O(1) extra space beyond the input. These algorithms are valuable when memory is constrained:

- In-place sorting (heap sort, quick sort) requires only O(log n) stack space
- In-place array reversal uses O(1) extra space
- In-place matrix transposition uses O(1) extra space

In-place algorithms are often more complex to implement but essential for large-scale data processing.

## Amortized complexity

Some operations have varying costs across invocations. **Amortized analysis** averages the cost over a sequence of operations.

Consider a dynamic array that doubles when full:
- Most insertions: O(1)
- Occasional resizing: O(n)

If we double the capacity each time, resizing happens at sizes 1, 2, 4, 8, 16, ... For n insertions, total resizing work is 1 + 2 + 4 + ... + n = 2n - 1. Averaged over n insertions, this is O(1) per insertion.

Amortized O(1) is weaker than worst-case O(1) but stronger than average-case O(1):
- **Worst-case O(1)**: Every operation takes constant time
- **Amortized O(1)**: Any sequence of n operations takes O(n) total time
- **Average-case O(1)**: Expected time over random inputs is constant

### When amortized analysis matters

Amortized complexity is appropriate when:
- Operations are performed in sequences, not individually
- Occasional expensive operations are acceptable
- You care about total time, not individual operation time

Amortized complexity is problematic when:
- Real-time guarantees are required
- Operations are latency-sensitive
- Expensive operations cause unacceptable delays

## Best, average, and worst case

Complexity can vary depending on input characteristics. Complete analysis considers multiple cases.

### Worst-case complexity

The maximum time for any input of size n. This is the most common measure because it provides guarantees:
- Quick sort is O(n²) worst case (when pivot selection is poor)
- Binary search is O(log n) worst case
- Hash table lookup is O(n) worst case (when all keys collide)

Worst-case analysis is conservative but safe.

### Average-case complexity

The expected time over all possible inputs of size n, assuming some probability distribution:
- Quick sort is O(n log n) average case (random pivot selection)
- Hash table lookup is O(1) average case (uniform hash distribution)
- Linear search is O(n/2) = O(n) average case (target equally likely anywhere)

Average-case analysis is realistic but requires assumptions about input distribution.

### Best-case complexity

The minimum time for any input of size n:
- Insertion sort is O(n) best case (already sorted)
- Linear search is O(1) best case (target is first)
- Binary search is O(1) best case (target is middle)

Best-case analysis is optimistic and rarely useful for algorithm selection.

### Which case matters?

For algorithm selection, prioritize cases in this order:
1. **Worst case** when inputs may be adversarial or pathological cases are common
2. **Average case** when inputs are random or well-distributed
3. **Best case** rarely, only when inputs are known to have special structure

## Complexity classes in practice

Different domains have different complexity tolerances based on typical input sizes and performance requirements.

### Web applications

- Response time budget: 100-500ms
- Typical data sizes: 100 to 10,000 items per request
- Acceptable complexities: O(1) through O(n log n); O(n²) only for n < 1000

### Data processing pipelines

- Response time budget: Minutes to hours
- Typical data sizes: Millions to billions of records
- Acceptable complexities: O(n) through O(n log n); O(n²) rarely acceptable

### Scientific computing

- Response time budget: Hours to days
- Typical data sizes: Thousands to millions of elements (often matrices)
- Acceptable complexities: O(n³) for dense linear algebra; specialized algorithms for specific structures

### Competitive programming

- Response time budget: 1-2 seconds
- Typical data sizes: Specified per problem (often 10⁵ to 10⁶)
- Target complexity: Usually deducible from constraints

## Recognizing complexity from code structure

With practice, you can estimate complexity by inspecting code structure:

### Single loop over input: O(n)

A single loop that iterates once per element is O(n). Multiple sequential loops are still O(n) because O(n + n + n) = O(n).

### Nested loops over input: O(n²)

Two nested loops, each iterating over input, produce O(n²). The outer loop runs n times; for each iteration, the inner loop runs up to n times.

### Nested loops with shrinking range: Still O(n²)

Loops like "for i from 0 to n; for j from i to n" iterate n + (n-1) + (n-2) + ... + 1 = n(n+1)/2 times. This is O(n²).

### Halving the search space: O(log n)

Any loop that halves remaining work each iteration is O(log n). Binary search divides by 2; ternary search divides by 3. Both are O(log n).

### Recursion with single recursive call: Depends on reduction

A recursive function making one call to a smaller problem has complexity depending on how much smaller:
- f(n) calls f(n-1): O(n) total calls
- f(n) calls f(n/2): O(log n) total calls

### Recursion with multiple recursive calls: Often exponential

A recursive function making multiple calls without memoization often has exponential complexity:
- f(n) = f(n-1) + f(n-1): O(2ⁿ)
- f(n) = f(n-1) + f(n-2): O(1.618ⁿ) ≈ O(2ⁿ)

Memoization can reduce these to polynomial complexity.

## How this section is organized

The following pages examine each major complexity class in detail:

1. **Constant O(1)** covers operations that take the same time regardless of input size. You will learn about direct addressing, hash tables, and the distinction between true constant time and amortized constant time.

2. **Logarithmic O(log n)** covers algorithms that eliminate half the problem at each step. Binary search, balanced trees, and exponentiation by squaring demonstrate this pattern.

3. **Linear O(n)** covers algorithms that examine each element a fixed number of times. You will learn when linear time is optimal and how to recognize hidden linear operations.

4. **Polynomial O(n²), O(n³)** covers algorithms with nested iteration over the input. Simple sorting algorithms, matrix operations, and pairwise comparisons fall into this category.

5. **Exponential O(2ⁿ)** covers algorithms that explore all subsets or branches. Combinatorial problems, the knapsack problem, and techniques for managing exponential complexity are covered.

6. **Factorial O(n!)** covers algorithms that explore all permutations. The traveling salesman problem, scheduling problems, and approximation techniques demonstrate when factorial complexity arises.

Each page includes examples, patterns, and guidance on when these complexities are acceptable.

## Summary

- Algorithms fall into common **complexity classes** based on their growth rates.
- The hierarchy from fastest to slowest: O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ), O(n!).
- **Polynomial** complexities (up to O(n³) or so) are tractable; **exponential** complexities are not.
- The gaps between classes are dramatic, determining what input sizes are practical.
- Within a class, **constant factors** and **implementation details** affect real performance.
- **Memory hierarchy** effects can make theoretically faster algorithms slower in practice.
- **Amortized analysis** averages costs over operation sequences, giving useful bounds for dynamic data structures.
- **Best, average, and worst case** analysis captures different aspects of algorithm behavior.
- Knowing common runtimes helps you predict algorithm behavior and choose appropriate solutions.
