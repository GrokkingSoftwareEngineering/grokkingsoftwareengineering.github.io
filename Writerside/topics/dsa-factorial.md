# Factorial

Reading time: 8 minutes

**Factorial time complexity**, written as **O(n!)**, represents the fastest-growing complexity class you will encounter in practice. It describes algorithms that explore all possible orderings or arrangements of n elements. With growth so explosive that even n = 15 is often impractical, factorial algorithms are reserved for tiny inputs or problems where no better approach exists.

## What factorial complexity means

The factorial of n, written n!, is the product of all positive integers from 1 to n:

- 5! = 5 × 4 × 3 × 2 × 1 = 120
- 10! = 3,628,800
- 15! = 1,307,674,368,000
- 20! = 2,432,902,008,176,640,000

Factorial grows faster than exponential:

| n | O(2ⁿ) | O(n!) |
|---|-------|-------|
| 5 | 32 | 120 |
| 10 | 1,024 | 3,628,800 |
| 12 | 4,096 | 479,001,600 |
| 15 | 32,768 | 1.3 × 10¹² |
| 20 | 1,048,576 | 2.4 × 10¹⁸ |

At n = 20, factorial is over 2 trillion times larger than exponential.

## Generating permutations

The classic O(n!) algorithm generates all permutations of a sequence:

```javascript
function permutations(arr) {
  const result = [];

  function backtrack(current, remaining) {
    if (remaining.length === 0) {
      result.push([...current]);
      return;
    }

    for (let i = 0; i < remaining.length; i++) {
      current.push(remaining[i]);
      const newRemaining = [...remaining.slice(0, i), ...remaining.slice(i + 1)];
      backtrack(current, newRemaining);
      current.pop();
    }
  }

  backtrack([], arr);
  return result;
}
// O(n!) permutations, O(n) work each = O(n × n!)
```

For each of n positions, you choose from the remaining elements. The first position has n choices, the second has n-1, and so on: n × (n-1) × ... × 1 = n!.

**Heap's algorithm** is a more efficient permutation generator:

```javascript
function heapsPermutations(arr) {
  const result = [];
  const n = arr.length;
  const c = new Array(n).fill(0);

  result.push([...arr]);

  let i = 0;
  while (i < n) {
    if (c[i] < i) {
      if (i % 2 === 0) {
        [arr[0], arr[i]] = [arr[i], arr[0]];
      } else {
        [arr[c[i]], arr[i]] = [arr[i], arr[c[i]]];
      }
      result.push([...arr]);
      c[i]++;
      i = 0;
    } else {
      c[i] = 0;
      i++;
    }
  }

  return result;
}
// Still O(n!), but with lower constants
```

## The traveling salesman problem

The brute-force solution to the traveling salesman problem (TSP) is O(n!):

```javascript
function tspBruteForce(distances) {
  const n = distances.length;
  const cities = Array.from({ length: n - 1 }, (_, i) => i + 1);
  let minDistance = Infinity;
  let bestPath = null;

  function calculateDistance(path) {
    let total = distances[0][path[0]];
    for (let i = 0; i < path.length - 1; i++) {
      total += distances[path[i]][path[i + 1]];
    }
    total += distances[path[path.length - 1]][0];
    return total;
  }

  function permute(current, remaining) {
    if (remaining.length === 0) {
      const dist = calculateDistance(current);
      if (dist < minDistance) {
        minDistance = dist;
        bestPath = [...current];
      }
      return;
    }

    for (let i = 0; i < remaining.length; i++) {
      current.push(remaining[i]);
      permute(current, [...remaining.slice(0, i), ...remaining.slice(i + 1)]);
      current.pop();
    }
  }

  permute([], cities);
  return { path: [0, ...bestPath, 0], distance: minDistance };
}
// O(n!) - checks all possible routes
```

Starting from city 0, you must visit all other n-1 cities in some order. There are (n-1)! possible orderings.

## Why n! grows so fast

Factorial incorporates multiplication at every step:

- 2ⁿ doubles each time: 2, 4, 8, 16, 32...
- n! multiplies by increasing numbers: 1, 2, 6, 24, 120, 720, 5040...

By Stirling's approximation: n! ≈ √(2πn) × (n/e)ⁿ

This shows that n! grows roughly like nⁿ, which is much faster than any fixed base raised to n.

## Practical limits

Assuming 10⁸ operations per second:

| Target Time | Maximum n for O(n!) |
|-------------|---------------------|
| 1 second | ~10 |
| 1 minute | ~12 |
| 1 hour | ~14 |
| 1 day | ~15 |
| 1 year | ~17-18 |

