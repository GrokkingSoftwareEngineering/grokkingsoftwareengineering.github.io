# Factorial

Reading time: 25 minutes

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

## The mathematics of factorial growth

The factorial function grows faster than any exponential function. For large n, Stirling's approximation gives:

n! ≈ √(2πn) × (n/e)ⁿ

This shows that n! grows roughly like nⁿ, which is much faster than any fixed base raised to n. While 2ⁿ doubles with each increment, n! multiplies by an increasing amount:

- Going from n to n+1: n! × (n+1) = (n+1)!
- 10! × 11 = 11!
- 10! × 11 ≈ 40 million

The multiplier itself grows with n, leading to super-exponential growth.

### Comparing growth rates

| Function | Growth as n → ∞ |
|----------|-----------------|
| O(n) | Linear |
| O(n²) | Polynomial |
| O(2ⁿ) | Exponential |
| O(n!) | Super-exponential |
| O(nⁿ) | Fastest common |

For most practical purposes, O(n!) is the ceiling of computational feasibility.

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

### In-place permutation generation

```javascript
function permutationsInPlace(arr) {
  const result = [];

  function swap(i, j) {
    [arr[i], arr[j]] = [arr[j], arr[i]];
  }

  function generate(k) {
    if (k === 1) {
      result.push([...arr]);
      return;
    }

    generate(k - 1);

    for (let i = 0; i < k - 1; i++) {
      if (k % 2 === 0) {
        swap(i, k - 1);
      } else {
        swap(0, k - 1);
      }
      generate(k - 1);
    }
  }

  generate(arr.length);
  return result;
}
// O(n!) - more efficient constant factor
```

### Heap's algorithm

Heap's algorithm is one of the most efficient permutation generators:

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
// Still O(n!), but with lower constants and only one swap per permutation
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

### TSP with early pruning

```javascript
function tspWithPruning(distances) {
  const n = distances.length;
  let minDistance = Infinity;
  let bestPath = null;

  function search(current, visited, pathLength) {
    // Prune: current path already longer than best
    if (pathLength >= minDistance) return;

    if (current.length === n) {
      const total = pathLength + distances[current[current.length - 1]][0];
      if (total < minDistance) {
        minDistance = total;
        bestPath = [...current];
      }
      return;
    }

    const lastCity = current[current.length - 1];

    // Try each unvisited city, sorted by distance (nearest neighbor heuristic)
    const candidates = [];
    for (let city = 0; city < n; city++) {
      if (!visited.has(city)) {
        candidates.push({ city, dist: distances[lastCity][city] });
      }
    }
    candidates.sort((a, b) => a.dist - b.dist);

    for (const { city, dist } of candidates) {
      // Prune: if even the nearest neighbor exceeds bound
      if (pathLength + dist >= minDistance) break;

      visited.add(city);
      current.push(city);
      search(current, visited, pathLength + dist);
      current.pop();
      visited.delete(city);
    }
  }

  const visited = new Set([0]);
  search([0], visited, 0);
  return { path: [...bestPath, 0], distance: minDistance };
}
```

## Why n! grows so fast

Factorial incorporates multiplication at every step:

- 2ⁿ doubles each time: 2, 4, 8, 16, 32...
- n! multiplies by increasing numbers: 1, 2, 6, 24, 120, 720, 5040...

By Stirling's approximation: n! ≈ √(2πn) × (n/e)ⁿ

This shows that n! grows roughly like nⁿ, which is much faster than any fixed base raised to n.

### Visualizing the growth

| n | n! | n! / 2ⁿ |
|---|-----|---------|
| 5 | 120 | 3.75 |
| 10 | 3,628,800 | 3,543 |
| 15 | 1.3 × 10¹² | 39,916,800 |
| 20 | 2.4 × 10¹⁸ | 2.3 × 10¹² |

The ratio of factorial to exponential itself grows exponentially!

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

### The impossibility of brute force

Consider trying to solve TSP for 25 cities:
- 24! ≈ 6.2 × 10²³ routes to check
- At 10⁸ routes/second, this takes 6.2 × 10¹⁵ seconds
- That is about 197 million years

No amount of parallelism makes this feasible. Even with a million computers, it would take 197 years.

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

### Combinatorial optimization
- **Traveling salesman:** Find shortest route visiting all cities
- **Hamiltonian path:** Find path visiting all vertices exactly once
- **Job shop scheduling:** Optimal scheduling of jobs on machines

### Assignment problems
- **Bipartite matching:** Optimal assignment of workers to tasks
- **Marriage problem:** Stable matching between two groups

