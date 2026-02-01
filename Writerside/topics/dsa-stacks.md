# Stacks

Reading time: 25 minutes

A **stack** is a linear data structure that follows the Last In, First Out (LIFO) principle. The last element you add is the first one you remove. Think of a stack of plates in a cafeteria: you add plates to the top and take plates from the top. You cannot grab a plate from the middle without first removing all the plates above it.

Stacks appear everywhere in computing. Your browser's back button, the undo feature in text editors, and the way your programming language tracks function calls all rely on stacks. This page teaches you how stacks work, how to implement them, and when to use them in your own code.

## The LIFO principle

**LIFO (Last In, First Out)** defines the core behavior of a stack. When you add elements 1, 2, and 3 to a stack in that order, removing elements gives you 3, 2, and 1. The last element in is the first element out.

This ordering constraint is what makes a stack a stack. Unlike an array where you can access any element by index, a stack only lets you interact with the top element. You cannot peek at the middle or remove from the bottom without first removing everything above.

The LIFO constraint might seem limiting, but it is exactly what many problems need. When you undo an action in a text editor, you want to reverse the most recent action first. When a function calls another function, the inner function must finish before the outer function can continue. LIFO behavior matches these natural patterns.

Consider this sequence of operations:

1. Push `A` onto the stack. Stack is now: `[A]` (A is on top)
2. Push `B` onto the stack. Stack is now: `[A, B]` (B is on top)
3. Push `C` onto the stack. Stack is now: `[A, B, C]` (C is on top)
4. Pop from the stack. Returns `C`. Stack is now: `[A, B]`
5. Pop from the stack. Returns `B`. Stack is now: `[A]`
6. Push `D` onto the stack. Stack is now: `[A, D]`
7. Pop from the stack. Returns `D`. Stack is now: `[A]`

Notice how the element that comes out is always the most recently added one. This predictable ordering is the foundation for every stack application.

## Stack operations

A stack supports a small set of well-defined operations. Understanding these operations and their costs helps you use stacks effectively.

### Push

**Push** adds an element to the top of the stack. After a push, the new element becomes the top, and the previous top moves down one position.

```javascript
// Conceptually, pushing works like this:
// Before push(5): stack = [1, 2, 3] (3 is on top)
// After push(5):  stack = [1, 2, 3, 5] (5 is on top)
```

Push should be a constant-time operation, O(1). You add to one end of the structure without touching other elements. Both array-based and linked-list-based implementations achieve this.

### Pop

**Pop** removes and returns the element at the top of the stack. After a pop, the element below the removed one becomes the new top.

```javascript
// Conceptually, popping works like this:
// Before pop(): stack = [1, 2, 3, 5] (5 is on top)
// After pop():  stack = [1, 2, 3] (3 is on top)
// Returns: 5
```

Pop should also be a constant-time operation, O(1). You remove from one end without touching other elements.

Popping from an empty stack is an error condition. Your implementation must decide how to handle this: return a special value like `null`, throw an exception, or leave behavior undefined. The examples in this page throw an error to make bugs obvious.

### Peek (or Top)

**Peek** returns the top element without removing it. The stack remains unchanged after a peek.

```javascript
// Conceptually, peeking works like this:
// Stack: [1, 2, 3, 5] (5 is on top)
// peek() returns: 5
// Stack after peek(): [1, 2, 3, 5] (unchanged)
```

Peek is useful when you need to examine the top element before deciding whether to pop it. Like push and pop, peek should be a constant-time operation, O(1).

Peeking an empty stack is also an error condition. Handle it the same way you handle popping an empty stack.

### isEmpty

**isEmpty** returns `true` if the stack contains no elements, `false` otherwise.

```javascript
// Empty stack: isEmpty() returns true
// Stack with elements: isEmpty() returns false
```

This operation is essential for avoiding errors when popping or peeking. Always check `isEmpty()` before popping if you are not certain the stack has elements.

### Size (optional)

Some stack implementations provide a **size** operation that returns the number of elements currently in the stack. This is convenient but not essential; you can always track size separately if your implementation does not provide it.

## Array-based implementation

