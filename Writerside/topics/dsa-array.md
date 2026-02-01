# Array

Reading time: 25 minutes

An **array** is the most fundamental data structure in programming. It stores elements in contiguous memory locations, allowing you to access any element instantly using its index. Arrays form the backbone of countless algorithms and are the building blocks for more complex structures like heaps, hash tables, and matrices.

This page covers everything you need to know about arrays: how they work at the memory level, the performance characteristics of each operation, the difference between static and dynamic arrays, common patterns for solving problems, and pitfalls to avoid. By the end, you will understand not just how to use arrays, but why they behave the way they do.

## What is an array?

An array is an ordered collection of elements stored in contiguous memory. Each element occupies the same amount of space, and you access elements by their position, called an **index**. The first element is at index 0, the second at index 1, and so on.

This simple structure has profound implications. Because elements are stored consecutively and each element has the same size, you can calculate the exact memory address of any element using basic arithmetic. This makes arrays the only data structure that provides true **constant-time random access**—you can jump directly to any position without traversing through other elements.

Here is a simple example of creating and accessing an array in JavaScript:

```javascript
// Creating an array
const numbers = [10, 20, 30, 40, 50];

// Accessing elements by index
console.log(numbers[0]); // 10 (first element)
console.log(numbers[2]); // 30 (third element)
console.log(numbers[4]); // 50 (last element)

// Getting array length
console.log(numbers.length); // 5
```

## Memory layout and address calculation

Understanding how arrays are stored in memory explains why some operations are fast and others are slow.

### Contiguous memory allocation

When you create an array, the runtime allocates a single block of memory large enough to hold all elements. Each element is placed immediately after the previous one with no gaps.

Consider an array of 5 integers, where each integer uses 4 bytes. If the array starts at memory address 1000:

| Index | Element | Memory Address |
|-------|---------|----------------|
| 0     | 10      | 1000           |
| 1     | 20      | 1004           |
| 2     | 30      | 1008           |
| 3     | 40      | 1012           |
| 4     | 50      | 1016           |

The pattern is clear: each element's address is the base address plus its index times the element size.

### The address formula

The memory address of element at index i is:

```
address(i) = baseAddress + (i × elementSize)
```

This formula is why array access is O(1). No matter how large the array, calculating an address requires only multiplication and addition—operations that take constant time.

Compare this to a linked list, where finding the 1000th element requires following 999 pointers. The array jumps directly to element 1000 in the same time it takes to access element 1.

### Cache locality

The contiguous layout provides another major advantage: **cache locality**. Modern CPUs load memory in chunks called cache lines (typically 64 bytes). When you access one array element, the CPU loads neighboring elements into the fast L1 cache automatically.

This means sequential array traversal is extremely efficient:

```javascript
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
let sum = 0;

// Sequential access benefits from cache prefetching
for (let i = 0; i < arr.length; i++) {
  sum += arr[i];
}
```

Each memory fetch loads multiple elements, so subsequent accesses hit the cache instead of going to slower main memory. This cache efficiency makes arrays the fastest structure for sequential processing.

## Array operations and time complexity

Every data structure is defined by its operations and their costs. Let's examine each array operation in detail.

### Access (read an element)

Accessing an element by index is the array's signature operation.

**Time complexity:** O(1) - constant time

**How it works:** Apply the address formula and read the memory location directly.

```javascript
const fruits = ['apple', 'banana', 'cherry', 'date'];

// O(1) access - equally fast for any valid index
const first = fruits[0];      // 'apple'
const third = fruits[2];      // 'cherry'
const last = fruits[fruits.length - 1]; // 'date'
```

This is the operation that makes arrays special. No other structure provides guaranteed O(1) access by position.

### Search (find an element)

Searching for an element by value requires examining elements until you find a match.

**Time complexity:** O(n) for unsorted arrays, O(log n) for sorted arrays using binary search

**Linear search** checks each element sequentially:

```javascript
function linearSearch(arr, target) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) {
      return i; // Return the index where target was found
    }
  }
  return -1; // Target not found
}

const numbers = [64, 34, 25, 12, 22, 11, 90];
console.log(linearSearch(numbers, 25)); // 2
console.log(linearSearch(numbers, 100)); // -1
```

In the worst case, you examine every element, making this O(n).

**Binary search** works only on sorted arrays but is much faster:

```javascript
function binarySearch(arr, target) {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      return mid;
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return -1;
}

const sorted = [11, 12, 22, 25, 34, 64, 90];
console.log(binarySearch(sorted, 25)); // 3
console.log(binarySearch(sorted, 100)); // -1
```

Binary search eliminates half the remaining elements with each comparison, achieving O(log n) time. For an array of 1 million elements, linear search may need 1 million comparisons, while binary search needs at most 20.

