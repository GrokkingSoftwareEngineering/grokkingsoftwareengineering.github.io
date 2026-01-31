# Linear

Reading time: 25 minutes

**Linear time complexity**, written as **O(n)**, describes algorithms whose running time grows proportionally with input size. If you double the input, the algorithm takes roughly twice as long. This is often the best achievable complexity when you must examine every element at least once.

## What O(n) means

A linear algorithm performs a constant amount of work for each input element. The total work is proportional to n, the number of elements.

For example:
- 1,000 elements → about 1,000 operations
- 10,000 elements → about 10,000 operations
- 1,000,000 elements → about 1,000,000 operations

The "constant amount of work" might be one operation or a hundred, but it does not depend on n. This constant factor is absorbed into the Big-O notation.

## The mathematics of linear growth

A linear function has the form f(n) = an + b, where a and b are constants. In Big-O notation, we ignore the constant b (which becomes negligible for large n) and the coefficient a (which is a constant factor), leaving O(n).

The key property of linear functions is that they grow at a steady rate. The ratio f(n)/n approaches a constant as n grows. This makes linear algorithms predictable: if processing 1,000 elements takes 1 second, processing 2,000 elements takes about 2 seconds.

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

Each element is processed exactly once with O(1) work per element. The total work is n × O(1) = O(n).

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
// Worst case: O(n), Best case: O(1), Average case: O(n/2) = O(n)
```

In the worst case (target not found or at the end), all n elements are checked. On average, if the target exists, you find it after checking half the elements.

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

You cannot find the maximum without examining every element. Any skipped element might be the maximum. This is a fundamental lower bound.

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

### Counting elements

```javascript
function countOccurrences(arr, target) {
  let count = 0;
  for (const element of arr) {
    if (element === target) {
      count++;
    }
  }
  return count;
}
// O(n)
```

### Checking conditions

```javascript
function allPositive(arr) {
  for (const num of arr) {
    if (num <= 0) return false;  // Early exit possible
  }
  return true;
}
// Best case: O(1), Worst case: O(n)

function somePositive(arr) {
  for (const num of arr) {
    if (num > 0) return true;  // Early exit possible
  }
  return false;
}
// Best case: O(1), Worst case: O(n)
```

Even with early exit, the worst case is O(n) because we might need to check every element.

## O(n) with hash tables

Hash tables enable powerful O(n) algorithms by providing O(1) lookups:

### Finding duplicates

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

Without a hash table, finding duplicates requires O(n²) comparisons (checking every pair) or O(n log n) with sorting.

### Two-sum problem

The classic interview question: find two numbers that sum to a target.

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

For each element, we check if its complement exists in O(1) using the hash map.

### Counting frequencies

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

### Finding unique elements

```javascript
function findUnique(arr) {
  const counts = new Map();
  for (const item of arr) {
    counts.set(item, (counts.get(item) || 0) + 1);
  }
  for (const [item, count] of counts) {
    if (count === 1) return item;
  }
  return null;
}
// O(n) - two passes, still linear
```

### Group anagrams

```javascript
function groupAnagrams(words) {
  const groups = new Map();
  for (const word of words) {
    const key = word.split('').sort().join('');  // O(k log k) where k = word length
    if (!groups.has(key)) {
      groups.set(key, []);
    }
    groups.get(key).push(word);
  }
  return Array.from(groups.values());
}
// O(n × k log k) where n = number of words, k = average word length
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

Three passes means 3n operations, which is O(n). You could combine these into one pass for better cache performance, but the complexity remains the same.

### Combining multiple passes

```javascript
function analyzeOptimized(arr) {
  let sum = 0;
  let max = arr[0];
  let positiveCount = 0;

  for (const num of arr) {
    sum += num;
    if (num > max) max = num;
    if (num > 0) positiveCount++;
  }

  return { sum, max, positiveCount };
}
// O(n) - single pass, better cache behavior
```

## When O(n) is optimal

Linear time is the best possible complexity when you must:

1. **Read all input:** If every element matters, you must look at each one.
2. **Produce output proportional to input:** Writing n outputs takes O(n).
3. **Make a decision based on all elements:** Finding the sum, max, or checking a condition over all elements.

For example, you cannot find the sum of an array faster than O(n) because every element contributes to the answer. Skipping an element would give the wrong result.

### Lower bound proofs

Some problems have proven lower bounds of Ω(n):

**Finding the maximum:** You must examine all n elements because any unexamined element could be the maximum. Therefore, finding the maximum is Ω(n).

