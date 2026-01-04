# Network Protocols and Models

The layered models and protocols that make modern networking work.

## Prerequisites
- Programming basics
- Comfort with core data structures and algorithms

## What You'll Learn
- OSI vs TCP/IP models and how they map in practice
- DNS, HTTP, and TLS in the application stack
- How protocol choices affect system design

## Why It Matters
- Protocol knowledge helps debug connectivity issues
- Design decisions depend on transport guarantees
- Security depends on correct protocol use

## Core Concepts
### Network Models
- OSI layers and responsibilities
- TCP/IP stack mapping

### Core Protocols
- IP, TCP, and UDP tradeoffs
- Routing and addressing basics

### DNS and Naming
- Resolution flow and caching
- Failure modes and mitigation

### HTTP and HTTPS
- Request/response flow
- TLS handshake and certificates

### Protocol Selection
- Latency vs reliability tradeoffs
- When to use streaming vs request-response

## Worked Examples (Node.js-Style Pseudocode)
- Placeholder for end-to-end examples in JavaScript-like pseudocode
- Focus on clarity of concepts over language-specific features

## Common Pitfalls
- Assuming TCP guarantees low latency
- Ignoring DNS caching behavior
- Misconfiguring TLS and certificates

## Best Practices
- Use packet traces to validate assumptions
- Treat DNS and TLS as first-class dependencies
- Design timeouts and retries deliberately

## Try It Yourself
1. Map a real app request across OSI layers
2. Inspect a TLS handshake in a packet trace
3. Compare TCP vs UDP for a sample workload

## Next Steps
- Add case studies and real-world tradeoffs
- Link concepts to adjacent topics in the roadmap

## Related Articles
- [Networking-Fundamentals.md](Networking-Fundamentals.md)
- [Web-Security.md](Web-Security.md)
- [System-Design-Fundamentals.md](System-Design-Fundamentals.md)
