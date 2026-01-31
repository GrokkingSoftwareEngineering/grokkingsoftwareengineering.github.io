# Constant

Reading time: 25 minutes

**Constant time complexity**, written as **O(1)**, describes operations whose execution time does not depend on the input size. Whether you have 10 elements or 10 million, the operation takes roughly the same amount of time. This is the fastest complexity class and the ideal for operations you perform frequently.

## What O(1) means

An O(1) operation completes in a fixed amount of time regardless of how much data you have. The "1" does not mean "one operation" or "one millisecond." It means the time is constant, bounded by some fixed value that does not grow with input size.

More formally, an algorithm is O(1) if there exists a constant c such that the running time is at most c for all input sizes n.

The formal definition states that f(n) = O(1) if there exist positive constants c and n₀ such that f(n) ≤ c for all n ≥ n₀. This means the function is bounded above by some constant, regardless of input size.

## The mechanics of constant time

To understand why certain operations are O(1), you need to understand how computers access memory. Modern computers use **random access memory** (RAM), which allows any memory location to be accessed in the same amount of time. This is in contrast to sequential access (like magnetic tape) where accessing later elements takes longer.

When data is stored at a known address, retrieving it requires:
1. Calculate the memory address
2. Request data from that address
3. Receive the data

Each of these steps takes a fixed amount of time, regardless of how much other data exists in memory.

## Examples of O(1) operations

### Array access by index

Accessing an array element by its index is the canonical O(1) operation:

```javascript
function getElement(arr, index) {
  return arr[index];  // O(1)
}
```

Arrays store elements in contiguous memory. Given the starting address and element size, the computer calculates the exact memory location with simple arithmetic: `address = start + (index × elementSize)`. This calculation takes constant time regardless of array size.

The formula works because arrays guarantee contiguous storage. Element 0 is at the starting address, element 1 is one element-size later, and so on. This predictable layout enables O(1) access to any element.

### Array assignment by index

Setting an array element is also O(1):

```javascript
function setElement(arr, index, value) {
  arr[index] = value;  // O(1)
  return arr;
}
```

Like reading, writing requires calculating an address and performing a memory operation. The array length does not affect how long this takes.

### Hash table operations

Hash tables (JavaScript objects, Maps, Sets) provide O(1) average-case operations:

```javascript
const map = new Map();

// All O(1) average case
map.set('key', 'value');
map.get('key');
map.has('key');
map.delete('key');
```

Hash tables compute a hash of the key to find the storage location directly. Instead of searching through all stored items, they jump directly to where the item should be.

The hash function converts any key into a number (the hash). This number, after some modular arithmetic, gives an array index. Looking up a key means:
1. Compute the hash (O(1) for fixed-size keys)
2. Convert to array index (O(1))
3. Access array element (O(1))

Collisions can degrade performance, but with a good hash function and appropriate load factor, operations remain O(1) on average.

### Stack push and pop

Stacks support O(1) insertion and removal at one end:

```javascript
const stack = [];

stack.push(1);  // O(1) amortized
stack.push(2);
stack.pop();    // O(1)
```

Push and pop operate on the end of the array, requiring no shifting of elements. The operation directly accesses the last position.

### Queue operations with proper implementation

Queues support O(1) operations when implemented correctly:

```javascript
class Queue {
  constructor() {
    this.items = {};
    this.head = 0;
    this.tail = 0;
  }

  enqueue(item) {
    this.items[this.tail] = item;
    this.tail++;
    return this;  // O(1)
  }

  dequeue() {
    if (this.head === this.tail) return undefined;
    const item = this.items[this.head];
    delete this.items[this.head];
    this.head++;
    return item;  // O(1)
  }

  peek() {
    return this.items[this.head];  // O(1)
  }

  get size() {
    return this.tail - this.head;  // O(1)
  }
}
```

Using an object with head/tail pointers avoids the O(n) cost of array shift operations.

### Arithmetic and logical operations

Basic computations on fixed-size values are O(1):

```javascript
function compute(a, b) {
  const sum = a + b;        // O(1)
  const product = a * b;    // O(1)
  const isEqual = a === b;  // O(1)
  const bitwise = a & b;    // O(1)
  const comparison = a > b; // O(1)
  return sum + product;
}
```

These operations work on numbers of fixed size (typically 64 bits). The CPU executes them in a single instruction or a small fixed number of instructions.