**Checking if any element satisfies a condition:** In the worst case, you must check all elements to determine that none satisfy the condition. Therefore, this is Ω(n) in the worst case.

**Verifying a sorted array:** You must check adjacent pairs. With n elements, there are n-1 pairs to check. Therefore, verification is Ω(n).

## When you can do better than O(n)

You can beat O(n) when:

1. **Data has structure:** Sorted arrays enable O(log n) binary search.
2. **Preprocessing is allowed:** Build an index once, query many times.
3. **You do not need all elements:** Find one element, not all.
4. **Probabilistic is acceptable:** Approximate answers can sometimes be faster.
5. **Hardware parallelism:** Multiple processors can divide the work.

```javascript
// O(n) - no structure
function findInUnsorted(arr, target) {
  return arr.includes(target);
}

// O(log n) - sorted structure
function findInSorted(sortedArr, target) {
  return binarySearch(sortedArr, target) !== -1;
}
```

## Linear vs. linearithmic

O(n) is faster than O(n log n), but the difference is modest:

| n | O(n) | O(n log n) | Ratio |
|---|------|------------|-------|
| 1,000 | 1,000 | 10,000 | 10× |
| 1,000,000 | 1,000,000 | 20,000,000 | 20× |
| 1,000,000,000 | 1,000,000,000 | 30,000,000,000 | 30× |

The ratio is log n, which grows very slowly. An O(n log n) algorithm is often acceptable when O(n) is not achievable.

### When the log factor matters

For very large datasets or time-critical applications, the log factor can matter:

```javascript
// Processing 1 billion records
// O(n): ~10 seconds
// O(n log n): ~300 seconds (30 × 10)
```

This difference can be the difference between acceptable and unacceptable performance.

## Hidden linear operations

Some operations that look O(1) are actually O(n):

### Array operations

```javascript
arr.shift();      // O(n) - shifts all elements left
arr.unshift(x);   // O(n) - shifts all elements right
arr.splice(i, 1); // O(n) - shifts elements after i
arr.includes(x);  // O(n) - linear search
arr.indexOf(x);   // O(n) - linear search
arr.lastIndexOf(x);  // O(n) - linear search from end
```

### String operations

```javascript
str.includes(sub);     // O(n × m) where n = str length, m = sub length
str.indexOf(sub);      // O(n × m)
str + otherStr;        // O(n + m) - creates new string
str.slice(i, j);       // O(j - i) - copies substring
str.split(delimiter);  // O(n)
str.replace(pattern, replacement);  // O(n) or more
```

### Array concatenation

```javascript
const combined = arr1.concat(arr2);  // O(n + m)
const combined2 = [...arr1, ...arr2];  // O(n + m)
```

### Calling O(n) operations inside loops

```javascript
// Accidentally O(n²)
for (let i = 0; i < arr.length; i++) {
  if (arr.includes(someValue)) {  // O(n) inside O(n) loop
    // ...
  }
}

// Fixed: O(n)
const valueSet = new Set(arr);  // O(n) preprocessing
for (let i = 0; i < arr.length; i++) {
  if (valueSet.has(someValue)) {  // O(1) lookup
    // ...
  }
}
```

## Optimizing within O(n)

Even when complexity is fixed at O(n), constant factors matter:

### Minimize work per element

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

### Cache-friendly access

```javascript
// Good: sequential access (cache-friendly)
for (let i = 0; i < arr.length; i++) {
  process(arr[i]);
}

// Bad: random access (cache-unfriendly)
for (const index of randomIndices) {
  process(arr[index]);
}
```

Sequential access can be 10-100× faster due to CPU cache behavior.

### Reduce allocations

```javascript
// More allocations (creates new array)
const result = arr.map(x => x * 2);

// Fewer allocations (mutates in place)
for (let i = 0; i < arr.length; i++) {
  arr[i] = arr[i] * 2;
}
```

### Early termination

```javascript
// No early termination
function containsNegative(arr) {
  let found = false;
  for (const num of arr) {
    if (num < 0) found = true;
  }
  return found;
}

// With early termination
function containsNegative(arr) {
  for (const num of arr) {
    if (num < 0) return true;
  }
  return false;
}
```

## Linear time algorithms

Many important algorithms run in linear time:

### Counting sort

When the range of values is limited, counting sort achieves O(n + k) where k is the range:

```javascript
function countingSort(arr, maxValue) {
  const counts = new Array(maxValue + 1).fill(0);

  // Count occurrences: O(n)
  for (const num of arr) {
    counts[num]++;
  }

  // Build sorted array: O(n + k)
  const result = [];
  for (let i = 0; i <= maxValue; i++) {
    for (let j = 0; j < counts[i]; j++) {
      result.push(i);
    }
  }

  return result;
}
// O(n + k) - linear when k is O(n)
```

### Bucket sort

For uniformly distributed data:

```javascript
function bucketSort(arr, bucketCount = arr.length) {
  if (arr.length === 0) return arr;

  const min = Math.min(...arr);
  const max = Math.max(...arr);
  const bucketSize = (max - min) / bucketCount + 1;

  // Create buckets: O(bucketCount)
  const buckets = Array.from({ length: bucketCount }, () => []);

  // Distribute elements: O(n)
  for (const num of arr) {
    const bucketIndex = Math.floor((num - min) / bucketSize);
    buckets[bucketIndex].push(num);
  }

  // Sort each bucket and concatenate: O(n) expected
  const result = [];
  for (const bucket of buckets) {
    bucket.sort((a, b) => a - b);  // Small bucket, fast sort
    result.push(...bucket);
  }

  return result;
}
// O(n) expected for uniformly distributed data
```

### Two-pointer technique

Many array problems can be solved in O(n) using two pointers:

```javascript
// Check if array has pair summing to target (sorted array)
function hasPairWithSum(sortedArr, target) {
  let left = 0;
  let right = sortedArr.length - 1;

  while (left < right) {
    const sum = sortedArr[left] + sortedArr[right];
    if (sum === target) return true;
    if (sum < target) left++;
    else right--;
  }

  return false;
}
// O(n) - each pointer moves at most n times
```

### Sliding window

For substring/subarray problems:

```javascript
// Find longest substring without repeating characters
function longestUniqueSubstring(s) {
  const seen = new Map();
  let maxLength = 0;
  let start = 0;

  for (let end = 0; end < s.length; end++) {
    const char = s[end];
    if (seen.has(char) && seen.get(char) >= start) {
      start = seen.get(char) + 1;
    }
    seen.set(char, end);
    maxLength = Math.max(maxLength, end - start + 1);
  }

  return maxLength;
}
// O(n) - each character visited once
```

### Prefix sums

For range sum queries after preprocessing:

```javascript
function buildPrefixSums(arr) {
  const prefix = new Array(arr.length + 1).fill(0);
  for (let i = 0; i < arr.length; i++) {
    prefix[i + 1] = prefix[i] + arr[i];
  }
  return prefix;
}
// O(n) preprocessing

function rangeSum(prefix, left, right) {
  return prefix[right + 1] - prefix[left];
}
// O(1) per query
```

## Space complexity considerations

Linear algorithms often use O(n) space:

```javascript
// O(n) space - creates new array
const result = arr.map(x => x * 2);

// O(1) space - modifies in place
for (let i = 0; i < arr.length; i++) {
  arr[i] *= 2;
}
```

When memory is constrained, in-place O(n) time algorithms are valuable.

### In-place reversal

```javascript
function reverseInPlace(arr) {
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    [arr[left], arr[right]] = [arr[right], arr[left]];
    left++;
    right--;
  }

  return arr;
}
// O(n) time, O(1) space
```

### Dutch national flag (three-way partition)

```javascript
function partition(arr, pivot) {
  let low = 0;
  let mid = 0;
  let high = arr.length - 1;

  while (mid <= high) {
    if (arr[mid] < pivot) {
      [arr[low], arr[mid]] = [arr[mid], arr[low]];
      low++;
      mid++;
    } else if (arr[mid] > pivot) {
      [arr[mid], arr[high]] = [arr[high], arr[mid]];
      high--;
    } else {
      mid++;
    }
  }

  return arr;
}
// O(n) time, O(1) space
```

## Summary

- **O(n)** means running time grows proportionally with input size.
- Linear algorithms examine each element a constant number of times.
- Common patterns: traversal, linear search, find min/max, map/filter/reduce.
- **Hash tables** enable O(n) solutions for problems that would otherwise require O(n²).
- Multiple constant passes are still O(n): O(2n) = O(3n) = O(n).
- O(n) is **optimal** when you must examine every element.
- Watch for **hidden O(n)** operations inside loops that create O(n²) complexity.
- **Two-pointer** and **sliding window** techniques solve many problems in O(n).
- Within O(n), optimize by reducing work per element, improving cache behavior, and enabling early termination.
- The difference between O(n) and O(n log n) is modest (factor of log n), but significant for very large datasets.