### Insert (add an element)

Insertion performance depends heavily on where you insert.

**Insert at end:** O(1) amortized for dynamic arrays

```javascript
const arr = [1, 2, 3];
arr.push(4); // Fast - just add to the end
console.log(arr); // [1, 2, 3, 4]
```

**Insert at beginning:** O(n)

```javascript
const arr = [1, 2, 3];
arr.unshift(0); // Slow - must shift all existing elements
console.log(arr); // [0, 1, 2, 3]
```

**Insert at middle:** O(n)

```javascript
const arr = [1, 2, 4, 5];
arr.splice(2, 0, 3); // Insert 3 at index 2
console.log(arr); // [1, 2, 3, 4, 5]
```

Why is middle insertion slow? All elements after the insertion point must shift to make room:

```javascript
// Manually showing what happens during insertion at index 2
function insertAt(arr, index, value) {
  // Shift elements to the right, starting from the end
  for (let i = arr.length; i > index; i--) {
    arr[i] = arr[i - 1];
  }
  // Place the new value
  arr[index] = value;
  return arr;
}

const numbers = [10, 20, 40, 50];
insertAt(numbers, 2, 30);
console.log(numbers); // [10, 20, 30, 40, 50]
```

For an array of n elements, inserting at position 0 requires shifting all n elements—hence O(n).

### Delete (remove an element)

Deletion mirrors insertion. Removing an element leaves a gap that must be closed by shifting subsequent elements.

**Delete from end:** O(1)

```javascript
const arr = [1, 2, 3, 4];
arr.pop(); // Fast - just remove the last element
console.log(arr); // [1, 2, 3]
```

**Delete from beginning:** O(n)

```javascript
const arr = [1, 2, 3, 4];
arr.shift(); // Slow - must shift all remaining elements
console.log(arr); // [2, 3, 4]
```

**Delete from middle:** O(n)

```javascript
const arr = [1, 2, 3, 4, 5];
arr.splice(2, 1); // Remove 1 element at index 2
console.log(arr); // [1, 2, 4, 5]
```

Here is what happens during deletion:

```javascript
function deleteAt(arr, index) {
  // Shift elements to the left to fill the gap
  for (let i = index; i < arr.length - 1; i++) {
    arr[i] = arr[i + 1];
  }
  arr.length--; // Reduce the array length
  return arr;
}

const numbers = [10, 20, 30, 40, 50];
deleteAt(numbers, 2); // Remove element at index 2
console.log(numbers); // [10, 20, 40, 50]
```

### Update (modify an element)

Updating an existing element is as fast as accessing it.

**Time complexity:** O(1)

```javascript
const arr = [1, 2, 3, 4, 5];
arr[2] = 30; // Direct assignment at index
console.log(arr); // [1, 2, 30, 4, 5]
```

You calculate the address and write the new value. No shifting required.

### Summary of time complexities

| Operation | Best Case | Average Case | Worst Case |
|-----------|-----------|--------------|------------|
| Access by index | O(1) | O(1) | O(1) |
| Search (unsorted) | O(1) | O(n) | O(n) |
| Search (sorted, binary) | O(1) | O(log n) | O(log n) |
| Insert at end | O(1) | O(1)* | O(n)* |
| Insert at beginning | O(n) | O(n) | O(n) |
| Insert at middle | O(n) | O(n) | O(n) |
| Delete from end | O(1) | O(1) | O(1) |
| Delete from beginning | O(n) | O(n) | O(n) |
| Delete from middle | O(n) | O(n) | O(n) |
| Update | O(1) | O(1) | O(1) |

*For dynamic arrays, insert at end is O(1) amortized but O(n) worst case when resizing occurs.

## Static arrays vs. dynamic arrays

Arrays come in two fundamental flavors, and understanding the difference matters for performance.

### Static arrays

A **static array** has a fixed size determined at creation. It cannot grow or shrink.

Characteristics:
- Size is fixed at allocation time
- Memory is allocated once and never changes
- Attempting to add beyond capacity fails or corrupts memory
- Most memory efficient (no wasted space)
- Common in low-level languages like C

In languages with static arrays, you must know the size upfront:

```javascript
// JavaScript doesn't have true static arrays, but TypedArrays are fixed-size
const fixedArray = new Int32Array(5);
fixedArray[0] = 10;
fixedArray[1] = 20;
console.log(fixedArray.length); // 5 - cannot change

// Attempting to assign beyond the bounds is silently ignored
fixedArray[10] = 100; // No error, but no effect
console.log(fixedArray.length); // Still 5
```

### Dynamic arrays

A **dynamic array** (also called a growable array, resizable array, or ArrayList) can change size as elements are added or removed.

