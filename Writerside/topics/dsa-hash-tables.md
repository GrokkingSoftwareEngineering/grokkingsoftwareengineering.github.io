# Hash Tables

Reading time: 25 minutes

A **hash table** is a data structure that stores key-value pairs and provides average-case constant-time operations for insert, delete, and lookup. It achieves this speed by using a **hash function** to compute an index into an array of buckets, where the value is stored. Hash tables are one of the most useful data structures in all of programming, appearing everywhere from database indexes to in-memory caches to the objects and dictionaries built into most programming languages.

This page covers how hash tables work internally, the strategies they use to handle collisions, how they resize, and when you should (and should not) use them. You will also learn to implement a hash table from scratch, understand the difference between hash maps and hash sets, and recognize the trade-offs that make hash tables so effective.

## How hash tables work

At its core, a hash table is an array combined with a function. The function takes a key and produces an index. The index tells you where to look in the array.

Here is the basic idea:

1. You want to store a value associated with a key.
2. You pass the key through a hash function, which returns a number.
3. You use that number (modulo the array size) as an index.
4. You store the value at that index.

When you want to retrieve the value, you repeat the process: hash the key, compute the index, look at that position.

This works beautifully when every key hashes to a different index. In practice, multiple keys often hash to the same index. This is called a **collision**, and handling collisions is where most of the complexity in hash table design lives.

### A minimal example

```javascript
// A simple hash table with a fixed size
function createHashTable(size) {
  const buckets = new Array(size).fill(null);

  function hash(key) {
    let hashValue = 0;
    for (let i = 0; i < key.length; i++) {
      hashValue = (hashValue * 31 + key.charCodeAt(i)) % size;
    }
    return hashValue;
  }

  return {
    set(key, value) {
      const index = hash(key);
      buckets[index] = { key, value };
    },

    get(key) {
      const index = hash(key);
      const bucket = buckets[index];
      if (bucket && bucket.key === key) {
        return bucket.value;
      }
      return undefined;
    }
  };
}

const table = createHashTable(16);
table.set("name", "Alice");
table.set("age", 30);

console.log(table.get("name")); // "Alice"
console.log(table.get("age")); // 30
```

This example ignores collisions entirely. If two keys hash to the same index, the second one overwrites the first. That is a bug, not a feature. Real hash tables handle collisions properly.

## Hash functions

A **hash function** takes input of arbitrary size and produces output of fixed size. For hash tables, the input is a key and the output is an integer that becomes an array index.

### Properties of good hash functions

A good hash function for hash tables should have these properties:

**Deterministic.** The same key must always produce the same hash value. If `hash("apple")` returns 42 today, it must return 42 tomorrow. Without this property, you could not retrieve values after storing them.

**Uniform distribution.** Keys should spread evenly across possible hash values. If most keys hash to a small range, those buckets become crowded while others sit empty. The goal is to minimize collisions by using the entire bucket space.

**Fast to compute.** Hashing happens on every operation. If hashing is slow, your hash table is slow. A good hash function should be O(1) for fixed-size keys and O(k) for variable-size keys of length k.

**Sensitive to input changes.** Similar keys should produce very different hash values. If "cat" and "car" hash to adjacent indices, inputs with similar patterns will cluster together. This is sometimes called the **avalanche effect**: small input changes cause large output changes.

### Hash functions for strings

String hashing typically involves iterating through characters and combining them mathematically. The polynomial rolling hash is a common approach:

```javascript
function hashString(str, tableSize) {
  let hash = 0;
  const prime = 31; // A small prime number

  for (let i = 0; i < str.length; i++) {
    hash = (hash * prime + str.charCodeAt(i)) % tableSize;
  }

  return hash;
}
```

The prime multiplier helps spread bits across the hash value. Using 31 is a common choice because multiplication by 31 can be optimized to a shift and subtraction (`x * 31 === (x << 5) - x`), and 31 is large enough to create good distribution.

### Hash functions for numbers

For integers, you can use the number directly, but this creates problems if your keys have patterns. Sequential IDs (1, 2, 3, 4) would hash to sequential buckets, which is fine until you combine it with a table size that creates clustering.

A better approach uses bit manipulation to scramble the input:

```javascript
function hashInteger(n, tableSize) {
  // A simple but effective integer hash
  n = ((n >> 16) ^ n) * 0x45d9f3b;
  n = ((n >> 16) ^ n) * 0x45d9f3b;
  n = (n >> 16) ^ n;
  return Math.abs(n) % tableSize;
}
```