The most common stack implementation uses an array as the underlying storage. Elements live in a contiguous block of memory, and a variable tracks the position of the top.

### Basic structure

```javascript
class ArrayStack {
  constructor() {
    this.items = [];
    this.top = -1; // -1 indicates empty stack
  }

  push(element) {
    this.top++;
    this.items[this.top] = element;
  }

  pop() {
    if (this.isEmpty()) {
      throw new Error("Cannot pop from empty stack");
    }
    const element = this.items[this.top];
    this.items[this.top] = undefined; // Clear reference for garbage collection
    this.top--;
    return element;
  }

  peek() {
    if (this.isEmpty()) {
      throw new Error("Cannot peek empty stack");
    }
    return this.items[this.top];
  }

  isEmpty() {
    return this.top === -1;
  }

  size() {
    return this.top + 1;
  }
}
```

### Using JavaScript's built-in array methods

JavaScript arrays already have `push()` and `pop()` methods that operate on the end of the array. You can use these directly for a simpler implementation:

```javascript
class ArrayStack {
  constructor() {
    this.items = [];
  }

  push(element) {
    this.items.push(element);
  }

  pop() {
    if (this.isEmpty()) {
      throw new Error("Cannot pop from empty stack");
    }
    return this.items.pop();
  }

  peek() {
    if (this.isEmpty()) {
      throw new Error("Cannot peek empty stack");
    }
    return this.items[this.items.length - 1];
  }

  isEmpty() {
    return this.items.length === 0;
  }

  size() {
    return this.items.length;
  }
}
```

This version is cleaner and relies on JavaScript's optimized array implementation. The built-in `push()` and `pop()` methods handle resizing automatically.

### How it works

The array stores elements from index 0 upward. The `top` variable (or `items.length - 1` in the simplified version) tracks where the most recent element lives.

When you push:
1. Increment the top pointer (or let `push()` do this automatically)
2. Store the new element at that position

When you pop:
1. Read the element at the top position
2. Decrement the top pointer (or let `pop()` handle this)
3. Return the element

All operations access only one array position, so they run in constant time.

### Resizing considerations

JavaScript arrays resize automatically, but understanding what happens under the hood is valuable.

When you push to a full array, the runtime must:
1. Allocate a new, larger array (typically 1.5x or 2x the current size)
2. Copy all existing elements to the new array
3. Add the new element

This resizing takes O(n) time, but it happens infrequently. If you double the size each time, you copy n elements only after n pushes since the last resize. The **amortized cost** of push remains O(1): each element is copied at most O(log n) times total, and the average per-operation cost is constant.

In performance-critical code, you can avoid resizing by pre-allocating an array of known size. Most applications do not need this optimization.

### Advantages of array-based stacks

- **Memory efficiency.** Elements are stored contiguously with no pointer overhead.
- **Cache friendliness.** Sequential memory access patterns are fast on modern CPUs.
- **Simple implementation.** Arrays are familiar and well-supported in every language.
- **Random access if needed.** Though you should not rely on this (it breaks the stack abstraction), you can access any element for debugging.

### Disadvantages of array-based stacks

- **Wasted space.** Dynamic arrays often over-allocate to avoid frequent resizing.
- **Resize cost.** Occasional resizing takes O(n) time, which can cause latency spikes.
- **Fixed capacity option.** If you use a fixed-size array, you must handle stack overflow.

## Linked list implementation

An alternative implementation uses a linked list. Each element lives in a separate node that points to the node below it in the stack.

### Basic structure

```javascript
class Node {
  constructor(value) {
    this.value = value;
    this.next = null;
  }
}

class LinkedStack {
  constructor() {
    this.topNode = null;
    this.length = 0;
  }

  push(element) {
    const newNode = new Node(element);
    newNode.next = this.topNode;
    this.topNode = newNode;
    this.length++;
  }

  pop() {
    if (this.isEmpty()) {
      throw new Error("Cannot pop from empty stack");
    }
    const value = this.topNode.value;
    this.topNode = this.topNode.next;
    this.length--;
    return value;
  }

  peek() {
    if (this.isEmpty()) {
      throw new Error("Cannot peek empty stack");
    }
    return this.topNode.value;
  }

  isEmpty() {
    return this.topNode === null;
  }

  size() {
    return this.length;
  }
}
```