### Enumeration problems
- **Generating all arrangements:** List all ways to order n items
- **Counting permutations with constraints:** Count valid orderings

For many of these, better-than-factorial algorithms exist:
- TSP can be solved in O(n² × 2ⁿ) using dynamic programming (still exponential but better than factorial)
- Assignment problem is polynomial using the Hungarian algorithm
- Bipartite matching is O(V × E) using augmenting paths

## Reducing factorial complexity

Several techniques can help when facing factorial problems:

### Dynamic programming: O(n!) → O(n² × 2ⁿ)

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
      if (mask & (1 << city)) continue;  // Already visited
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

This reduces O(n!) to O(n² × 2ⁿ), which is a dramatic improvement:
- n = 15: 1.3 × 10¹² → 7.2 million
- n = 20: 2.4 × 10¹⁸ → 420 million

### Branch and bound

Prune branches that cannot improve on the best solution found:

```javascript
function tspBranchBound(distances) {
  const n = distances.length;
  let bestDistance = Infinity;
  let bestPath = null;

  // Calculate a lower bound for remaining path
  function lowerBound(visited, current) {
    let bound = 0;

    // Minimum edge from current to unvisited
    let minFromCurrent = Infinity;
    for (let i = 0; i < n; i++) {
      if (!visited.has(i)) {
        minFromCurrent = Math.min(minFromCurrent, distances[current][i]);
      }
    }
    if (minFromCurrent !== Infinity) bound += minFromCurrent;

    // Minimum edges for unvisited cities
    for (let i = 0; i < n; i++) {
      if (!visited.has(i) && i !== current) {
        let minEdge = distances[i][0];  // Edge back to start
        for (let j = 0; j < n; j++) {
          if (i !== j && (j === 0 || !visited.has(j))) {
            minEdge = Math.min(minEdge, distances[i][j]);
          }
        }
        bound += minEdge;
      }
    }

    return bound;
  }

  function search(path, visited, pathLength) {
    const current = path[path.length - 1];

    if (path.length === n) {
      const total = pathLength + distances[current][0];
      if (total < bestDistance) {
        bestDistance = total;
        bestPath = [...path];
      }
      return;
    }

    // Calculate lower bound and prune
    const lb = pathLength + lowerBound(visited, current);
    if (lb >= bestDistance) return;  // Prune

    // Try unvisited cities, sorted by distance
    const candidates = [];
    for (let i = 0; i < n; i++) {
      if (!visited.has(i)) {
        candidates.push({ city: i, dist: distances[current][i] });
      }
    }
    candidates.sort((a, b) => a.dist - b.dist);

    for (const { city, dist } of candidates) {
      visited.add(city);
      path.push(city);
      search(path, visited, pathLength + dist);
      path.pop();
      visited.delete(city);
    }
  }

  search([0], new Set([0]), 0);
  return { path: [...bestPath, 0], distance: bestDistance };
}
```

### Approximation algorithms

For TSP on metric distances, approximation algorithms give polynomial-time solutions:

```javascript
// Nearest neighbor heuristic: O(n²), gives solution within 2× of optimal
function nearestNeighbor(distances) {
  const n = distances.length;
  const visited = new Set([0]);
  const path = [0];
  let current = 0;
  let totalDistance = 0;

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
    totalDistance += minDist;
    current = nearest;
  }

  totalDistance += distances[current][0];  // Return to start
  return { path: [...path, 0], distance: totalDistance };
}
// O(n²) - fast but not optimal
```

```javascript
// 2-approximation using minimum spanning tree
function tspMSTApproximation(distances) {
  const n = distances.length;

  // Build MST using Prim's algorithm
  const mst = [];
  const inMST = new Set([0]);
  const edges = [];

  // Initialize edges from node 0
  for (let i = 1; i < n; i++) {
    edges.push({ from: 0, to: i, weight: distances[0][i] });
  }

  while (inMST.size < n) {
    // Find minimum edge to non-MST node
    edges.sort((a, b) => a.weight - b.weight);
    let minEdge = null;
    for (const edge of edges) {
      if (!inMST.has(edge.to)) {
        minEdge = edge;
        break;
      }
    }

    if (!minEdge) break;

    inMST.add(minEdge.to);
    mst.push(minEdge);

    // Add new edges
    for (let i = 0; i < n; i++) {
      if (!inMST.has(i)) {
        edges.push({ from: minEdge.to, to: i, weight: distances[minEdge.to][i] });
      }
    }
  }

  // DFS traversal of MST gives tour
  const adj = Array.from({ length: n }, () => []);
  for (const { from, to } of mst) {
    adj[from].push(to);
    adj[to].push(from);
  }

  const tour = [];
  const visited = new Set();

  function dfs(node) {
    visited.add(node);
    tour.push(node);
    for (const neighbor of adj[node]) {
      if (!visited.has(neighbor)) {
        dfs(neighbor);
      }
    }
  }

  dfs(0);
  tour.push(0);  // Complete the tour

  let totalDistance = 0;
  for (let i = 0; i < tour.length - 1; i++) {
    totalDistance += distances[tour[i]][tour[i + 1]];
  }

  return { path: tour, distance: totalDistance };
}
// O(n² log n) - guaranteed within 2× of optimal for metric TSP
```