### Hash functions for objects

Hashing objects requires deciding which properties contribute to identity. Two common approaches:

**Hash by reference.** Use the object's memory address or an internal ID. This is fast but means two objects with identical content have different hashes.

**Hash by content.** Combine hashes of the object's properties. This is slower and requires careful handling of nested objects and circular references.

JavaScript's `Map` uses reference equality for object keys by default. If you need content-based hashing, you must implement it yourself or use a library.

```javascript
function hashObject(obj, tableSize) {
  // Convert object to a string representation, then hash that
  const str = JSON.stringify(obj);
  return hashString(str, tableSize);
}
```

This is simple but slow for large objects. Production systems use more sophisticated approaches.

### Cryptographic vs. non-cryptographic hash functions

Hash tables use **non-cryptographic** hash functions. These are fast but not secure. An attacker who knows your hash function can craft inputs that all collide, degrading your O(1) operations to O(n).

**Cryptographic** hash functions (like SHA-256) are designed to resist this attack but are much slower. Use them for security purposes (password hashing, data integrity), not for hash tables.

Some hash table implementations use **randomized** hash functions, adding a random seed that changes between runs. This prevents attackers from predicting collisions without significantly slowing down hashing.

## Collision handling strategies

When two keys hash to the same index, you need a strategy to handle it. The two main families are **chaining** and **open addressing**.

### Chaining (separate chaining)

**Chaining** stores multiple entries at each bucket by using a secondary data structure, typically a linked list. When a collision occurs, you add the new entry to the list at that bucket.

```javascript
function createChainedHashTable(size) {
  const buckets = new Array(size).fill(null).map(() => []);

  function hash(key) {
    let hashValue = 0;
    for (let i = 0; i < key.length; i++) {
      hashValue = (hashValue * 31 + key.charCodeAt(i)) % size;
    }
    return hashValue;
  }

  return {
    set(key, value) {
      const index = hash(key);
      const bucket = buckets[index];

      // Check if key already exists
      for (let i = 0; i < bucket.length; i++) {
        if (bucket[i].key === key) {
          bucket[i].value = value;
          return;
        }
      }

      // Key doesn't exist, add new entry
      bucket.push({ key, value });
    },

    get(key) {
      const index = hash(key);
      const bucket = buckets[index];

      for (let i = 0; i < bucket.length; i++) {
        if (bucket[i].key === key) {
          return bucket[i].value;
        }
      }

      return undefined;
    },

    delete(key) {
      const index = hash(key);
      const bucket = buckets[index];

      for (let i = 0; i < bucket.length; i++) {
        if (bucket[i].key === key) {
          bucket.splice(i, 1);
          return true;
        }
      }

      return false;
    }
  };
}
```

**Advantages of chaining:**
- Simple to implement and understand.
- The table never fills up; you can always add more entries.
- Deletion is straightforward (just remove from the list).
- Performance degrades gracefully as load increases.

**Disadvantages of chaining:**
- Uses extra memory for pointers in linked lists.
- Poor cache locality since list nodes may be scattered in memory.
- Memory allocation overhead for each new entry.

### Open addressing

**Open addressing** stores all entries directly in the bucket array. When a collision occurs, you probe other buckets until you find an empty one. The sequence of buckets you check is called the **probe sequence**.

#### Linear probing

**Linear probing** is the simplest open addressing strategy. When a collision occurs, check the next bucket. If that is full, check the next one, and so on. When you reach the end, wrap around to the beginning.

```javascript
function createLinearProbingHashTable(size) {
  const buckets = new Array(size).fill(null);
  let count = 0;

  function hash(key) {
    let hashValue = 0;
    for (let i = 0; i < key.length; i++) {
      hashValue = (hashValue * 31 + key.charCodeAt(i)) % size;
    }
    return hashValue;
  }

  return {
    set(key, value) {
      if (count >= size * 0.7) {
        throw new Error("Table is too full, needs resizing");
      }

      let index = hash(key);

      while (buckets[index] !== null) {
        if (buckets[index].key === key) {
          buckets[index].value = value;
          return;
        }
        index = (index + 1) % size;
      }

      buckets[index] = { key, value };
      count++;
    },

    get(key) {
      let index = hash(key);
      let startIndex = index;

      while (buckets[index] !== null) {
        if (buckets[index].key === key) {
          return buckets[index].value;
        }
        index = (index + 1) % size;
        if (index === startIndex) break; // Full loop
      }

      return undefined;
    }
  };
}
```