### How it works

The `topNode` pointer always references the most recently added element. Each node's `next` pointer references the node that was on top before it was pushed.

When you push:
1. Create a new node with the element
2. Set the new node's `next` to the current top
3. Update `topNode` to point to the new node

When you pop:
1. Save the value from the top node
2. Update `topNode` to point to `topNode.next`
3. Return the saved value (the old top node becomes garbage)

All operations update only a few pointers, so they run in constant time. Unlike array-based stacks, there is no resizing—each push allocates exactly one node.

### Advantages of linked list stacks

- **No resizing.** Each push allocates exactly the memory needed, no more.
- **Consistent performance.** Every operation takes the same time; no occasional slowdowns from resizing.
- **Unbounded size.** The stack grows until you run out of memory.

### Disadvantages of linked list stacks

- **Memory overhead.** Each element requires a separate node with a pointer, roughly doubling memory usage for small values.
- **Poor cache locality.** Nodes may be scattered in memory, causing cache misses.
- **Allocation cost.** Each push allocates memory; each pop frees memory. This can add up in high-frequency scenarios.

## Array vs linked list: when to use which

For most applications, an array-based stack is the better choice. It uses less memory, has better cache performance, and is simpler to implement. JavaScript's built-in arrays handle resizing efficiently, so you rarely need to worry about it.

Choose a linked list stack when:
- You need guaranteed constant-time operations with no occasional O(n) spikes
- Memory fragmentation is acceptable and cache performance is not critical
- You are implementing a stack in a language without dynamic arrays

In practice, most production code uses array-based stacks. The theoretical disadvantages of occasional resizing rarely matter in real applications.

## Time complexity summary

| Operation | Array-based | Linked list |
|-----------|-------------|-------------|
| push      | O(1) amortized | O(1) |
| pop       | O(1) | O(1) |
| peek      | O(1) | O(1) |
| isEmpty   | O(1) | O(1) |
| size      | O(1) | O(1) |

Both implementations achieve constant-time operations. The array-based version has amortized O(1) push due to occasional resizing, but this rarely matters in practice.

## The call stack

One of the most important uses of stacks in computing is the **call stack**, which your programming language uses to manage function calls.

### How the call stack works

When a function is called, the runtime pushes a **stack frame** onto the call stack. This frame contains:
- The return address (where to continue after the function finishes)
- Local variables for the function
- Parameters passed to the function
- Any saved registers or state

When the function returns, its frame is popped from the stack, and execution continues at the return address.

```javascript
function greet(name) {
  const greeting = createGreeting(name);
  console.log(greeting);
}

function createGreeting(name) {
  return "Hello, " + name;
}

greet("Alice");
```

The call stack during execution:

1. `greet("Alice")` is called. Push greet's frame.
   Call stack: `[greet]`

2. `greet` calls `createGreeting("Alice")`. Push createGreeting's frame.
   Call stack: `[greet, createGreeting]`

3. `createGreeting` returns. Pop its frame.
   Call stack: `[greet]`

4. `greet` calls `console.log()`. Push console.log's frame.
   Call stack: `[greet, console.log]`

5. `console.log` returns. Pop its frame.
   Call stack: `[greet]`

6. `greet` returns. Pop its frame.
   Call stack: `[]`

### Stack overflow

The call stack has a limited size. If you call functions too deeply, you exhaust this space and get a **stack overflow** error.

```javascript
function recurseForever() {
  return recurseForever(); // No base case!
}

recurseForever(); // RangeError: Maximum call stack size exceeded
```

Each recursive call adds a frame to the stack. Without a base case to stop recursion, the stack grows until it overflows.

### Why LIFO works for function calls

LIFO order is exactly what function calls need. When function A calls function B, B must complete before A can continue. When B calls C, C must complete before B can continue. The innermost function always finishes first—last in, first out.

This is why recursion works. Each recursive call waits for its nested calls to complete, and the stack naturally tracks this nesting.

