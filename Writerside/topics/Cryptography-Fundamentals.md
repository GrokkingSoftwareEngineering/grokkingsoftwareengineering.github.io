# Cryptography Fundamentals

Core cryptographic concepts for secure systems.

## Prerequisites
- Programming basics
- Comfort with core data structures and algorithms

## What You'll Learn
- Hashing vs encryption vs encoding
- Public key cryptography and key exchange
- Common cryptographic primitives and risks

## Why It Matters
- Security depends on correct cryptographic use
- Misuse leads to severe vulnerabilities
- Understanding primitives guides system design

## Core Concepts
### Hashing and Integrity
- Hash functions and collision resistance
- Password hashing and salts

### Symmetric Encryption
- Block vs stream ciphers
- Modes of operation and pitfalls

### Asymmetric Cryptography
- Public/private keys and signatures
- Key exchange basics

### Randomness and Key Management
- CSPRNG basics
- Key storage and rotation

### Cryptography in Practice
- TLS overview and certificates
- Common implementation mistakes

## Worked Examples (Node.js-Style Pseudocode)
- Placeholder for end-to-end examples in JavaScript-like pseudocode
- Focus on clarity of concepts over language-specific features

## Common Pitfalls
- Rolling your own crypto primitives
- Using weak randomness sources
- Storing secrets alongside application code

## Best Practices
- Use vetted libraries and protocols
- Separate keys from code and configs
- Threat model before choosing primitives

## Try It Yourself
1. Compare hashing algorithms for password storage
2. Sketch a secure key rotation process
3. Analyze a TLS handshake flow

## Next Steps
- Add case studies and real-world tradeoffs
- Link concepts to adjacent topics in the roadmap

## Related Articles
- [Security-Fundamentals.md](Security-Fundamentals.md)
- [Web-Security.md](Web-Security.md)
- [Network-Protocols.md](Network-Protocols.md)