JavaScript arrays are dynamic:

```javascript
const arr = [1, 2, 3];
console.log(arr.length); // 3

arr.push(4);
arr.push(5);
console.log(arr.length); // 5

arr.pop();
arr.pop();
console.log(arr.length); // 3
```

### How dynamic arrays grow

Dynamic arrays maintain an internal capacity larger than their current length. When you add elements, they fill the available capacity. When capacity is exhausted, the array allocates a new, larger memory block and copies all elements.

Here is a simplified implementation showing the growth strategy:

```javascript
class DynamicArray {
  constructor() {
    this.data = new Array(4); // Initial capacity
    this.length = 0;
    this.capacity = 4;
  }

  push(value) {
    if (this.length === this.capacity) {
      this.resize();
    }
    this.data[this.length] = value;
    this.length++;
  }

  resize() {
    // Double the capacity
    this.capacity *= 2;
    const newData = new Array(this.capacity);

    // Copy all existing elements
    for (let i = 0; i < this.length; i++) {
      newData[i] = this.data[i];
    }

    this.data = newData;
    console.log(`Resized to capacity: ${this.capacity}`);
  }

  get(index) {
    if (index < 0 || index >= this.length) {
      throw new Error('Index out of bounds');
    }
    return this.data[index];
  }
}

const arr = new DynamicArray();
for (let i = 0; i < 10; i++) {
  arr.push(i);
}
// Output:
// Resized to capacity: 8
// Resized to capacity: 16
```

### Amortized analysis

Resizing copies all n elements, taking O(n) time. This seems to contradict the claim that push is O(1). The resolution is **amortized analysis**.

Consider adding n elements to an empty dynamic array that doubles when full:
- Resizes happen at sizes 1, 2, 4, 8, 16, ... up to n
- Total copy operations: 1 + 2 + 4 + 8 + ... + n = 2n
- Average cost per operation: 2n / n = 2 = O(1)

Most push operations are truly O(1). Occasionally, a push triggers an expensive O(n) resize. But the expensive operations are rare enough that the average cost remains constant.

This is **amortized O(1)**—the cost is spread across many operations. Any individual operation might be slow, but the total cost over many operations is predictable.

### Growth factor trade-offs

Common growth factors are 1.5x and 2x:

**Doubling (2x):**
- Fewer resize operations (less copying overall)
- More wasted space (up to 50% of capacity unused)
- Used by Java ArrayList, Python lists

**1.5x growth:**
- More resize operations
- Less wasted space (up to 33% unused)
- Better memory locality after multiple resizes
- Used by C++ std::vector (in some implementations)

```javascript
// Comparing memory usage with different growth factors
function simulateGrowth(targetSize, growthFactor) {
  let capacity = 1;
  let resizes = 0;

  while (capacity < targetSize) {
    capacity = Math.floor(capacity * growthFactor);
    resizes++;
  }

  const wastedSpace = capacity - targetSize;
  const wastePercent = ((wastedSpace / capacity) * 100).toFixed(1);

  return { capacity, resizes, wastedSpace, wastePercent };
}

console.log('Growing to 1000 elements:');
console.log('2x growth:', simulateGrowth(1000, 2));
console.log('1.5x growth:', simulateGrowth(1000, 1.5));
// 2x growth: { capacity: 1024, resizes: 10, wastedSpace: 24, wastePercent: '2.3' }
// 1.5x growth: { capacity: 1139, resizes: 16, wastedSpace: 139, wastePercent: '12.2' }
```

## Common array patterns

Experienced programmers recognize recurring patterns when working with arrays. Learning these patterns helps you solve problems more efficiently.

### Two-pointer technique

The **two-pointer technique** uses two indices that move through the array, often from opposite ends or at different speeds.

**Example: Finding a pair that sums to a target in a sorted array**

```javascript
function twoSum(sortedArr, target) {
  let left = 0;
  let right = sortedArr.length - 1;

  while (left < right) {
    const sum = sortedArr[left] + sortedArr[right];

    if (sum === target) {
      return [left, right];
    } else if (sum < target) {
      left++; // Need a larger sum
    } else {
      right--; // Need a smaller sum
    }
  }

  return null; // No pair found
}

const numbers = [1, 2, 3, 4, 6, 8, 9];
console.log(twoSum(numbers, 10)); // [1, 5] (indices of 2 and 8)
```

This solves in O(n) time what would take O(n squared) with nested loops.

**Example: Reversing an array in place**

```javascript
function reverse(arr) {
  let left = 0;
  let right = arr.length - 1;

  while (left < right) {
    // Swap elements
    const temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;

    left++;
    right--;
  }

  return arr;
}

console.log(reverse([1, 2, 3, 4, 5])); // [5, 4, 3, 2, 1]
```