### Tail call optimization

Some languages optimize **tail calls**, where a function's last action is calling another function. Instead of pushing a new frame, the runtime reuses the current frame. This prevents stack overflow for tail-recursive functions.

```javascript
// Tail recursive - the recursive call is the last thing that happens
function factorial(n, accumulator = 1) {
  if (n <= 1) return accumulator;
  return factorial(n - 1, n * accumulator); // Tail position
}

// Not tail recursive - multiplication happens after the recursive call
function factorialNonTail(n) {
  if (n <= 1) return 1;
  return n * factorialNonTail(n - 1); // Not tail position
}
```

JavaScript engines may or may not implement tail call optimization. Node.js does not reliably optimize tail calls, so you should not depend on this behavior. When stack depth is a concern, convert recursion to iteration.

## Common stack applications

Stacks solve many problems naturally. When a problem involves reversing, nesting, or tracking history, a stack is often the right tool.

### Undo and redo

Text editors, graphics programs, and many other applications use stacks to implement undo and redo.

```javascript
class UndoRedoManager {
  constructor() {
    this.undoStack = [];
    this.redoStack = [];
    this.currentState = "";
  }

  execute(newState) {
    this.undoStack.push(this.currentState);
    this.currentState = newState;
    this.redoStack = []; // Clear redo stack when new action is taken
  }

  undo() {
    if (this.undoStack.length === 0) {
      throw new Error("Nothing to undo");
    }
    this.redoStack.push(this.currentState);
    this.currentState = this.undoStack.pop();
  }

  redo() {
    if (this.redoStack.length === 0) {
      throw new Error("Nothing to redo");
    }
    this.undoStack.push(this.currentState);
    this.currentState = this.redoStack.pop();
  }

  getCurrentState() {
    return this.currentState;
  }
}

// Example usage
const manager = new UndoRedoManager();
manager.execute("Hello");
manager.execute("Hello, World");
manager.execute("Hello, World!");
console.log(manager.getCurrentState()); // "Hello, World!"

manager.undo();
console.log(manager.getCurrentState()); // "Hello, World"

manager.undo();
console.log(manager.getCurrentState()); // "Hello"

manager.redo();
console.log(manager.getCurrentState()); // "Hello, World"
```

The undo stack stores previous states. Undo pops the most recent state and pushes the current state onto the redo stack. Redo reverses this. When a new action occurs, the redo stack clears because you can no longer redo actions from a different timeline.

### Browser history

The browser's back and forward buttons work similarly. The back button uses a stack of previously visited pages. Going forward uses a separate stack of pages you navigated back from.

```javascript
class BrowserHistory {
  constructor(homepage) {
    this.backStack = [];
    this.forwardStack = [];
    this.currentPage = homepage;
  }

  visit(url) {
    this.backStack.push(this.currentPage);
    this.currentPage = url;
    this.forwardStack = []; // Clear forward history on new navigation
  }

  back() {
    if (this.backStack.length === 0) {
      return this.currentPage; // Cannot go back
    }
    this.forwardStack.push(this.currentPage);
    this.currentPage = this.backStack.pop();
    return this.currentPage;
  }

  forward() {
    if (this.forwardStack.length === 0) {
      return this.currentPage; // Cannot go forward
    }
    this.backStack.push(this.currentPage);
    this.currentPage = this.forwardStack.pop();
    return this.currentPage;
  }
}
```

### Parentheses matching

Stacks excel at validating nested structures. The classic example is checking whether parentheses, brackets, and braces are balanced.

```javascript
function isBalanced(str) {
  const stack = [];
  const pairs = {
    ')': '(',
    ']': '[',
    '}': '{'
  };
  const openers = new Set(['(', '[', '{']);

  for (const char of str) {
    if (openers.has(char)) {
      stack.push(char);
    } else if (pairs[char]) {
      if (stack.length === 0 || stack.pop() !== pairs[char]) {
        return false;
      }
    }
    // Ignore other characters
  }

  return stack.length === 0;
}

// Examples
console.log(isBalanced("()")); // true
console.log(isBalanced("()[]{}")); // true
console.log(isBalanced("([])")); // true
console.log(isBalanced("([)]")); // false - brackets interleaved incorrectly
console.log(isBalanced("(((")); // false - unclosed parens
console.log(isBalanced("())")); // false - extra closing paren
```