**Advantages of linear probing:**
- Excellent cache locality. Consecutive probes access adjacent memory.
- No extra memory for pointers.
- Simple implementation.

**Disadvantages of linear probing:**
- **Primary clustering.** Runs of consecutive filled buckets grow over time. Once a cluster forms, it tends to get larger because new keys that hash anywhere in the cluster add to its end. Large clusters cause long probe sequences.

#### Quadratic probing

**Quadratic probing** addresses primary clustering by using a non-linear probe sequence. Instead of checking indices i, i+1, i+2, ..., you check i, i+1, i+4, i+9, ..., where the offset is the square of the probe number.

```javascript
function findSlotQuadratic(buckets, key, hashValue) {
  let probe = 0;
  let size = buckets.length;

  while (true) {
    let index = (hashValue + probe * probe) % size;

    if (buckets[index] === null || buckets[index].key === key) {
      return index;
    }

    probe++;
    if (probe >= size) {
      return -1; // Table is full or key not found
    }
  }
}
```

Quadratic probing reduces primary clustering but introduces **secondary clustering**: keys that hash to the same bucket follow the same probe sequence. It also does not guarantee visiting all buckets unless the table size is chosen carefully (a prime number or power of two with specific probe coefficients).

#### Double hashing

**Double hashing** uses a second hash function to determine the probe step size. If the first hash is h1(key) and the second is h2(key), the probe sequence is h1, h1+h2, h1+2*h2, h1+3*h2, and so on.

```javascript
function createDoubleHashingTable(size) {
  const buckets = new Array(size).fill(null);

  function hash1(key) {
    let h = 0;
    for (let i = 0; i < key.length; i++) {
      h = (h * 31 + key.charCodeAt(i)) % size;
    }
    return h;
  }

  function hash2(key) {
    let h = 0;
    for (let i = 0; i < key.length; i++) {
      h = (h * 37 + key.charCodeAt(i)) % (size - 1);
    }
    return h + 1; // Must be non-zero
  }

  return {
    set(key, value) {
      let index = hash1(key);
      let step = hash2(key);
      let probes = 0;

      while (buckets[index] !== null && buckets[index].key !== key) {
        index = (index + step) % size;
        probes++;
        if (probes >= size) {
          throw new Error("Table is full");
        }
      }

      buckets[index] = { key, value };
    },

    get(key) {
      let index = hash1(key);
      let step = hash2(key);
      let probes = 0;

      while (buckets[index] !== null) {
        if (buckets[index].key === key) {
          return buckets[index].value;
        }
        index = (index + step) % size;
        probes++;
        if (probes >= size) break;
      }

      return undefined;
    }
  };
}
```

Double hashing eliminates both primary and secondary clustering because different keys have different step sizes. However, it requires computing two hash functions and has worse cache locality than linear probing.

### Robin Hood hashing

**Robin Hood hashing** is a variant of linear probing that balances probe lengths. When inserting, if the new key has traveled further from its home bucket than the current occupant, you swap them and continue inserting the displaced key. This "steals from the rich" (entries with short probe distances) and "gives to the poor" (entries with long probe distances).

The result is that the variance in probe lengths decreases dramatically. Instead of some entries having very long probe sequences while others have very short ones, the lengths are more uniform.

### Choosing a collision strategy

For most applications, either chaining or linear probing works well:

- **Choose chaining** when you expect high load factors, need simple deletion, or are unsure about workload characteristics.
- **Choose linear probing** when you need cache efficiency and can keep the load factor low (below 0.7).
- **Choose double hashing** when you need open addressing but are concerned about clustering.

## Load factor and resizing

The **load factor** is the ratio of stored entries to total buckets:

```
load factor = number of entries / number of buckets
```

As the load factor increases, performance degrades. With chaining, higher load means longer chains to search. With open addressing, higher load means longer probe sequences.

### How load factor affects performance

For chaining with a good hash function, the average chain length equals the load factor. If you have 100 entries in 50 buckets, chains average 2 entries each. Lookup time is O(1 + load factor).

For open addressing, the relationship is more severe. The expected number of probes for a successful search in linear probing is approximately:

```
(1 + 1/(1 - load factor)) / 2
```

At 50% load, you expect about 1.5 probes. At 90% load, you expect about 5.5 probes. At 99% load, you expect about 50 probes. The table becomes unusable well before it fills completely.

### When to resize

Most hash table implementations resize when the load factor exceeds a threshold, typically 0.7 for open addressing or 1.0-2.0 for chaining. Some implementations also shrink when the load factor drops too low to reclaim memory.