### Sliding window

A **sliding window** maintains a range (window) that moves through the array, often to find subarrays with specific properties.

**Example: Maximum sum of k consecutive elements**

```javascript
function maxSumSubarray(arr, k) {
  if (arr.length < k) return null;

  // Calculate sum of first window
  let windowSum = 0;
  for (let i = 0; i < k; i++) {
    windowSum += arr[i];
  }

  let maxSum = windowSum;

  // Slide the window: add next element, remove first element
  for (let i = k; i < arr.length; i++) {
    windowSum = windowSum + arr[i] - arr[i - k];
    maxSum = Math.max(maxSum, windowSum);
  }

  return maxSum;
}

const values = [1, 4, 2, 10, 2, 3, 1, 0, 20];
console.log(maxSumSubarray(values, 4)); // 24 (sum of [3, 1, 0, 20])
```

Without the sliding window, you would recalculate the sum for each position, taking O(n times k). The sliding window achieves O(n).

**Example: Longest substring without repeating characters (using a window)**

```javascript
function lengthOfLongestSubstring(s) {
  const charIndex = new Map();
  let maxLength = 0;
  let windowStart = 0;

  for (let windowEnd = 0; windowEnd < s.length; windowEnd++) {
    const char = s[windowEnd];

    // If character is already in window, shrink window from left
    if (charIndex.has(char) && charIndex.get(char) >= windowStart) {
      windowStart = charIndex.get(char) + 1;
    }

    charIndex.set(char, windowEnd);
    maxLength = Math.max(maxLength, windowEnd - windowStart + 1);
  }

  return maxLength;
}

console.log(lengthOfLongestSubstring('abcabcbb')); // 3 ('abc')
console.log(lengthOfLongestSubstring('bbbbb')); // 1 ('b')
console.log(lengthOfLongestSubstring('pwwkew')); // 3 ('wke')
```

### Prefix sum

A **prefix sum** array stores cumulative sums, enabling O(1) range sum queries after O(n) preprocessing.

```javascript
function buildPrefixSum(arr) {
  const prefix = new Array(arr.length + 1).fill(0);

  for (let i = 0; i < arr.length; i++) {
    prefix[i + 1] = prefix[i] + arr[i];
  }

  return prefix;
}

function rangeSum(prefix, left, right) {
  // Sum of elements from index left to right (inclusive)
  return prefix[right + 1] - prefix[left];
}

const numbers = [1, 2, 3, 4, 5];
const prefix = buildPrefixSum(numbers);
console.log(prefix); // [0, 1, 3, 6, 10, 15]

console.log(rangeSum(prefix, 1, 3)); // 9 (sum of indices 1-3: 2+3+4)
console.log(rangeSum(prefix, 0, 4)); // 15 (sum of entire array)
console.log(rangeSum(prefix, 2, 2)); // 3 (single element)
```

This pattern is essential when you need to answer many range sum queries on a static array.

### Fast and slow pointers

Also called the "tortoise and hare" pattern, this uses two pointers moving at different speeds.

**Example: Finding the middle element**

```javascript
function findMiddle(arr) {
  let slow = 0;
  let fast = 0;

  while (fast < arr.length - 1 && fast + 1 < arr.length) {
    slow++;
    fast += 2;
  }

  return arr[slow];
}

console.log(findMiddle([1, 2, 3, 4, 5])); // 3
console.log(findMiddle([1, 2, 3, 4, 5, 6])); // 3 (left middle for even length)
```

### Partitioning

**Partitioning** rearranges elements based on a condition, placing elements that satisfy the condition before those that do not.

**Example: Dutch National Flag problem (three-way partition)**

```javascript
function threeWayPartition(arr, pivot) {
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

console.log(threeWayPartition([3, 1, 2, 3, 3, 1, 2], 2));
// [1, 1, 2, 2, 3, 3, 3] - all elements less than 2 first, then 2s, then greater
```

This is the core operation in quicksort and is useful for grouping elements.

### In-place modifications

Modifying an array without using extra space is often required in memory-constrained scenarios.

**Example: Remove duplicates from sorted array in place**

```javascript
function removeDuplicates(arr) {
  if (arr.length === 0) return 0;

  let writeIndex = 1;

  for (let readIndex = 1; readIndex < arr.length; readIndex++) {
    if (arr[readIndex] !== arr[readIndex - 1]) {
      arr[writeIndex] = arr[readIndex];
      writeIndex++;
    }
  }

  // Truncate the array to remove the extra elements
  arr.length = writeIndex;
  return writeIndex;
}

const sorted = [1, 1, 2, 2, 2, 3, 4, 4, 5];
const newLength = removeDuplicates(sorted);
console.log(sorted); // [1, 2, 3, 4, 5]
console.log(newLength); // 5
```