When you see an opening bracket, push it. When you see a closing bracket, pop and check that it matches. If the stack is empty when you try to pop, or if the popped character does not match, the string is unbalanced. At the end, the stack should be empty; leftover openers mean unclosed brackets.

This algorithm works because matching brackets have a LIFO structure: the most recently opened bracket must close first.

### Expression evaluation

Stacks are fundamental to evaluating mathematical expressions. Compilers and calculators use stacks to handle operator precedence and parentheses.

#### Evaluating postfix (Reverse Polish Notation)

In **postfix notation** (also called Reverse Polish Notation or RPN), operators come after their operands. The expression `3 + 4` becomes `3 4 +`. This notation eliminates the need for parentheses and makes evaluation straightforward with a stack.

```javascript
function evaluatePostfix(expression) {
  const stack = [];
  const tokens = expression.split(' ');

  for (const token of tokens) {
    if (isNumber(token)) {
      stack.push(parseFloat(token));
    } else if (isOperator(token)) {
      if (stack.length < 2) {
        throw new Error("Invalid expression: not enough operands");
      }
      const b = stack.pop();
      const a = stack.pop();
      const result = applyOperator(token, a, b);
      stack.push(result);
    }
  }

  if (stack.length !== 1) {
    throw new Error("Invalid expression: leftover values");
  }
  return stack.pop();
}

function isNumber(token) {
  return !isNaN(parseFloat(token));
}

function isOperator(token) {
  return ['+', '-', '*', '/', '^'].includes(token);
}

function applyOperator(operator, a, b) {
  switch (operator) {
    case '+': return a + b;
    case '-': return a - b;
    case '*': return a * b;
    case '/': return a / b;
    case '^': return Math.pow(a, b);
    default: throw new Error(`Unknown operator: ${operator}`);
  }
}

// Examples
console.log(evaluatePostfix("3 4 +")); // 7 (3 + 4)
console.log(evaluatePostfix("3 4 + 2 *")); // 14 ((3 + 4) * 2)
console.log(evaluatePostfix("5 1 2 + 4 * + 3 -")); // 14 (5 + ((1 + 2) * 4) - 3)
```

The algorithm processes each token:
- If it is a number, push it onto the stack
- If it is an operator, pop two operands, apply the operator, and push the result

At the end, the stack contains exactly one value: the result.

#### Converting infix to postfix (Shunting Yard algorithm)

The **Shunting Yard algorithm** converts standard infix notation (like `3 + 4 * 2`) to postfix notation. It uses two stacks: one for output and one for operators.

```javascript
function infixToPostfix(expression) {
  const output = [];
  const operatorStack = [];
  const tokens = tokenize(expression);

  const precedence = { '+': 1, '-': 1, '*': 2, '/': 2, '^': 3 };
  const rightAssociative = new Set(['^']);

  for (const token of tokens) {
    if (isNumber(token)) {
      output.push(token);
    } else if (isOperator(token)) {
      while (
        operatorStack.length > 0 &&
        operatorStack[operatorStack.length - 1] !== '(' &&
        (precedence[operatorStack[operatorStack.length - 1]] > precedence[token] ||
         (precedence[operatorStack[operatorStack.length - 1]] === precedence[token] &&
          !rightAssociative.has(token)))
      ) {
        output.push(operatorStack.pop());
      }
      operatorStack.push(token);
    } else if (token === '(') {
      operatorStack.push(token);
    } else if (token === ')') {
      while (operatorStack.length > 0 && operatorStack[operatorStack.length - 1] !== '(') {
        output.push(operatorStack.pop());
      }
      if (operatorStack.length === 0) {
        throw new Error("Mismatched parentheses");
      }
      operatorStack.pop(); // Remove the '('
    }
  }

  while (operatorStack.length > 0) {
    const op = operatorStack.pop();
    if (op === '(') {
      throw new Error("Mismatched parentheses");
    }
    output.push(op);
  }

  return output.join(' ');
}

function tokenize(expression) {
  // Simple tokenizer: split on spaces, keeping operators and parens separate
  return expression.match(/\d+\.?\d*|[+\-*/^()]/g) || [];
}

// Examples
console.log(infixToPostfix("3 + 4")); // "3 4 +"
console.log(infixToPostfix("3 + 4 * 2")); // "3 4 2 * +"
console.log(infixToPostfix("( 3 + 4 ) * 2")); // "3 4 + 2 *"
console.log(infixToPostfix("2 ^ 3 ^ 4")); // "2 3 4 ^ ^" (right associative)
```