However, arithmetic on arbitrary-precision numbers (BigInt) is NOT O(1). Adding two n-digit numbers requires O(n) digit-by-digit addition.

### Linked list head operations

Inserting or removing at the head of a linked list is O(1):

```javascript
class LinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }

  prepend(value) {
    const newNode = { value, next: this.head };
    this.head = newNode;
    if (this.tail === null) {
      this.tail = newNode;
    }
    this.length++;
    return this;  // O(1)
  }

  removeFirst() {
    if (this.head === null) return undefined;
    const value = this.head.value;
    this.head = this.head.next;
    if (this.head === null) {
      this.tail = null;
    }
    this.length--;
    return value;  // O(1)
  }

  append(value) {
    const newNode = { value, next: null };
    if (this.tail === null) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      this.tail.next = newNode;
      this.tail = newNode;
    }
    this.length++;
    return this;  // O(1) with tail pointer
  }
}
```

Only pointer updates are needed, regardless of list length. The key is maintaining direct references to the positions where operations occur.

### Getting collection size

Well-designed collections maintain their size, making length queries O(1):

```javascript
const arr = [1, 2, 3, 4, 5];
const len = arr.length;  // O(1) - stored property

const set = new Set([1, 2, 3]);
const size = set.size;  // O(1) - stored property
```

The data structure updates the count during insertions and deletions rather than counting elements on demand.

## O(1) does not mean fast

A common misconception is that O(1) means "fast" or "instant." It means the time is constant, not small.

Consider an O(1) operation that takes 1 second. It is still O(1) because it takes 1 second whether you have 1 element or 1 billion. Compare this to an O(n) operation that takes 1 millisecond per element. For small inputs, the O(n) operation is faster:

| Input Size | O(1) at 1 second | O(n) at 1ms per element |
|------------|------------------|-------------------------|
| 10 | 1 second | 10 milliseconds |
| 100 | 1 second | 100 milliseconds |
| 1,000 | 1 second | 1 second |
| 10,000 | 1 second | 10 seconds |

The O(1) operation becomes faster than O(n) only when n exceeds 1,000.

In practice, O(1) operations usually are fast because they involve simple memory lookups or arithmetic. But the guarantee is about scaling, not absolute speed.

### Constant factors in O(1) operations

Different O(1) operations have different constant factors:

| Operation | Typical time |
|-----------|--------------|
| Integer addition | ~1 nanosecond |
| Floating-point division | ~10 nanoseconds |
| Array access (cached) | ~1 nanosecond |
| Array access (uncached) | ~100 nanoseconds |
| Hash table lookup | ~50-200 nanoseconds |
| Disk block read | ~10 milliseconds |

All are O(1), but their actual speeds differ by factors of millions.

## Amortized O(1)

Some operations are O(1) **amortized**, meaning they are usually O(1) but occasionally more expensive. The expensive operations are rare enough that they average out to O(1) over many calls.

### Dynamic array growth

```javascript
const arr = [];
for (let i = 0; i < 1000000; i++) {
  arr.push(i);  // O(1) amortized
}
```

Most push operations are O(1), but when the array's capacity is exceeded, it must allocate new memory and copy all elements, which is O(n). If the array doubles in size each time, this happens only O(log n) times for n insertions, averaging to O(1) per insertion.

Here is the math: If we start with capacity 1 and double each time, insertions trigger copying at sizes 1, 2, 4, 8, 16, ..., up to n. Total copying work: 1 + 2 + 4 + 8 + ... + n ≤ 2n. For n insertions, total work is at most 2n, which is O(1) per insertion on average.

### Hash table resizing

Hash tables occasionally rehash all elements when load factor gets too high:

```javascript
class SimpleHashTable {
  constructor(initialCapacity = 16) {
    this.buckets = new Array(initialCapacity).fill(null);
    this.size = 0;
    this.loadFactor = 0.75;
  }

  set(key, value) {
    if (this.size / this.buckets.length > this.loadFactor) {
      this.resize();  // O(n) occasionally
    }
    // Regular insertion: O(1)
    const index = this.hash(key) % this.buckets.length;
    // ... insert logic
    this.size++;
  }

  resize() {
    const oldBuckets = this.buckets;
    this.buckets = new Array(oldBuckets.length * 2).fill(null);
    this.size = 0;
    for (const bucket of oldBuckets) {
      // Rehash all elements: O(n)
      // ... rehash logic
    }
  }

  hash(key) {
    // Simple hash function
    let hash = 0;
    for (const char of String(key)) {
      hash = (hash * 31 + char.charCodeAt(0)) | 0;
    }
    return Math.abs(hash);
  }
}
```

