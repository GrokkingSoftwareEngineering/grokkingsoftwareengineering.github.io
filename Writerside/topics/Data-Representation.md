# Data Representation

How computers encode numbers, text, and instructions, and why those details matter for correctness and performance.

## Prerequisites
- Programming basics
- Comfort with core data structures and algorithms

## What You'll Learn
- Binary, hex, and bitwise operations for systems-level reasoning
- Endianness and floating point tradeoffs
- ASCII, Unicode, and character encoding pitfalls

## Why It Matters
- Explains bugs caused by overflow, precision loss, and encoding mismatches
- Grounds performance work in how data is laid out in memory
- Builds intuition for low-level debugging and interoperability

## Core Concepts
### Binary, Hex, and Integer Ranges
- Binary and hexadecimal representations
- Signed vs unsigned integers
- Overflow and underflow behavior

### Bitwise Operations
- Masks, shifts, and bit flags
- Bitwise tricks for performance and correctness

### Endianness
- Big endian vs little endian
- Where endianness appears in file formats and protocols

### Floating Point
- IEEE 754 layout and precision limits
- Rounding error and numerical stability

### Text Encodings
- ASCII vs Unicode
- UTF-8 and common encoding pitfalls

## Worked Examples (Node.js-Style Pseudocode)
- Placeholder for end-to-end examples in JavaScript-like pseudocode
- Focus on clarity of concepts over language-specific features

## Common Pitfalls
- Assuming floats are exact for currency or precise comparisons
- Mixing encodings across systems without explicit conversion
- Using signed types where unsigned is required (and vice versa)

## Best Practices
- Make encoding explicit at system boundaries
- Use integer types with clearly defined ranges
- Prefer decimal-safe approaches for money and totals

## Try It Yourself
1. Inspect a binary file format and map its fields by byte offset
2. Demonstrate a floating point precision issue and mitigation
3. Convert a string through multiple encodings and compare results

## Next Steps
- Add case studies and real-world tradeoffs
- Link concepts to adjacent topics in the roadmap

## Related Articles
- [How-Computers-Work.md](How-Computers-Work.md)
- [Computer-Architecture.md](Computer-Architecture.md)
- [Programming-Basics.md](Programming-Basics.md)
