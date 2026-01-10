# Constant

Reading time: 7 minutes

**Constant time complexity**, written as **O(1)**, describes operations whose execution time does not depend on the input size. Whether you have 10 elements or 10 million, the operation takes roughly the same amount of time. This is the fastest complexity class and the ideal for operations you perform frequently.

## What O(1) means

An O(1) operation completes in a fixed amount of time regardless of how much data you have. The "1" does not mean "one operation" or "one millisecond." It means the time is constant, bounded by some fixed value that does not grow with input size.

More formally, an algorithm is O(1) if there exists a constant c such that the running time is at most c for all input sizes n.

## Examples of O(1) operations

### Array access by index

Accessing an array element by its index is the canonical O(1) operation:

```javascript
function getElement(arr, index) {
  return arr[index];  // O(1)
}
```

Arrays store elements in contiguous memory. Given the starting address and element size, the computer calculates the exact memory location with simple arithmetic: `address = start + (index × elementSize)`. This calculation takes constant time regardless of array size.

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

Hash tables compute a hash of the key to find the storage location directly. Collisions can degrade performance, but with a good hash function, operations remain O(1) on average.

### Stack push and pop

Stacks support O(1) insertion and removal at one end:

```javascript
const stack = [];

stack.push(1);  // O(1) amortized
stack.push(2);
stack.pop();    // O(1)
```

Push and pop operate on the end of the array, requiring no shifting of elements.

### Arithmetic and logical operations

Basic computations on fixed-size values are O(1):

```javascript
function compute(a, b) {
  const sum = a + b;        // O(1)
  const product = a * b;    // O(1)
  const isEqual = a === b;  // O(1)
  return sum + product;
}
```

These operations work on numbers of fixed size (typically 64 bits). The CPU executes them in a single instruction or a small fixed number of instructions.

### Linked list head operations

Inserting or removing at the head of a linked list is O(1):

```javascript
class LinkedList {
  constructor() {
    this.head = null;
  }

  prepend(value) {
    const newNode = { value, next: this.head };
    this.head = newNode;  // O(1)
  }

  removeFirst() {
    if (this.head) {
      this.head = this.head.next;  // O(1)
    }
  }
}
```

Only pointer updates are needed, regardless of list length.

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

## Amortized O(1)

Some operations are O(1) **amortized**, meaning they are usually O(1) but occasionally more expensive. The expensive operations are rare enough that they average out to O(1) over many calls.

**Dynamic array growth:**

```javascript
const arr = [];
for (let i = 0; i < 1000000; i++) {
  arr.push(i);  // O(1) amortized
}
```

Most push operations are O(1), but when the array's capacity is exceeded, it must allocate new memory and copy all elements, which is O(n). If the array doubles in size each time, this happens only O(log n) times for n insertions, averaging to O(1) per insertion.

**Hash table resizing:**

Hash tables occasionally rehash all elements when load factor gets too high. Like dynamic arrays, this is O(n) occasionally but O(1) amortized per operation.

## When O(1) is not really O(1)

Some operations that appear to be O(1) have hidden costs:

**Large constant factors:**

An "O(1)" operation might do significant work that does not depend on n but is still slow:

```javascript
// O(1) but slow
function hashLargeObject(obj) {
  // Computes hash of a fixed-size object with 1000 fields
  // Still O(1) because object size is fixed, but takes time
}
```

**Hidden input dependency:**

Some operations depend on input characteristics other than n:

```javascript
// O(1) for array access, but what about the value?
function processValue(arr, index) {
  const value = arr[index];  // O(1)
  return value.toString();   // O(digits in value) - NOT O(1)!
}
```

**Memory hierarchy effects:**

CPU cache behavior can make O(1) operations slower when data is not in cache:

```javascript
// Both O(1), but different real-world speeds
const value1 = arr[0];      // Likely in cache
const value2 = arr[random]; // Might cause cache miss
```

## Recognizing O(1) patterns

Operations are typically O(1) when they:

- Access a known memory location directly (array indexing, pointer dereferencing)
- Perform a fixed number of steps regardless of input
- Use hash functions to locate data
- Modify only the ends of a data structure (stack, queue with tail pointer)

Operations are NOT O(1) when they:

- Must examine each element (searching, summing)
- Shift elements (array insertion in middle)
- Depend on the value's magnitude (printing a number, string operations)
- Traverse a structure to find a position (linked list by index)

## O(1) in algorithm design

When designing algorithms, aim for O(1) operations in hot paths, code that runs frequently. Common strategies:

**Precomputation:** Calculate values once and store them for O(1) lookup:

```javascript
// O(n) to build, O(1) to query
const squares = new Map();
for (let i = 0; i < 1000; i++) {
  squares.set(i, i * i);
}
// Later: O(1)
const result = squares.get(42);
```

**Direct addressing:** Use arrays when keys are small integers:

```javascript
// O(1) lookup by ID
const users = [];
users[123] = { name: 'Alice' };
users[456] = { name: 'Bob' };
const user = users[123];  // O(1)
```

**Maintaining state:** Keep track of information incrementally rather than recalculating:

```javascript
class RunningSum {
  constructor() {
    this.sum = 0;
  }

  add(value) {
    this.sum += value;  // O(1)
  }

  getSum() {
    return this.sum;    // O(1)
  }
}
```

## Summary

- **O(1)** means execution time is constant, independent of input size.
- Common O(1) operations: array access, hash table operations, stack push/pop, arithmetic.
- O(1) does not mean "fast," just that time does not grow with input.
- **Amortized O(1)** operations are usually O(1) but occasionally more expensive.
- Watch for hidden costs: large constants, input-dependent work, cache effects.
- Design for O(1) in frequently executed code paths.