Like dynamic arrays, this is O(n) occasionally but O(1) amortized per operation.

### Understanding amortized bounds

The banker's method helps understand amortized complexity. Imagine each cheap operation "deposits" extra time into a bank account, and expensive operations "withdraw" from this account.

For dynamic arrays:
- Each push deposits 3 time units
- When resizing triggers, the accumulated deposits pay for copying
- As long as deposits exceed withdrawals, the amortized cost remains O(1)

## When O(1) is not really O(1)

Some operations that appear to be O(1) have hidden costs:

### Large constant factors

An "O(1)" operation might do significant work that does not depend on n but is still slow:

```javascript
// O(1) but slow
function hashLargeObject(obj) {
  // Computes hash of a fixed-size object with 1000 fields
  // Still O(1) because object size is fixed, but takes time
  let hash = 0;
  for (const key of Object.keys(obj)) {
    hash ^= stringHash(key) ^ valueHash(obj[key]);
  }
  return hash;
}
```

If the object always has the same structure, this is technically O(1). But if the number of fields varies with some input parameter, it becomes O(fields).

### Hidden input dependency

Some operations depend on input characteristics other than the primary n:

```javascript
// O(1) for array access, but what about the value?
function processValue(arr, index) {
  const value = arr[index];  // O(1)
  return value.toString();   // O(digits in value) - NOT O(1)!
}

// String comparison depends on string length
function compareStrings(s1, s2) {
  return s1 === s2;  // O(min(len(s1), len(s2)))
}

// Object comparison may traverse the object
function deepEqual(obj1, obj2) {
  return JSON.stringify(obj1) === JSON.stringify(obj2);  // O(size of objects)
}
```

Be careful to identify all the variables that affect running time, not just the obvious one.

### Memory hierarchy effects

CPU cache behavior can make O(1) operations slower when data is not in cache:

```javascript
// Both O(1), but different real-world speeds
const value1 = arr[0];      // Likely in cache: ~1 ns
const value2 = arr[random]; // Might cause cache miss: ~100 ns
```

Accessing memory in random order can be 100× slower than sequential access, even though both are O(1).

### Virtual memory and paging

When data exceeds physical RAM, the operating system uses disk as virtual memory. Accessing paged-out data causes page faults:

```javascript
// O(1) in theory, but potentially very slow
function accessLargeArray(arr, index) {
  return arr[index];  // Could trigger page fault: ~10 ms
}
```

A page fault can take 10 million times longer than a cached memory access, even though both are O(1).

## Recognizing O(1) patterns

Operations are typically O(1) when they:

- Access a known memory location directly (array indexing, pointer dereferencing)
- Perform a fixed number of steps regardless of input
- Use hash functions to locate data
- Modify only the ends of a data structure (stack, queue with tail pointer)
- Read or update a maintained property (length, size, sum)

Operations are NOT O(1) when they:

- Must examine each element (searching, summing)
- Shift elements (array insertion in middle)
- Depend on the value's magnitude (printing a number, string operations)
- Traverse a structure to find a position (linked list by index)
- Require comparison of variable-length data (string equality)

## O(1) space complexity

Some algorithms use O(1) space, meaning they require only a fixed amount of extra memory regardless of input size:

### Swapping without extra array

```javascript
function reverseInPlace(arr) {
  let left = 0;
  let right = arr.length - 1;
  while (left < right) {
    // Swap using only two indices (O(1) space)
    const temp = arr[left];
    arr[left] = arr[right];
    arr[right] = temp;
    left++;
    right--;
  }
  return arr;  // O(1) extra space, O(n) time
}
```

### Running calculations

```javascript
function findMax(arr) {
  let max = arr[0];  // O(1) extra space
  for (const num of arr) {
    if (num > max) max = num;
  }
  return max;  // O(1) space, O(n) time
}
```

O(1) space algorithms are valuable when memory is constrained or when you want to modify data in place.

## O(1) in algorithm design

When designing algorithms, aim for O(1) operations in hot paths, code that runs frequently. Common strategies:

### Precomputation

Calculate values once and store them for O(1) lookup:

```javascript
// O(n) to build, O(1) to query
function createSquaresLookup(maxN) {
  const squares = new Map();
  for (let i = 0; i <= maxN; i++) {
    squares.set(i, i * i);
  }
  return squares;
}

const squares = createSquaresLookup(1000);
// Later: O(1) lookups
const result = squares.get(42);  // 1764
```

This trades O(n) space for O(1) query time.

### Direct addressing

Use arrays when keys are small integers:

```javascript
// O(1) lookup by ID
function createUserIndex(users) {
  const index = [];
  for (const user of users) {
    index[user.id] = user;  // Direct addressing
  }
  return index;
}

const userIndex = createUserIndex(users);
const user = userIndex[123];  // O(1)
```

Direct addressing is faster than hash tables when keys are dense integers.

### Maintaining state

Keep track of information incrementally rather than recalculating:

```javascript
class RunningStats {
  constructor() {
    this.count = 0;
    this.sum = 0;
    this.min = Infinity;
    this.max = -Infinity;
  }

  add(value) {
    this.count++;
    this.sum += value;
    if (value < this.min) this.min = value;
    if (value > this.max) this.max = value;
    // All O(1)
  }

  getAverage() {
    return this.count === 0 ? 0 : this.sum / this.count;  // O(1)
  }

  getMin() { return this.min; }  // O(1)
  getMax() { return this.max; }  // O(1)
  getCount() { return this.count; }  // O(1)
  getSum() { return this.sum; }  // O(1)
}
```

Each addition is O(1), and all statistics are available in O(1).

### Trading accuracy for speed

Some O(1) algorithms provide approximate answers:

```javascript
// Approximate membership test: O(1) but may have false positives
class BloomFilter {
  constructor(size, hashCount) {
    this.bits = new Array(size).fill(false);
    this.hashCount = hashCount;
    this.size = size;
  }

  add(item) {
    for (let i = 0; i < this.hashCount; i++) {
      const index = this.hash(item, i) % this.size;
      this.bits[index] = true;
    }
    // O(hashCount) = O(1) since hashCount is constant
  }

  mightContain(item) {
    for (let i = 0; i < this.hashCount; i++) {
      const index = this.hash(item, i) % this.size;
      if (!this.bits[index]) return false;
    }
    return true;  // Might be false positive
    // O(hashCount) = O(1)
  }

  hash(item, seed) {
    // Simple hash combining item and seed
    let h = seed;
    for (const char of String(item)) {
      h = (h * 31 + char.charCodeAt(0)) | 0;
    }
    return Math.abs(h);
  }
}
```

Bloom filters provide O(1) membership testing with some probability of false positives.

## Common pitfalls

### Assuming library operations are O(1)

Some seemingly simple operations are not O(1):

```javascript
// NOT O(1)
arr.shift();           // O(n) - shifts all elements
arr.unshift(x);        // O(n) - shifts all elements
arr.splice(0, 1);      // O(n) - shifts elements after position 0
arr.includes(x);       // O(n) - linear search
arr.indexOf(x);        // O(n) - linear search
[...arr];              // O(n) - copies all elements
arr.slice();           // O(n) - copies all elements

// O(1)
arr.push(x);           // O(1) amortized
arr.pop();             // O(1)
arr[i];                // O(1)
arr.length;            // O(1)
```

### Assuming hash operations are always O(1)

Hash table operations are O(1) average case but can degrade:

```javascript
// Can be O(n) if all keys hash to same bucket
const badHashTable = new Map();
// If hash function is poor or keys are adversarial,
// all operations might traverse a long chain
```

In the worst case (all keys collide), hash table operations become O(n).

### Ignoring the cost of key comparison

Even O(1) hash lookups require comparing keys:

```javascript
const map = new Map();
map.set('very_long_string_key_that_takes_time_to_compare', value);
// Lookup is O(1) + O(key length)
```

For long keys, comparison overhead can dominate.

## Summary

- **O(1)** means execution time is constant, independent of input size.
- Common O(1) operations: array access, hash table operations, stack push/pop, arithmetic.
- O(1) does not mean "fast," just that time does not grow with input.
- **Amortized O(1)** operations are usually O(1) but occasionally more expensive.
- Watch for hidden costs: large constants, input-dependent work, cache effects.
- O(1) space means using only a fixed amount of extra memory.
- Design for O(1) in frequently executed code paths using precomputation, direct addressing, and incremental state.
- Many seemingly O(1) operations (array shift, string comparison) are actually O(n).
- The constant in O(1) can vary by factors of millions between different operations.