This algorithm respects operator precedence and associativity. Higher-precedence operators bind tighter, and right-associative operators (like exponentiation) group from right to left.

### Depth-first search (DFS)

**Depth-first search** explores as far as possible along each branch before backtracking. While DFS is often written recursively, an explicit stack shows the underlying mechanism clearly.

```javascript
function dfsIterative(graph, startNode) {
  const visited = new Set();
  const stack = [startNode];
  const result = [];

  while (stack.length > 0) {
    const node = stack.pop();

    if (visited.has(node)) {
      continue;
    }

    visited.add(node);
    result.push(node);

    // Add neighbors to the stack (in reverse order to visit in original order)
    const neighbors = graph[node] || [];
    for (let i = neighbors.length - 1; i >= 0; i--) {
      if (!visited.has(neighbors[i])) {
        stack.push(neighbors[i]);
      }
    }
  }

  return result;
}

// Example graph represented as adjacency list
const graph = {
  'A': ['B', 'C'],
  'B': ['D', 'E'],
  'C': ['F'],
  'D': [],
  'E': ['F'],
  'F': []
};

console.log(dfsIterative(graph, 'A')); // ['A', 'B', 'D', 'E', 'F', 'C']
```

The stack stores nodes to visit. Popping a node processes it; pushing neighbors schedules them for future processing. Because stacks are LIFO, the algorithm goes deep before going wide.

Compare this to breadth-first search, which uses a queue (FIFO) and explores level by level.

### Backtracking problems

Many problems involve exploring possibilities and undoing choices when they lead to dead ends. A stack naturally tracks these choices.

```javascript
function findPathInMaze(maze, start, end) {
  const rows = maze.length;
  const cols = maze[0].length;
  const visited = new Set();
  const stack = [[start, [start]]]; // [current position, path so far]

  const directions = [
    [0, 1],  // right
    [1, 0],  // down
    [0, -1], // left
    [-1, 0]  // up
  ];

  while (stack.length > 0) {
    const [current, path] = stack.pop();
    const [row, col] = current;
    const key = `${row},${col}`;

    if (row === end[0] && col === end[1]) {
      return path; // Found the exit
    }

    if (visited.has(key)) {
      continue;
    }
    visited.add(key);

    for (const [dr, dc] of directions) {
      const newRow = row + dr;
      const newCol = col + dc;
      const newKey = `${newRow},${newCol}`;

      if (
        newRow >= 0 && newRow < rows &&
        newCol >= 0 && newCol < cols &&
        maze[newRow][newCol] === 0 && // 0 = open, 1 = wall
        !visited.has(newKey)
      ) {
        stack.push([[newRow, newCol], [...path, [newRow, newCol]]]);
      }
    }
  }

  return null; // No path found
}

// Example maze (0 = path, 1 = wall)
const maze = [
  [0, 0, 1, 0],
  [1, 0, 1, 0],
  [0, 0, 0, 0],
  [0, 1, 1, 0]
];

const path = findPathInMaze(maze, [0, 0], [3, 3]);
console.log(path); // [[0,0], [0,1], [1,1], [2,1], [2,2], [2,3], [3,3]]
```

When the algorithm reaches a dead end, it backtracks by popping from the stack. The previous state becomes the current state, and exploration continues from there.

### String reversal

A stack naturally reverses the order of elements. Push all elements, then pop them all.