The pattern uses separate read and write pointers, reading ahead and writing only unique values.

## Multi-dimensional arrays

Arrays can contain other arrays, creating multi-dimensional structures.

### Two-dimensional arrays (matrices)

A **matrix** is a 2D array with rows and columns. You access elements with two indices: row and column.

```javascript
// Creating a 3x4 matrix (3 rows, 4 columns)
const matrix = [
  [1, 2, 3, 4],
  [5, 6, 7, 8],
  [9, 10, 11, 12]
];

// Accessing elements: matrix[row][column]
console.log(matrix[0][0]); // 1 (top-left)
console.log(matrix[1][2]); // 7 (second row, third column)
console.log(matrix[2][3]); // 12 (bottom-right)

// Dimensions
const rows = matrix.length; // 3
const cols = matrix[0].length; // 4
```

### Creating matrices dynamically

```javascript
function createMatrix(rows, cols, defaultValue = 0) {
  const matrix = [];

  for (let i = 0; i < rows; i++) {
    matrix[i] = new Array(cols).fill(defaultValue);
  }

  return matrix;
}

const grid = createMatrix(3, 4, 0);
console.log(grid);
// [[0, 0, 0, 0], [0, 0, 0, 0], [0, 0, 0, 0]]
```

### Traversing a matrix

**Row-by-row (most common):**

```javascript
function traverseRowByRow(matrix) {
  const result = [];

  for (let row = 0; row < matrix.length; row++) {
    for (let col = 0; col < matrix[row].length; col++) {
      result.push(matrix[row][col]);
    }
  }

  return result;
}

const matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];
console.log(traverseRowByRow(matrix)); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
```

**Column-by-column:**

```javascript
function traverseColumnByColumn(matrix) {
  const result = [];

  for (let col = 0; col < matrix[0].length; col++) {
    for (let row = 0; row < matrix.length; row++) {
      result.push(matrix[row][col]);
    }
  }

  return result;
}

console.log(traverseColumnByColumn(matrix)); // [1, 4, 7, 2, 5, 8, 3, 6, 9]
```

**Diagonal traversal:**

```javascript
function traverseDiagonal(matrix) {
  const result = [];
  const n = matrix.length;

  // Main diagonal (top-left to bottom-right)
  for (let i = 0; i < n; i++) {
    result.push(matrix[i][i]);
  }

  return result;
}

console.log(traverseDiagonal(matrix)); // [1, 5, 9]
```

### Spiral traversal

A common interview pattern is traversing a matrix in spiral order:

```javascript
function spiralOrder(matrix) {
  if (matrix.length === 0) return [];

  const result = [];
  let top = 0;
  let bottom = matrix.length - 1;
  let left = 0;
  let right = matrix[0].length - 1;

  while (top <= bottom && left <= right) {
    // Traverse right
    for (let col = left; col <= right; col++) {
      result.push(matrix[top][col]);
    }
    top++;

    // Traverse down
    for (let row = top; row <= bottom; row++) {
      result.push(matrix[row][right]);
    }
    right--;

    // Traverse left (if there are rows remaining)
    if (top <= bottom) {
      for (let col = right; col >= left; col--) {
        result.push(matrix[bottom][col]);
      }
      bottom--;
    }

    // Traverse up (if there are columns remaining)
    if (left <= right) {
      for (let row = bottom; row >= top; row--) {
        result.push(matrix[row][left]);
      }
      left++;
    }
  }

  return result;
}

const grid = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
];
console.log(spiralOrder(grid)); // [1, 2, 3, 6, 9, 8, 7, 4, 5]
```

### Memory layout of multi-dimensional arrays

In memory, 2D arrays are typically stored in **row-major order**: all elements of row 0, then all elements of row 1, and so on. This means traversing row-by-row accesses memory sequentially, which is cache-friendly.

Traversing column-by-column jumps around in memory, causing cache misses. For large matrices, this difference can be significant:

```javascript
// Cache-friendly (row-major access)
function sumRowMajor(matrix) {
  let sum = 0;
  for (let row = 0; row < matrix.length; row++) {
    for (let col = 0; col < matrix[row].length; col++) {
      sum += matrix[row][col];
    }
  }
  return sum;
}

// Cache-unfriendly (column-major access)
function sumColumnMajor(matrix) {
  let sum = 0;
  for (let col = 0; col < matrix[0].length; col++) {
    for (let row = 0; row < matrix.length; row++) {
      sum += matrix[row][col];
    }
  }
  return sum;
}

// Both return the same result, but sumRowMajor is faster on large matrices
```