### Heuristics and metaheuristics

```javascript
// 2-opt improvement
function twoOpt(distances, path) {
  let improved = true;
  let bestPath = [...path];

  function pathDistance(p) {
    let total = 0;
    for (let i = 0; i < p.length - 1; i++) {
      total += distances[p[i]][p[i + 1]];
    }
    return total;
  }

  while (improved) {
    improved = false;
    for (let i = 1; i < bestPath.length - 2; i++) {
      for (let j = i + 1; j < bestPath.length - 1; j++) {
        // Try reversing segment [i, j]
        const newPath = [
          ...bestPath.slice(0, i),
          ...bestPath.slice(i, j + 1).reverse(),
          ...bestPath.slice(j + 1)
        ];

        if (pathDistance(newPath) < pathDistance(bestPath)) {
          bestPath = newPath;
          improved = true;
        }
      }
    }
  }

  return { path: bestPath, distance: pathDistance(bestPath) };
}
// Each iteration is O(n²), typically converges quickly
```

## When factorial is acceptable

O(n!) algorithms are acceptable when:

1. **n is tiny:** For n ≤ 10, factorial algorithms run quickly.
2. **Exact solution is required:** When approximations are not acceptable and no better algorithm exists.
3. **Verification:** Checking all possibilities to prove optimality.
4. **Enumeration is the goal:** When you genuinely need all permutations.
5. **One-time computation:** If you only need to solve the problem once and can wait.

### Small input guidelines

| Complexity | Acceptable n |
|------------|--------------|
| O(n!) | n ≤ 10-12 |
| O(n² × 2ⁿ) | n ≤ 20-25 |
| O(2ⁿ) | n ≤ 25-30 |
| O(n³) | n ≤ 1000 |
| O(n²) | n ≤ 10,000 |

## Recognizing factorial patterns

Your algorithm is likely O(n!) if:

- You generate all permutations
- You try all orderings of n elements
- The recurrence is T(n) = n × T(n-1)
- You have n nested loops where the i-th loop runs n-i+1 times

```javascript
// O(n!) - generates permutations
function factorial(arr) {
  if (arr.length <= 1) return [arr];
  const result = [];
  for (let i = 0; i < arr.length; i++) {
    const rest = [...arr.slice(0, i), ...arr.slice(i + 1)];
    for (const perm of factorial(rest)) {  // n-1 subproblems
      result.push([arr[i], ...perm]);
    }
  }
  return result;
}
```

The recurrence T(n) = n × T(n-1) with T(1) = 1 solves to T(n) = n!.

## Factorial vs exponential

While both are intractable for large n, the practical difference is significant:

| n | O(2ⁿ) time | O(n!) time | Factorial / Exponential |
|---|------------|------------|-------------------------|
| 10 | 10 μs | 36 ms | 3,543× |
| 15 | 0.3 ms | 13 sec | 39,917× |
| 20 | 10 ms | 77 years | 2.3 × 10¹²× |

At n = 20:
- O(2ⁿ) is still fast (10 milliseconds)
- O(n!) is completely impractical (77 years)

This is why dynamic programming improvements from O(n!) to O(2ⁿ) are so valuable.

## Summary

- **O(n!)** grows faster than any other common complexity class.
- Factorial algorithms become **impractical around n = 12-15**.
- Common sources: generating permutations, brute-force optimization over orderings.
- **n! ≈ nⁿ** in growth, much faster than O(2ⁿ).
- **Dynamic programming** can sometimes reduce O(n!) to O(2ⁿ) or better.
- **Branch and bound** with good lower bounds can dramatically prune the search space.
- **Approximation algorithms** trade exactness for polynomial time.
- Factorial algorithms are reserved for very small inputs or verification purposes.
- The Held-Karp algorithm reduces TSP from O(n!) to O(n² × 2ⁿ), enabling solutions for n ≤ 20-25.
- For problems like TSP, approximation algorithms give guaranteed-quality solutions in polynomial time.