```javascript
function reverseString(str) {
  const stack = [];

  // Push all characters
  for (const char of str) {
    stack.push(char);
  }

  // Pop all characters
  let reversed = '';
  while (stack.length > 0) {
    reversed += stack.pop();
  }

  return reversed;
}

console.log(reverseString("hello")); // "olleh"
```

While JavaScript has simpler ways to reverse strings (`str.split('').reverse().join('')`), this demonstrates the reversal property of stacks.

### Min stack

A **min stack** supports finding the minimum element in O(1) time. It uses an auxiliary stack to track minimums.

```javascript
class MinStack {
  constructor() {
    this.stack = [];
    this.minStack = [];
  }

  push(value) {
    this.stack.push(value);

    // Push to min stack if it is empty or value is <= current min
    if (this.minStack.length === 0 || value <= this.getMin()) {
      this.minStack.push(value);
    }
  }

  pop() {
    if (this.stack.length === 0) {
      throw new Error("Cannot pop from empty stack");
    }

    const value = this.stack.pop();

    // If we are popping the minimum, also pop from min stack
    if (value === this.getMin()) {
      this.minStack.pop();
    }

    return value;
  }

  peek() {
    if (this.stack.length === 0) {
      throw new Error("Cannot peek empty stack");
    }
    return this.stack[this.stack.length - 1];
  }

  getMin() {
    if (this.minStack.length === 0) {
      throw new Error("Stack is empty");
    }
    return this.minStack[this.minStack.length - 1];
  }

  isEmpty() {
    return this.stack.length === 0;
  }
}

// Example
const minStack = new MinStack();
minStack.push(5);
minStack.push(2);
minStack.push(8);
minStack.push(1);

console.log(minStack.getMin()); // 1
minStack.pop();
console.log(minStack.getMin()); // 2
minStack.pop();
console.log(minStack.getMin()); // 2
minStack.pop();
console.log(minStack.getMin()); // 5
```

The min stack tracks the minimum at each level of the main stack. When you pop a value equal to the current minimum, you also pop from the min stack, revealing the previous minimum.

## Edge cases and error handling

Robust stack implementations must handle edge cases correctly.

### Empty stack operations

Calling `pop()` or `peek()` on an empty stack is an error. Your implementation should handle this explicitly:

```javascript
// Option 1: Throw an error (recommended for catching bugs)
pop() {
  if (this.isEmpty()) {
    throw new Error("Cannot pop from empty stack");
  }
  return this.items.pop();
}

// Option 2: Return undefined (silent failure)
pop() {
  return this.items.pop(); // Returns undefined if empty
}

// Option 3: Return a sentinel value
pop() {
  if (this.isEmpty()) {
    return null; // Or some other sentinel
  }
  return this.items.pop();
}
```

Throwing an error is usually best because it makes bugs obvious. Silent failure can hide problems until they cause confusing behavior elsewhere.

### Stack overflow (fixed-size stacks)

If you implement a fixed-capacity stack, you must handle the case where a push exceeds capacity:

```javascript
class FixedStack {
  constructor(capacity) {
    this.items = new Array(capacity);
    this.capacity = capacity;
    this.top = -1;
  }

  push(element) {
    if (this.isFull()) {
      throw new Error("Stack overflow: cannot push to full stack");
    }
    this.top++;
    this.items[this.top] = element;
  }

  isFull() {
    return this.top === this.capacity - 1;
  }

  // ... other methods
}
```

### Handling null and undefined values

Your stack should correctly handle `null` and `undefined` as valid values:

```javascript
const stack = new ArrayStack();
stack.push(null);
stack.push(undefined);
stack.push(0);
stack.push(false);
stack.push('');

console.log(stack.pop()); // '' (empty string)
console.log(stack.pop()); // false
console.log(stack.pop()); // 0
console.log(stack.pop()); // undefined
console.log(stack.pop()); // null
```

Be careful when using sentinel values. If you return `undefined` to indicate an empty stack, you cannot distinguish between an empty stack and a stack containing `undefined`.

### Type safety

In JavaScript, stacks hold values of any type. If you need type safety, document the expected type and consider runtime checks:

