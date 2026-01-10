# Linear

Reading time: 8 minutes

**Linear time complexity**, written as **O(n)**, describes algorithms whose running time grows proportionally with input size. If you double the input, the algorithm takes roughly twice as long. This is often the best achievable complexity when you must examine every element at least once.

## What O(n) means

A linear algorithm performs a constant amount of work for each input element. The total work is proportional to n, the number of elements.

For example:
- 1,000 elements → about 1,000 operations
- 10,000 elements → about 10,000 operations
- 1,000,000 elements → about 1,000,000 operations

The "constant amount of work" might be one operation or a hundred, but it does not depend on n. This constant factor is absorbed into the Big-O notation.

## Common O(n) operations

### Traversing a collection

The most basic O(n) operation is visiting every element once:

```javascript
function sum(arr) {
  let total = 0;
  for (const num of arr) {
    total += num;  // O(1) per element
  }
  return total;
}
// Total: O(n)
```

Each element is processed exactly once with O(1) work per element.

### Linear search

Finding an element without any special structure requires checking elements one by one:

```javascript
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) {
      return i;
    }
  }
  return -1;
}
// Worst case: O(n), Best case: O(1)
```

In the worst case (target not found or at the end), all n elements are checked.

### Finding minimum or maximum

```javascript
function findMax(arr) {
  let max = arr[0];
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] > max) {
      max = arr[i];
    }
  }
  return max;
}
// O(n) - must check every element
```

You cannot find the maximum without examining every element. Any skipped element might be the maximum.

### Copying or transforming

Creating a new collection based on existing data is O(n):

```javascript
function double(arr) {
  const result = [];
  for (const num of arr) {
    result.push(num * 2);
  }
  return result;
}
// O(n) time, O(n) space
```

Higher-order functions like `map`, `filter`, and `reduce` are typically O(n):

```javascript
const doubled = arr.map(x => x * 2);     // O(n)
const evens = arr.filter(x => x % 2 === 0);  // O(n)
const sum = arr.reduce((a, b) => a + b, 0);  // O(n)
```

## O(n) with hash tables

Hash tables enable powerful O(n) algorithms by providing O(1) lookups:

**Finding duplicates:**

```javascript
function hasDuplicate(arr) {
  const seen = new Set();
  for (const item of arr) {
    if (seen.has(item)) return true;  // O(1) lookup
    seen.add(item);                    // O(1) insert
  }
  return false;
}
// O(n) time, O(n) space
```

Without a hash table, finding duplicates requires O(n²) comparisons.

**Two-sum problem:**

```javascript
function twoSum(arr, target) {
  const seen = new Map();
  for (let i = 0; i < arr.length; i++) {
    const complement = target - arr[i];
    if (seen.has(complement)) {
      return [seen.get(complement), i];
    }
    seen.set(arr[i], i);
  }
  return null;
}
// O(n) time, O(n) space
```

**Counting frequencies:**

```javascript
function countFrequencies(arr) {
  const counts = new Map();
  for (const item of arr) {
    counts.set(item, (counts.get(item) || 0) + 1);
  }
  return counts;
}
// O(n)
```

## Multiple passes are still O(n)

Making multiple passes through the data is still O(n) as long as the number of passes is constant:

```javascript
function analyze(arr) {
  // Pass 1: find sum
  let sum = 0;
  for (const num of arr) {
    sum += num;
  }

  // Pass 2: find max
  let max = arr[0];
  for (const num of arr) {
    if (num > max) max = num;
  }

  // Pass 3: count positive
  let positiveCount = 0;
  for (const num of arr) {
    if (num > 0) positiveCount++;
  }

  return { sum, max, positiveCount };
}
// O(3n) = O(n)
```

Three passes means 3n operations, which is O(n).

## When O(n) is optimal

Linear time is the best possible complexity when you must:

1. **Read all input:** If every element matters, you must look at each one.
2. **Produce output proportional to input:** Writing n outputs takes O(n).
3. **Make a decision based on all elements:** Finding the sum, max, or checking a condition over all elements.

For example, you cannot find the sum of an array faster than O(n) because every element contributes to the answer. Skipping an element would give the wrong result.

## When you can do better than O(n)

You can beat O(n) when:

1. **Data has structure:** Sorted arrays enable O(log n) binary search.
2. **Preprocessing is allowed:** Build an index once, query many times.
3. **You do not need all elements:** Find one element, not all.
4. **Probabilistic is acceptable:** Approximate answers can sometimes be faster.

## Linear vs. linearithmic

O(n) is faster than O(n log n), but the difference is modest:

| n | O(n) | O(n log n) | Ratio |
|---|------|------------|-------|
| 1,000 | 1,000 | 10,000 | 10× |
| 1,000,000 | 1,000,000 | 20,000,000 | 20× |
| 1,000,000,000 | 1,000,000,000 | 30,000,000,000 | 30× |

The ratio is log n, which grows very slowly. An O(n log n) algorithm is often acceptable when O(n) is not achievable.

## Hidden linear operations

Some operations that look O(1) are actually O(n):

**Array operations:**

```javascript
arr.shift();      // O(n) - shifts all elements
arr.unshift(x);   // O(n) - shifts all elements
arr.splice(i, 1); // O(n) - shifts elements after i
arr.includes(x);  // O(n) - linear search
arr.indexOf(x);   // O(n) - linear search
```

**String operations:**

```javascript
str.includes(sub);     // O(n × m)
str.indexOf(sub);      // O(n × m)
str + otherStr;        // O(n + m) - creates new string
str.slice(i, j);       // O(j - i)
```

**Calling O(n) operations inside loops:**

```javascript
// Accidentally O(n²)
for (let i = 0; i < arr.length; i++) {
  if (arr.includes(someValue)) {  // O(n) inside O(n) loop
    // ...
  }
}
```

## Optimizing within O(n)

Even when complexity is fixed at O(n), constant factors matter:

**Minimize work per element:**

```javascript
// Slower: multiple operations
for (const item of arr) {
  total += item;
  count++;
  if (item > max) max = item;
}

// Faster: single conditional
for (const item of arr) {
  total += item;
  count++;
  max = item > max ? item : max;  // Branch prediction friendly
}
```

**Cache-friendly access:**

```javascript
// Good: sequential access
for (let i = 0; i < arr.length; i++) {
  process(arr[i]);
}

// Bad: random access
for (const index of randomIndices) {
  process(arr[index]);
}
```

**Reduce allocations:**

```javascript
// More allocations
const result = arr.map(x => x * 2);

// Fewer allocations (mutates in place)
for (let i = 0; i < arr.length; i++) {
  arr[i] = arr[i] * 2;
}
```

## Summary

- **O(n)** means running time grows proportionally with input size.
- Linear algorithms examine each element a constant number of times.
- Common patterns: traversal, linear search, find min/max, map/filter/reduce.
- **Hash tables** enable O(n) solutions for problems that would otherwise require O(n²).
- Multiple constant passes are still O(n): O(2n) = O(3n) = O(n).
- O(n) is **optimal** when you must examine every element.
- Watch for **hidden O(n)** operations inside loops that create O(n²) complexity.