### Rehashing

Resizing a hash table requires **rehashing**: allocating a new, larger array and re-inserting all entries. You cannot simply copy entries because the bucket an entry belongs to depends on the table size.

```javascript
function createResizableHashTable() {
  let buckets = new Array(8).fill(null).map(() => []);
  let count = 0;
  const maxLoadFactor = 0.75;

  function hash(key, size) {
    let hashValue = 0;
    for (let i = 0; i < key.length; i++) {
      hashValue = (hashValue * 31 + key.charCodeAt(i)) % size;
    }
    return hashValue;
  }

  function resize() {
    const oldBuckets = buckets;
    const newSize = buckets.length * 2;
    buckets = new Array(newSize).fill(null).map(() => []);
    count = 0;

    for (const bucket of oldBuckets) {
      for (const entry of bucket) {
        set(entry.key, entry.value);
      }
    }
  }

  function set(key, value) {
    if (count / buckets.length > maxLoadFactor) {
      resize();
    }

    const index = hash(key, buckets.length);
    const bucket = buckets[index];

    for (let i = 0; i < bucket.length; i++) {
      if (bucket[i].key === key) {
        bucket[i].value = value;
        return;
      }
    }

    bucket.push({ key, value });
    count++;
  }

  function get(key) {
    const index = hash(key, buckets.length);
    const bucket = buckets[index];

    for (let i = 0; i < bucket.length; i++) {
      if (bucket[i].key === key) {
        return bucket[i].value;
      }
    }

    return undefined;
  }

  return { set, get };
}
```

### Amortized analysis of resizing

Resizing takes O(n) time because you must re-insert every entry. However, if you double the size each time, the cost is amortized to O(1) per insertion.

Here is why: after resizing from n to 2n buckets, you must insert n more entries before resizing again. The O(n) cost of resizing is spread across those n insertions, adding O(1) to each.

This is the same amortization pattern as dynamic arrays. The individual expensive operation is rare enough that the average cost remains constant.

### Growth factor considerations

Most implementations double the table size on resize (growth factor of 2). This balances memory overhead against resize frequency:

- A smaller growth factor (like 1.5) uses less memory but resizes more often.
- A larger growth factor (like 4) resizes less often but may waste significant memory.

Doubling is a common sweet spot.

## Core operations

Now let us look at the fundamental operations in detail.

### Insert

To insert a key-value pair:

1. Compute the hash of the key.
2. Find the appropriate bucket (using modulo to convert hash to index).
3. If the key already exists, update its value.
4. If the key does not exist, add a new entry.
5. If the load factor exceeds the threshold, resize.

```javascript
function insert(key, value) {
  if ((count + 1) / buckets.length > maxLoadFactor) {
    resize();
  }

  const index = hash(key) % buckets.length;

  // With chaining
  for (let entry of buckets[index]) {
    if (entry.key === key) {
      entry.value = value; // Update existing
      return;
    }
  }
  buckets[index].push({ key, value }); // Add new
  count++;
}
```

Time complexity:
- **Average case:** O(1), assuming a good hash function and reasonable load factor.
- **Worst case:** O(n), if all keys hash to the same bucket, or O(n) during resize.

### Search (lookup)

To look up a value by key:

1. Compute the hash of the key.
2. Find the appropriate bucket.
3. Search within that bucket for the key.
4. Return the value if found, or indicate absence.

```javascript
function get(key) {
  const index = hash(key) % buckets.length;

  // With chaining
  for (let entry of buckets[index]) {
    if (entry.key === key) {
      return entry.value;
    }
  }

  return undefined;
}
```

Time complexity:
- **Average case:** O(1).
- **Worst case:** O(n), if all keys hash to the same bucket.

### Delete

Deletion in hash tables is straightforward with chaining but tricky with open addressing.

#### Deletion with chaining

Simply remove the entry from the bucket's list:

```javascript
function deleteKey(key) {
  const index = hash(key) % buckets.length;
  const bucket = buckets[index];

  for (let i = 0; i < bucket.length; i++) {
    if (bucket[i].key === key) {
      bucket.splice(i, 1);
      count--;
      return true;
    }
  }

  return false;
}
```

#### Deletion with open addressing

Open addressing deletion is more complex. If you simply empty a bucket, you break the probe sequence for any key that passed through that bucket during insertion.

Consider three keys A, B, C where:
- A hashes to index 5
- B hashes to index 5, probes to index 6
- C hashes to index 5, probes past 6 to index 7