```javascript
class NumberStack {
  constructor() {
    this.items = [];
  }

  push(value) {
    if (typeof value !== 'number' || Number.isNaN(value)) {
      throw new TypeError("NumberStack only accepts valid numbers");
    }
    this.items.push(value);
  }

  // ... other methods
}
```

## Performance tips

### Prefer array-based implementations

For most use cases, arrays outperform linked lists due to cache locality. JavaScript's built-in array methods are highly optimized.

### Avoid unnecessary operations

Do not call `isEmpty()` just to throw an error if you will access the stack anyway:

```javascript
// Less efficient: two checks
if (!stack.isEmpty()) {
  return stack.peek();
}

// More efficient in hot paths: let pop/peek handle the check
try {
  return stack.peek();
} catch (e) {
  // Handle empty stack
}
```

However, for clarity, `if (!stack.isEmpty())` is often preferred unless performance is critical.

### Pre-allocate when size is known

If you know the maximum stack size, pre-allocating can avoid resize costs:

```javascript
class PreallocatedStack {
  constructor(capacity) {
    this.items = new Array(capacity);
    this.top = -1;
  }
  // ...
}
```

### Use typed arrays for numeric data

For large stacks of numbers, typed arrays offer better performance:

```javascript
class Int32Stack {
  constructor(capacity) {
    this.items = new Int32Array(capacity);
    this.top = -1;
  }

  push(value) {
    this.items[++this.top] = value;
  }

  pop() {
    return this.items[this.top--];
  }
}
```

Typed arrays have fixed size and contiguous memory, making them faster for numeric operations.

## Common mistakes

### Using shift/unshift for stack operations

JavaScript's `shift()` and `unshift()` operate on the beginning of an array. They require shifting all elements and run in O(n) time. Always use `push()` and `pop()` which operate on the end:

```javascript
// Wrong: O(n) operations
stack.unshift(element); // Push to "bottom"
stack.shift();          // Pop from "bottom"

// Correct: O(1) operations
stack.push(element);    // Push to top
stack.pop();            // Pop from top
```

### Forgetting to check for empty stack

Always check before popping when the stack might be empty:

```javascript
// Dangerous if stack might be empty
const value = stack.pop(); // Could throw or return undefined

// Safe
if (!stack.isEmpty()) {
  const value = stack.pop();
}
```

### Mutating returned values

If your stack stores objects, popping returns a reference. Mutating the returned object changes the object itself:

```javascript
const stack = new ArrayStack();
const obj = { count: 0 };
stack.push(obj);

const popped = stack.pop();
popped.count = 99;

console.log(obj.count); // 99 - same object!
```

If you need isolation, clone objects when pushing or popping.

### Modifying stack during iteration

If you iterate over a stack while modifying it, you can get unexpected results:

```javascript
// Dangerous: modifying while iterating
while (!stack.isEmpty()) {
  const value = stack.pop();
  if (someCondition(value)) {
    stack.push(transform(value)); // Stack grows during iteration!
  }
}
```

Collect changes and apply them after iteration, or be very careful about the termination condition.

## Summary

A stack is a fundamental data structure that follows the Last In, First Out (LIFO) principle. The last element you add is the first element you remove.

Key takeaways:

- **Core operations** are push (add to top), pop (remove from top), peek (view top without removing), and isEmpty. All run in O(1) time.
- **Array-based implementations** are usually preferred for their simplicity, memory efficiency, and cache friendliness. JavaScript's built-in `push()` and `pop()` methods make this easy.
- **Linked list implementations** offer consistent O(1) operations without resize spikes but use more memory and have worse cache performance.
- **The call stack** is a critical application of stacks that manages function calls and returns in your programs. Understanding it helps you debug recursion and avoid stack overflow.
- **Common applications** include undo/redo functionality, browser history, parentheses matching, expression evaluation, depth-first search, and backtracking algorithms.
- **Edge cases** to handle include empty stack operations, stack overflow (for fixed-size stacks), and null/undefined values.

Stacks appear deceptively simple, but they unlock solutions to many problems. When you recognize the LIFO pattern in a problem—reversing order, tracking nested state, or managing history—reach for a stack.