Factorial algorithms hit a wall around n = 12-15. Beyond that, even the fastest computers cannot help.

## Comparison with other complexities

| n | O(n²) | O(2ⁿ) | O(n!) |
|---|-------|-------|-------|
| 5 | 25 | 32 | 120 |
| 8 | 64 | 256 | 40,320 |
| 10 | 100 | 1,024 | 3,628,800 |
| 12 | 144 | 4,096 | 479,001,600 |
| 15 | 225 | 32,768 | 1.3 × 10¹² |

At n = 12, O(n!) is already 100,000 times larger than O(2ⁿ).

## Problems that seem to require O(n!)

Several important problems have no known polynomial-time exact solutions:

- **Traveling salesman:** Find shortest route visiting all cities
- **Hamiltonian path:** Find path visiting all vertices exactly once
- **Assignment problem (brute force):** Optimally assign n workers to n tasks
- **Generating all arrangements:** List all ways to order n items

For many of these, better-than-factorial algorithms exist:
- TSP can be solved in O(n² × 2ⁿ) using dynamic programming (still exponential but better than factorial)
- Assignment problem is polynomial using the Hungarian algorithm

## Reducing factorial complexity

Several techniques can help when facing factorial problems:

**Dynamic programming:**

The Held-Karp algorithm solves TSP in O(n² × 2ⁿ):

```javascript
function tspDP(distances) {
  const n = distances.length;
  const ALL_VISITED = (1 << n) - 1;
  const memo = new Map();

  function solve(mask, pos) {
    if (mask === ALL_VISITED) {
      return distances[pos][0];
    }

    const key = `${mask},${pos}`;
    if (memo.has(key)) return memo.get(key);

    let best = Infinity;
    for (let city = 0; city < n; city++) {
      if (mask & (1 << city)) continue;
      const newDist = distances[pos][city] + solve(mask | (1 << city), city);
      best = Math.min(best, newDist);
    }

    memo.set(key, best);
    return best;
  }

  return solve(1, 0);  // Start at city 0
}
// O(n² × 2ⁿ) - exponential but not factorial
```

**Branch and bound:**

Prune branches that cannot improve on the best solution found:

```javascript
function tspBranchBound(distances) {
  const n = distances.length;
  let bestDistance = Infinity;

  function lowerBound(visited, current, totalSoFar) {
    // Calculate minimum possible remaining distance
    // If it exceeds bestDistance, prune this branch
    // (Implementation depends on heuristic used)
  }

  function search(visited, current, pathLength) {
    if (visited.size === n) {
      const total = pathLength + distances[current][0];
      bestDistance = Math.min(bestDistance, total);
      return;
    }

    // Calculate lower bound and prune if necessary
    if (pathLength + lowerBound(visited, current, pathLength) >= bestDistance) {
      return;  // Prune
    }

    for (let next = 0; next < n; next++) {
      if (!visited.has(next)) {
        visited.add(next);
        search(visited, next, pathLength + distances[current][next]);
        visited.delete(next);
      }
    }
  }

  const visited = new Set([0]);
  search(visited, 0, 0);
  return bestDistance;
}
```

**Approximation algorithms:**

For TSP on metric distances, the nearest-neighbor heuristic gives a solution within 2× of optimal in O(n²):

```javascript
function nearestNeighbor(distances) {
  const n = distances.length;
  const visited = new Set([0]);
  const path = [0];
  let current = 0;

  while (visited.size < n) {
    let nearest = -1;
    let minDist = Infinity;

    for (let i = 0; i < n; i++) {
      if (!visited.has(i) && distances[current][i] < minDist) {
        minDist = distances[current][i];
        nearest = i;
      }
    }

    visited.add(nearest);
    path.push(nearest);
    current = nearest;
  }

  return path;
}
// O(n²) - fast but not optimal
```

## When factorial is acceptable

O(n!) algorithms are acceptable when:

1. **n is tiny:** For n ≤ 10, factorial algorithms run quickly.
2. **Exact solution is required:** When approximations are not acceptable and no better algorithm exists.
3. **Verification:** Checking all possibilities to prove optimality.
4. **Enumeration is the goal:** When you genuinely need all permutations.

## Summary

- **O(n!)** grows faster than any other common complexity class.
- Factorial algorithms become **impractical around n = 12-15**.
- Common sources: generating permutations, brute-force optimization.
- **n! ≈ nⁿ** in growth, much faster than O(2ⁿ).
- **Dynamic programming** can sometimes reduce O(n!) to O(2ⁿ) or better.
- **Approximation algorithms** trade exactness for polynomial time.
- Factorial algorithms are reserved for very small inputs or verification purposes.