If you delete B by emptying index 6, searching for C will stop at the empty index 6, never finding C at index 7.

The solution is **tombstones**: instead of emptying the bucket, mark it as deleted. Search treats tombstones as occupied (continue probing), but insert treats them as empty (can place a new entry).

```javascript
const TOMBSTONE = Symbol('tombstone');

function deleteKey(key) {
  let index = hash(key);

  while (buckets[index] !== null) {
    if (buckets[index] !== TOMBSTONE && buckets[index].key === key) {
      buckets[index] = TOMBSTONE;
      count--;
      return true;
    }
    index = (index + 1) % buckets.length;
  }

  return false;
}

function get(key) {
  let index = hash(key);

  while (buckets[index] !== null) {
    if (buckets[index] !== TOMBSTONE && buckets[index].key === key) {
      return buckets[index].value;
    }
    index = (index + 1) % buckets.length;
  }

  return undefined;
}
```

Tombstones accumulate over time and degrade performance. Rehashing clears them, so tables with many deletions may need to resize even if the entry count has not increased.

## Time complexity analysis

Understanding when hash tables are fast and when they are slow is essential for using them correctly.

### Average case: O(1)

Under reasonable assumptions (good hash function, bounded load factor), hash table operations are O(1) on average. This is the primary reason hash tables are so popular.

The analysis assumes that the hash function distributes keys uniformly. Each bucket receives approximately n/m keys, where n is the number of entries and m is the number of buckets. With a load factor of 1, each bucket has about one entry on average. With a load factor of 0.5, each bucket has about half an entry on average.

### Worst case: O(n)

The worst case occurs when all keys hash to the same bucket. This can happen due to:

- **A bad hash function** that does not distribute keys evenly.
- **Adversarial input** specifically crafted to cause collisions.
- **Unlucky input** that happens to collide by chance.

In the worst case, the hash table degrades to a linked list, and operations become O(n).

### Amortized O(1) for dynamic tables

When the table resizes, that single operation is O(n). However, resizing happens infrequently enough that the amortized cost per operation remains O(1).

### Comparing collision strategies

| Strategy | Average Search | Worst Search | Space |
|----------|----------------|--------------|-------|
| Chaining | O(1) | O(n) | Extra for pointers |
| Linear probing | O(1) | O(n) | Compact |
| Quadratic probing | O(1) | O(n) | Compact |
| Double hashing | O(1) | O(n) | Compact |

All strategies have the same big-O complexity. The differences are in constants and cache behavior:

- Linear probing is fastest when the load factor is low due to cache locality.
- Chaining handles high load factors more gracefully.
- Double hashing offers a middle ground but with higher overhead per probe.

## Hash maps vs. hash sets

Two common abstractions build on hash tables.

### Hash map

A **hash map** (also called a hash table, dictionary, or associative array) stores key-value pairs. Each key maps to exactly one value. Operations include:

- `set(key, value)`: Associate a value with a key.
- `get(key)`: Retrieve the value for a key.
- `has(key)`: Check if a key exists.
- `delete(key)`: Remove a key and its value.

JavaScript's `Map` and object literals are hash map implementations.

```javascript
// Using Map
const scores = new Map();
scores.set("Alice", 95);
scores.set("Bob", 87);
console.log(scores.get("Alice")); // 95
console.log(scores.has("Charlie")); // false

// Using object literal (limited to string keys)
const config = {};
config["debug"] = true;
config["timeout"] = 5000;
console.log(config["debug"]); // true
```

### Hash set

A **hash set** stores unique values (no duplicates) without associated values. It answers the question "is this value in the set?" Operations include:

- `add(value)`: Add a value to the set.
- `has(value)`: Check if a value exists.
- `delete(value)`: Remove a value.

JavaScript's `Set` is a hash set implementation.

```javascript
const seen = new Set();
seen.add("apple");
seen.add("banana");
seen.add("apple"); // No effect, already present

console.log(seen.has("apple")); // true
console.log(seen.has("cherry")); // false
console.log(seen.size); // 2
```

### When to use each

Use a **hash map** when you need to associate data with keys:
- Counting occurrences of items.
- Caching computed results.
- Storing configuration values.
- Representing sparse arrays or matrices.

Use a **hash set** when you only care about membership:
- Tracking which items you have seen.
- Removing duplicates from a collection.
- Fast membership testing in algorithms.
- Implementing mathematical sets (union, intersection).

## Common applications