## Real-world use cases

Arrays appear everywhere in software. Here are common applications:

### Storing collections

The most basic use: holding a list of items.

```javascript
const users = ['Alice', 'Bob', 'Charlie'];
const prices = [19.99, 29.99, 39.99];
const flags = [true, false, true, true];
```

### Implementing other data structures

Many data structures use arrays internally:

**Stack using an array:**

```javascript
class Stack {
  constructor() {
    this.items = [];
  }

  push(item) {
    this.items.push(item);
  }

  pop() {
    return this.items.pop();
  }

  peek() {
    return this.items[this.items.length - 1];
  }

  isEmpty() {
    return this.items.length === 0;
  }
}
```

**Queue using an array (with caveats):**

```javascript
class Queue {
  constructor() {
    this.items = [];
  }

  enqueue(item) {
    this.items.push(item);
  }

  dequeue() {
    return this.items.shift(); // O(n) - not ideal
  }

  front() {
    return this.items[0];
  }

  isEmpty() {
    return this.items.length === 0;
  }
}
```

Note: Using shift() makes dequeue O(n). For a true O(1) queue, use a circular buffer or linked list.

**Heap using an array:**

Binary heaps are perfectly represented as arrays. For a node at index i:
- Left child: 2i + 1
- Right child: 2i + 2
- Parent: floor((i - 1) / 2)

```javascript
class MinHeap {
  constructor() {
    this.heap = [];
  }

  parent(i) {
    return Math.floor((i - 1) / 2);
  }

  leftChild(i) {
    return 2 * i + 1;
  }

  rightChild(i) {
    return 2 * i + 2;
  }

  swap(i, j) {
    [this.heap[i], this.heap[j]] = [this.heap[j], this.heap[i]];
  }

  insert(value) {
    this.heap.push(value);
    this.bubbleUp(this.heap.length - 1);
  }

  bubbleUp(index) {
    while (index > 0) {
      const parentIndex = this.parent(index);
      if (this.heap[parentIndex] <= this.heap[index]) break;
      this.swap(parentIndex, index);
      index = parentIndex;
    }
  }

  extractMin() {
    if (this.heap.length === 0) return null;
    if (this.heap.length === 1) return this.heap.pop();

    const min = this.heap[0];
    this.heap[0] = this.heap.pop();
    this.bubbleDown(0);
    return min;
  }

  bubbleDown(index) {
    while (true) {
      const left = this.leftChild(index);
      const right = this.rightChild(index);
      let smallest = index;

      if (left < this.heap.length && this.heap[left] < this.heap[smallest]) {
        smallest = left;
      }
      if (right < this.heap.length && this.heap[right] < this.heap[smallest]) {
        smallest = right;
      }
      if (smallest === index) break;

      this.swap(index, smallest);
      index = smallest;
    }
  }
}

const heap = new MinHeap();
[5, 3, 8, 1, 2].forEach(n => heap.insert(n));
console.log(heap.extractMin()); // 1
console.log(heap.extractMin()); // 2
console.log(heap.extractMin()); // 3
```

### Buffers and streaming

Arrays serve as buffers for I/O operations:

```javascript
// Simulating a ring buffer for streaming data
class RingBuffer {
  constructor(capacity) {
    this.buffer = new Array(capacity);
    this.capacity = capacity;
    this.head = 0;
    this.tail = 0;
    this.size = 0;
  }

  write(value) {
    if (this.size === this.capacity) {
      throw new Error('Buffer full');
    }
    this.buffer[this.tail] = value;
    this.tail = (this.tail + 1) % this.capacity;
    this.size++;
  }

  read() {
    if (this.size === 0) {
      throw new Error('Buffer empty');
    }
    const value = this.buffer[this.head];
    this.head = (this.head + 1) % this.capacity;
    this.size--;
    return value;
  }
}

const buffer = new RingBuffer(3);
buffer.write('a');
buffer.write('b');
console.log(buffer.read()); // 'a'
buffer.write('c');
buffer.write('d');
console.log(buffer.read()); // 'b'
```

### Lookup tables

Arrays enable O(1) lookup when keys are small integers:

```javascript
// Counting character frequencies
function countCharacters(str) {
  const counts = new Array(26).fill(0);

  for (const char of str.toLowerCase()) {
    const index = char.charCodeAt(0) - 'a'.charCodeAt(0);
    if (index >= 0 && index < 26) {
      counts[index]++;
    }
  }

  return counts;
}

const freq = countCharacters('hello');
// freq[7] = 1 (h), freq[4] = 1 (e), freq[11] = 2 (l), freq[14] = 1 (o)
```

### Image processing

Images are 2D arrays of pixel values:

```javascript
// Grayscale image as a 2D array of brightness values (0-255)
const image = [
  [100, 150, 200],
  [50, 100, 150],
  [0, 50, 100]
];

// Invert colors
function invertImage(img) {
  const result = [];
  for (let row = 0; row < img.length; row++) {
    result[row] = [];
    for (let col = 0; col < img[row].length; col++) {
      result[row][col] = 255 - img[row][col];
    }
  }
  return result;
}

console.log(invertImage(image));
// [[155, 105, 55], [205, 155, 105], [255, 205, 155]]
```

### Time series and logs

Sequential data is naturally represented as arrays:

```javascript
const temperatures = [
  { time: '09:00', temp: 68 },
  { time: '10:00', temp: 72 },
  { time: '11:00', temp: 75 },
  { time: '12:00', temp: 78 }
];

// Find maximum temperature
const maxTemp = temperatures.reduce((max, current) =>
  current.temp > max.temp ? current : max
);
console.log(maxTemp); // { time: '12:00', temp: 78 }
```

## Edge cases and pitfalls

Arrays have subtle behaviors that can cause bugs. Here are the most common issues.

### Off-by-one errors

The most common array bug. Remember: indices go from 0 to length - 1.

```javascript
const arr = [1, 2, 3, 4, 5];

// Wrong: accessing arr[5] when length is 5
console.log(arr[arr.length]); // undefined (not an error in JavaScript)

// Correct: last element is at length - 1
console.log(arr[arr.length - 1]); // 5

// Wrong: loop goes one too far
for (let i = 0; i <= arr.length; i++) {
  console.log(arr[i]); // Last iteration logs undefined
}

// Correct
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]);
}
```

### Empty array handling

Always consider what happens when the array has zero elements:

```javascript
function findMax(arr) {
  // Bug: calling Math.max with empty spread returns -Infinity
  return Math.max(...arr);
}
console.log(findMax([])); // -Infinity

// Better: handle empty case explicitly
function findMaxSafe(arr) {
  if (arr.length === 0) return null;
  return Math.max(...arr);
}
console.log(findMaxSafe([])); // null
```

### Single element arrays

Edge case between empty and "normal" arrays:

```javascript
function findMiddle(arr) {
  // Works for odd-length arrays
  return arr[Math.floor(arr.length / 2)];
}

console.log(findMiddle([1])); // 1 (correct)
console.log(findMiddle([])); // undefined (should we return null?)
```

### Negative indices

JavaScript does not support Python-style negative indexing:

```javascript
const arr = [1, 2, 3, 4, 5];

// This does NOT get the last element in JavaScript
console.log(arr[-1]); // undefined

// Use at() for negative indexing (ES2022+)
console.log(arr.at(-1)); // 5

// Or calculate the index
console.log(arr[arr.length - 1]); // 5
```

### Mutating while iterating

Modifying an array while looping over it causes subtle bugs:

```javascript
// Bug: removing elements shifts indices
const numbers = [1, 2, 3, 4, 5, 6];
for (let i = 0; i < numbers.length; i++) {
  if (numbers[i] % 2 === 0) {
    numbers.splice(i, 1); // Removes element, shifts remaining
    // Now we skip the next element because i increments
  }
}
console.log(numbers); // [1, 3, 5] - works by accident, but buggy logic

// Fix: iterate backwards
const nums = [1, 2, 3, 4, 5, 6];
for (let i = nums.length - 1; i >= 0; i--) {
  if (nums[i] % 2 === 0) {
    nums.splice(i, 1);
  }
}
console.log(nums); // [1, 3, 5]

// Or use filter to create a new array
const original = [1, 2, 3, 4, 5, 6];
const odds = original.filter(n => n % 2 !== 0);
console.log(odds); // [1, 3, 5]
```

### Shallow vs. deep copy

Copying arrays of objects copies references, not the objects themselves:

```javascript
const original = [{ x: 1 }, { x: 2 }];

// Shallow copy: objects are shared
const shallow = [...original];
shallow[0].x = 100;
console.log(original[0].x); // 100 (original was mutated!)

// Deep copy: objects are cloned
const deep = JSON.parse(JSON.stringify(original));
deep[0].x = 999;
console.log(original[0].x); // 100 (original is unchanged)
```

### Sparse arrays

JavaScript arrays can have gaps:

```javascript
const sparse = [1, 2, 3];
sparse[10] = 11;

console.log(sparse.length); // 11
console.log(sparse[5]); // undefined
console.log(sparse); // [1, 2, 3, <7 empty items>, 11]

// Some methods skip holes
sparse.forEach((val, i) => console.log(i, val));
// 0 1
// 1 2
// 2 3
// 10 11
// (indices 3-9 are skipped)
```

