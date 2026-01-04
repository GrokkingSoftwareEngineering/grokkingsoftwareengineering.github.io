# Architectural Styles

Common architectural styles and when to use them.

## Prerequisites
- Programming basics
- Comfort with core data structures and algorithms

## What You'll Learn
- Layered, hexagonal, and event-driven approaches
- Tradeoffs between coupling, cohesion, and scalability
- How styles influence deployment and operations

## Why It Matters
- Architecture choices shape long-term maintainability
- Different styles optimize for different constraints
- Explicit styles make tradeoffs visible

## Core Concepts
### Layered and Modular Architectures
- Separation of concerns and boundaries
- Dependency direction and coupling control

### Hexagonal and Ports-and-Adapters
- Domain isolation and testability
- Adapters for infrastructure concerns

### Event-Driven Systems
- Asynchronous messaging and eventual consistency
- Event sourcing vs simple event streams

### Service-Oriented and Microservices
- Service boundaries and data ownership
- Operational complexity and governance

### Monoliths and Evolution
- Modular monolith benefits
- When to split into services

## Worked Examples (Node.js-Style Pseudocode)
- Placeholder for end-to-end examples in JavaScript-like pseudocode
- Focus on clarity of concepts over language-specific features

## Common Pitfalls
- Choosing microservices without operational maturity
- Mixing styles without clear boundaries
- Letting infrastructure leak into domain logic

## Best Practices
- Align architecture with team structure and goals
- Document decisions and revisit them periodically
- Design for change and clear boundaries

## Try It Yourself
1. Sketch two architectural styles for the same product
2. Identify coupling hot spots in an existing system
3. Write a migration plan from monolith to services

## Next Steps
- Add case studies and real-world tradeoffs
- Link concepts to adjacent topics in the roadmap

## Related Articles
- [Software-Architecture.md](Software-Architecture.md)
- [Architecture-Decision-Making.md](Architecture-Decision-Making.md)
- [Enterprise-Integration-Patterns.md](Enterprise-Integration-Patterns.md)