Hash tables appear throughout software engineering. Here are some typical uses.

### Caching and memoization

Hash maps are natural for caching. The key is the input, and the value is the precomputed result.

```javascript
const cache = new Map();

function expensiveComputation(input) {
  if (cache.has(input)) {
    return cache.get(input);
  }

  // Simulate expensive work
  const result = input.split('').reverse().join('') + '_processed';
  cache.set(input, result);
  return result;
}
```

### Counting frequencies

Count how often each item appears:

```javascript
function countFrequencies(items) {
  const counts = new Map();

  for (const item of items) {
    counts.set(item, (counts.get(item) || 0) + 1);
  }

  return counts;
}

const words = ["apple", "banana", "apple", "cherry", "banana", "apple"];
console.log(countFrequencies(words));
// Map { 'apple' => 3, 'banana' => 2, 'cherry' => 1 }
```

### Detecting duplicates

Check for duplicates in O(n) time:

```javascript
function hasDuplicates(items) {
  const seen = new Set();

  for (const item of items) {
    if (seen.has(item)) {
      return true;
    }
    seen.add(item);
  }

  return false;
}
```

### Two-sum problem

A classic interview problem: find two numbers in an array that sum to a target.

```javascript
function twoSum(numbers, target) {
  const seen = new Map(); // value -> index

  for (let i = 0; i < numbers.length; i++) {
    const complement = target - numbers[i];

    if (seen.has(complement)) {
      return [seen.get(complement), i];
    }

    seen.set(numbers[i], i);
  }

  return null;
}

console.log(twoSum([2, 7, 11, 15], 9)); // [0, 1]
```

### Grouping items

Group items by some property:

```javascript
function groupBy(items, keyFn) {
  const groups = new Map();

  for (const item of items) {
    const key = keyFn(item);
    if (!groups.has(key)) {
      groups.set(key, []);
    }
    groups.get(key).push(item);
  }

  return groups;
}

const people = [
  { name: "Alice", department: "Engineering" },
  { name: "Bob", department: "Sales" },
  { name: "Charlie", department: "Engineering" }
];

const byDept = groupBy(people, p => p.department);
// Map { 'Engineering' => [...], 'Sales' => [...] }
```

### Implementing other data structures

Hash tables underlie many other structures:

- **LRU cache:** Hash map for fast lookup combined with a linked list for ordering.
- **Graph adjacency list:** Hash map from node to list of neighbors.
- **Disjoint set (union-find):** Hash map from element to parent.

### Database indexing

Database systems use hash indexes for equality queries. A hash index on a column lets the database find rows with a specific value in O(1) time instead of scanning the entire table.

## Choosing good hash functions

The hash function is critical to hash table performance. A poor hash function creates collisions; a good one avoids them.

### For strings

Polynomial hashing with a prime base works well for most string keys:

```javascript
function hashString(str, tableSize) {
  const prime = 31;
  let hash = 0;

  for (let i = 0; i < str.length; i++) {
    hash = (hash * prime + str.charCodeAt(i)) % tableSize;
  }

  return hash;
}
```

For better distribution, consider FNV-1a:

```javascript
function fnv1aHash(str) {
  let hash = 2166136261; // FNV offset basis

  for (let i = 0; i < str.length; i++) {
    hash ^= str.charCodeAt(i);
    hash = (hash * 16777619) >>> 0; // FNV prime, keep as 32-bit
  }

  return hash;
}
```

### For integers

Bit mixing functions scramble integer patterns:

```javascript
function hashInt(n) {
  n = ((n >> 16) ^ n) * 0x45d9f3b;
  n = ((n >> 16) ^ n) * 0x45d9f3b;
  n = (n >> 16) ^ n;
  return n >>> 0; // Ensure non-negative
}
```

### For composite keys

Combine hashes of individual components. A common approach:

```javascript
function combineHashes(hash1, hash2) {
  // Inspired by boost::hash_combine
  return hash1 ^ (hash2 + 0x9e3779b9 + (hash1 << 6) + (hash1 >> 2));
}

function hashPoint(point) {
  const xHash = hashInt(point.x);
  const yHash = hashInt(point.y);
  return combineHashes(xHash, yHash);
}
```

### Universal hashing

**Universal hashing** chooses hash function parameters randomly at table creation time. This prevents adversaries from crafting collision-inducing inputs because they do not know which hash function is in use.