### Array-like objects

Some things look like arrays but are not:

```javascript
// Arguments object (in non-arrow functions)
function example() {
  console.log(arguments.length); // Works
  console.log(arguments.map); // undefined - not a real array!

  // Convert to real array
  const args = Array.from(arguments);
  console.log(args.map(x => x * 2)); // Now it works
}

// DOM NodeList
const elements = document.querySelectorAll('div');
// elements.forEach exists, but elements.map does not
const arr = Array.from(elements); // Convert to real array
```

### Integer overflow in indices

In most languages (not JavaScript), array indices are limited by integer size:

```javascript
// JavaScript numbers are doubles, so this is less of an issue
// But arrays have a practical limit
const arr = [];
arr[2 ** 32] = 'too big';
console.log(arr.length); // 0 (index was treated as a property, not an element)
```

### Reference vs. value comparison

When searching, remember that objects are compared by reference:

```javascript
const objects = [{ id: 1 }, { id: 2 }, { id: 3 }];
const target = { id: 2 };

console.log(objects.indexOf(target)); // -1 (different object, even though contents match)
console.log(objects.findIndex(obj => obj.id === target.id)); // 1 (correct)
```

## JavaScript array methods reference

JavaScript provides many built-in array methods. Here is a quick reference for the most useful ones:

### Mutating methods (modify the original array)

```javascript
const arr = [1, 2, 3];

// Add/remove at end
arr.push(4);        // [1, 2, 3, 4] - returns new length
arr.pop();          // [1, 2, 3] - returns removed element

// Add/remove at beginning
arr.unshift(0);     // [0, 1, 2, 3] - returns new length
arr.shift();        // [1, 2, 3] - returns removed element

// Modify in place
arr.splice(1, 1, 'a', 'b'); // [1, 'a', 'b', 3] - removes 1 element at index 1, inserts 'a' and 'b'
arr.reverse();      // [3, 'b', 'a', 1]
arr.sort();         // [1, 3, 'a', 'b'] - sorts as strings by default
arr.fill(0);        // [0, 0, 0, 0]
```

### Non-mutating methods (return new arrays)

```javascript
const arr = [1, 2, 3, 4, 5];

arr.slice(1, 4);        // [2, 3, 4] - elements from index 1 to 3
arr.concat([6, 7]);     // [1, 2, 3, 4, 5, 6, 7]
arr.map(x => x * 2);    // [2, 4, 6, 8, 10]
arr.filter(x => x > 2); // [3, 4, 5]
arr.toReversed();       // [5, 4, 3, 2, 1] (ES2023, original unchanged)
arr.toSorted();         // [1, 2, 3, 4, 5] (ES2023, original unchanged)
```

### Search methods

```javascript
const arr = [1, 2, 3, 2, 1];

arr.indexOf(2);         // 1 (first occurrence)
arr.lastIndexOf(2);     // 3 (last occurrence)
arr.includes(3);        // true
arr.find(x => x > 2);   // 3 (first element matching condition)
arr.findIndex(x => x > 2); // 2 (index of first match)
```

### Iteration methods

```javascript
const arr = [1, 2, 3, 4, 5];

arr.forEach(x => console.log(x)); // Logs each element
arr.reduce((sum, x) => sum + x, 0); // 15 (reduces to single value)
arr.every(x => x > 0);  // true (all elements match)
arr.some(x => x > 4);   // true (at least one matches)
```

## Summary

Arrays are fundamental to programming because they provide the rare combination of simplicity and efficiency. Their contiguous memory layout enables constant-time random access and excellent cache performance for sequential operations.

Key takeaways:

- **Contiguous memory** is the defining feature. Elements are stored consecutively, enabling O(1) access via index arithmetic.
- **Access and update are O(1)**. This is the array's primary advantage over linked structures.
- **Insert and delete are O(n)** for arbitrary positions because elements must shift. End operations are O(1).
- **Dynamic arrays** grow automatically by reallocating and copying. This is O(n) worst case but O(1) amortized.
- **Cache locality** makes arrays much faster in practice than big-O analysis suggests. Sequential access is extremely efficient.
- **Common patterns** like two pointers, sliding window, and prefix sums solve many problems elegantly.
- **Multi-dimensional arrays** represent matrices and grids. Row-major traversal is cache-friendly.
- **Edge cases** like empty arrays, off-by-one errors, and mutation during iteration cause common bugs.

Arrays are not always the best choice. When you need frequent middle insertions, consider linked lists. When you need fast membership testing, consider hash sets. When you need sorted iteration with fast updates, consider balanced trees. But when you need fast random access and sequential processing, arrays are usually optimal.

Master arrays first. They are the foundation on which you build understanding of more complex structures.
