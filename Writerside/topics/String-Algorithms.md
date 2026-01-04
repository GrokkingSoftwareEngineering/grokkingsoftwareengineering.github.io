# String Algorithms

Efficient techniques for searching, matching, and indexing text.

## Prerequisites
- Programming basics
- Comfort with core data structures and algorithms

## What You'll Learn
- Substring search algorithms and their complexity
- Pattern matching techniques used in real systems
- When to use suffix arrays and related indexes

## Why It Matters
- Search and parsing tasks appear in every domain
- Efficient text search prevents performance bottlenecks
- Indexing strategies shape storage and latency

## Core Concepts
### Naive Substring Search
- Baseline approach and complexity
- Where it is still acceptable

### Knuth-Morris-Pratt (KMP)
- Prefix table construction
- Linear-time matching

### Boyer-Moore
- Bad character and good suffix heuristics
- Performance on large alphabets

### Rabin-Karp
- Rolling hashes and collision handling
- When randomized search is useful

### Suffix Arrays and Indexing
- Suffix arrays vs suffix trees
- Tradeoffs for memory and speed

## Worked Examples (Node.js-Style Pseudocode)
- Placeholder for end-to-end examples in JavaScript-like pseudocode
- Focus on clarity of concepts over language-specific features

## Common Pitfalls
- Ignoring character encodings in string search
- Using algorithms optimized for large alphabets on tiny ones
- Skipping collision handling in hash-based search

## Best Practices
- Choose algorithms based on text size and alphabet
- Be explicit about encoding and normalization
- Benchmark against realistic workloads

## Try It Yourself
1. Compare naive search and KMP on large inputs
2. Implement Rabin-Karp with collision checks
3. Build a suffix array for a small text corpus

## Next Steps
- Add case studies and real-world tradeoffs
- Link concepts to adjacent topics in the roadmap

## Related Articles
- [Data-Structures-Algorithms.md](Data-Structures-Algorithms.md)
- [Sorting-Searching-Algorithms.md](Sorting-Searching-Algorithms.md)
- [Advanced-Algorithms.md](Advanced-Algorithms.md)