```javascript
function createUniversalHashTable(size) {
  // Random parameters chosen once at creation
  const a = Math.floor(Math.random() * (size - 1)) + 1;
  const b = Math.floor(Math.random() * size);
  const prime = 2147483647; // Large prime

  function hash(key) {
    // Assume key is already a number
    return ((a * key + b) % prime) % size;
  }

  // ... rest of implementation
}
```

### Avoiding common mistakes

- **Do not use modulo with power-of-two table sizes and simple hash functions.** If your keys have patterns (like sequential integers), the low bits may have poor distribution. Either use prime table sizes or use a mixing function before modulo.
- **Do not hash only part of the key.** Hashing just the first few characters of a string ignores most of the input.
- **Do not ignore the hash function when debugging.** If you see clustering, suspect the hash function first.

## Edge cases and pitfalls

Hash tables have several edge cases that can cause bugs or performance problems.

### Mutable keys

If you modify a key after inserting it, the hash value changes but the entry stays in its original bucket. Lookups for the modified key will fail.

```javascript
const map = new Map();
const key = { id: 1 };
map.set(key, "value");

key.id = 2; // Mutating the key!

// This might still work because Map uses reference equality
console.log(map.get(key)); // "value"

// But with content-based hashing, this would break
```

Rule: treat hash table keys as immutable. If you need to change a key, delete the old entry and insert a new one.

### Null and undefined keys

Some implementations have special handling for null and undefined:

```javascript
const map = new Map();
map.set(null, "null value");
map.set(undefined, "undefined value");

console.log(map.get(null)); // "null value"
console.log(map.get(undefined)); // "undefined value"
```

JavaScript's `Map` handles these correctly. Object literals convert null to the string "null", which can cause subtle bugs.

### NaN as a key

NaN (Not a Number) does not equal itself (`NaN !== NaN`), which breaks naive equality checks. JavaScript's `Map` uses the SameValueZero algorithm, which treats NaN as equal to NaN:

```javascript
const map = new Map();
map.set(NaN, "not a number");
console.log(map.get(NaN)); // "not a number"
```

### Object key identity

JavaScript's `Map` uses reference equality for object keys. Two objects with identical content are different keys:

```javascript
const map = new Map();
map.set({ a: 1 }, "first");
map.set({ a: 1 }, "second");

console.log(map.size); // 2, not 1

const key = { a: 1 };
map.set(key, "third");
console.log(map.get(key)); // "third"
console.log(map.get({ a: 1 })); // undefined, different object
```

If you need content-based equality, convert objects to strings or use a custom hash map implementation.

### Empty string vs. missing key

Distinguish between a key that exists with an empty or falsy value and a key that does not exist:

```javascript
const map = new Map();
map.set("present", "");
map.set("zero", 0);

// Wrong way
if (map.get("present")) { /* never runs */ }
if (map.get("zero")) { /* never runs */ }

// Right way
if (map.has("present")) { /* runs */ }
if (map.has("zero")) { /* runs */ }
```

### Hash collisions in security contexts

If your hash table accepts user input as keys, an attacker might craft inputs that all collide. This turns O(1) operations into O(n), enabling denial-of-service attacks.

Mitigations:
- Use randomized hash functions (secret seed).
- Limit the number of entries per bucket.
- Use collision-resistant hash functions for security-critical applications.

### Iteration order

Hash table iteration order varies by implementation:

- JavaScript `Map` preserves insertion order.
- JavaScript objects mostly preserve insertion order for string keys (with caveats for integer-like keys).
- Some languages (like Python pre-3.7) had unpredictable iteration order.

Do not rely on iteration order unless your implementation explicitly guarantees it.

## Implementing a complete hash table

Here is a full implementation with chaining, resizing, and iteration support:

```javascript
class HashMap {
  constructor(initialCapacity = 16, loadFactor = 0.75) {
    this.buckets = new Array(initialCapacity).fill(null).map(() => []);
    this.size = 0;
    this.loadFactor = loadFactor;
  }

  _hash(key) {
    const str = String(key);
    let hash = 0;
    for (let i = 0; i < str.length; i++) {
      hash = (hash * 31 + str.charCodeAt(i)) >>> 0;
    }
    return hash % this.buckets.length;
  }

  _resize() {
    const oldBuckets = this.buckets;
    this.buckets = new Array(oldBuckets.length * 2).fill(null).map(() => []);
    this.size = 0;

    for (const bucket of oldBuckets) {
      for (const entry of bucket) {
        this.set(entry.key, entry.value);
      }
    }
  }

  set(key, value) {
    if (this.size / this.buckets.length > this.loadFactor) {
      this._resize();
    }

    const index = this._hash(key);
    const bucket = this.buckets[index];

    for (const entry of bucket) {
      if (entry.key === key) {
        entry.value = value;
        return this;
      }
    }

    bucket.push({ key, value });
    this.size++;
    return this;
  }

  get(key) {
    const index = this._hash(key);
    const bucket = this.buckets[index];

    for (const entry of bucket) {
      if (entry.key === key) {
        return entry.value;
      }
    }

    return undefined;
  }

  has(key) {
    const index = this._hash(key);
    const bucket = this.buckets[index];

    for (const entry of bucket) {
      if (entry.key === key) {
        return true;
      }
    }

    return false;
  }

  delete(key) {
    const index = this._hash(key);
    const bucket = this.buckets[index];

    for (let i = 0; i < bucket.length; i++) {
      if (bucket[i].key === key) {
        bucket.splice(i, 1);
        this.size--;
        return true;
      }
    }

    return false;
  }

  clear() {
    this.buckets = new Array(this.buckets.length).fill(null).map(() => []);
    this.size = 0;
  }

  keys() {
    const result = [];
    for (const bucket of this.buckets) {
      for (const entry of bucket) {
        result.push(entry.key);
      }
    }
    return result;
  }

  values() {
    const result = [];
    for (const bucket of this.buckets) {
      for (const entry of bucket) {
        result.push(entry.value);
      }
    }
    return result;
  }

  entries() {
    const result = [];
    for (const bucket of this.buckets) {
      for (const entry of bucket) {
        result.push([entry.key, entry.value]);
      }
    }
    return result;
  }

  forEach(callback) {
    for (const bucket of this.buckets) {
      for (const entry of bucket) {
        callback(entry.value, entry.key, this);
      }
    }
  }
}

// Usage
const map = new HashMap();
map.set("name", "Alice");
map.set("age", 30);
map.set("city", "Seattle");

console.log(map.get("name")); // "Alice"
console.log(map.has("age")); // true
console.log(map.size); // 3

map.delete("city");
console.log(map.has("city")); // false

for (const [key, value] of map.entries()) {
  console.log(`${key}: ${value}`);
}
```

## Performance comparison with other structures

How do hash tables compare to alternatives for common operations?

| Operation | Hash Table | Sorted Array | Balanced Tree | Unsorted Array |
|-----------|------------|--------------|---------------|----------------|
| Search | O(1) avg, O(n) worst | O(log n) | O(log n) | O(n) |
| Insert | O(1) avg, O(n) worst | O(n) | O(log n) | O(1) |
| Delete | O(1) avg, O(n) worst | O(n) | O(log n) | O(n) |
| Min/Max | O(n) | O(1) | O(log n) | O(n) |
| Range query | O(n) | O(log n + k) | O(log n + k) | O(n) |
| Ordered iteration | O(n log n) | O(n) | O(n) | O(n log n) |

Choose hash tables when:
- You need fast lookup, insert, and delete.
- You do not need ordering or range queries.
- Your keys have a good hash function.

Choose trees or sorted arrays when:
- You need ordered iteration.
- You need range queries (find all values between X and Y).
- You need guaranteed worst-case performance (trees only).
- Your workload involves finding minimum or maximum values.

## Summary

Hash tables provide average-case O(1) operations for insert, delete, and lookup by using a hash function to map keys to array indices. This makes them one of the most useful data structures in programming.

Key takeaways:

- A **hash function** converts keys to array indices. Good hash functions are deterministic, uniform, fast, and sensitive to input changes.
- **Collisions** occur when multiple keys hash to the same index. **Chaining** handles collisions with linked lists; **open addressing** handles them by probing other buckets.
- The **load factor** (entries divided by buckets) determines performance. Higher load factors mean more collisions and slower operations.
- **Resizing** and **rehashing** keep the load factor bounded, maintaining O(1) amortized operations.
- **Hash maps** store key-value pairs. **Hash sets** store unique values for fast membership testing.
- Worst-case O(n) performance occurs when all keys collide. Use randomized hash functions in security-sensitive contexts.
- **Deletion** with open addressing requires tombstones to preserve probe sequences.
- Hash tables trade ordered operations for speed. If you need ordering or range queries, consider trees instead.
- Watch for edge cases: mutable keys, null values, and object identity versus content equality.

Hash tables are so fundamental that most languages provide them as built-in types. Understanding how they work helps you use them effectively and recognize when an alternative structure might serve you better.
