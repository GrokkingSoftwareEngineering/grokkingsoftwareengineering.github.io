# Database Systems

Core database concepts that drive data modeling, performance, and reliability.

## Prerequisites
- Programming basics
- Comfort with core data structures and algorithms

## What You'll Learn
- Relational vs NoSQL tradeoffs
- Normalization, indexing, and transactions
- Replication, sharding, and scaling strategies

## Why It Matters
- Database choices define system performance and correctness
- Data modeling mistakes are expensive to fix later
- Consistency and availability tradeoffs affect product behavior

## Core Concepts
### Data Models
- Relational, document, key-value, and graph models
- SQL vs NoSQL tradeoffs

### Modeling and Normalization
- ER modeling and schema design
- Normalization vs denormalization

### Transactions and Consistency
- ACID, isolation levels, and anomalies
- BASE and eventual consistency

### Indexing and Query Planning
- B-tree indexes and query plans
- Secondary indexes and tradeoffs

### Scaling and Distribution
- Replication, sharding, and clustering
- Federation and multi-database systems

### Database Features
- Views, stored procedures, and triggers
- DDL, DML, DQL, and DCL roles

## Worked Examples (Node.js-Style Pseudocode)
- Placeholder for end-to-end examples in JavaScript-like pseudocode
- Focus on clarity of concepts over language-specific features

## Common Pitfalls
- Choosing NoSQL for scale without data access analysis
- Over-normalizing and hurting query performance
- Ignoring transaction isolation anomalies

## Best Practices
- Model queries before finalizing schemas
- Use indexes based on access patterns
- Plan for migrations and schema evolution

## Try It Yourself
1. Design a schema and list its query patterns
2. Compare query plans with and without indexes
3. Map a dataset to relational and document models

## Next Steps
- Add case studies and real-world tradeoffs
- Link concepts to adjacent topics in the roadmap

## Related Articles
- [Database-Design.md](Database-Design.md)
- [System-Design-Fundamentals.md](System-Design-Fundamentals.md)
- [Distributed-Systems.md](Distributed-Systems.md)
