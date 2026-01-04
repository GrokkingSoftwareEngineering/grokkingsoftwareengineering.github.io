# Engineering Foundations

[![Build Documentation](https://github.com/engineeringfoundations/engineeringfoundations.github.io/actions/workflows/build-docs.yml/badge.svg)](https://github.com/engineeringfoundations/engineeringfoundations.github.io/actions/workflows/build-docs.yml)

**Combating brain rot through comprehensive software engineering education.**

Visit the live site: [https://engineeringfoundations.github.io](https://engineeringfoundations.github.io)

## Mission

Engineering Foundations provides deep, long-form technical content to combat the brain rot epidemic caused by short-form content and superficial tutorials. We believe in building genuine expertise through comprehensive education.

## Content Structure

Our **comprehensive topics** follow the Computer Science roadmap progression:

1. **Programming Basics** - Baseline programming literacy
2. **Data Structures** - Core containers and structures
3. **Asymptotic Notation** - Complexity analysis fundamentals
4. **Common Algorithms** - Sorting, searching, graph algorithms, classic problems
5. **String Search & Data Representation** - Text algorithms and low-level encoding details
6. **Design Patterns & Architecture** - Patterns, architectural styles, UML
7. **Basic Math Skills** - Discrete math and mathematical foundations
8. **Complexity Classes** - P, NP, NP-hard, NP-complete
9. **Networking** - Models, protocols, and network programming
10. **Databases** - Data modeling, indexing, transactions, scaling
11. **System Design** - Scalability, caching, distributed systems, API design
12. **Security** - Security fundamentals and cryptography
13. **How Computers Work** - CPU execution, memory, OS, concurrency
14. **Real-Time Communication** - Polling, SSE, WebSockets

## Technology Stack

- **Documentation Platform**: [JetBrains Writerside](https://www.jetbrains.com/writerside/)
- **Hosting**: GitHub Pages
- **CI/CD**: GitHub Actions

## Development Setup

### Prerequisites

- [JetBrains Writerside](https://www.jetbrains.com/writerside/) IDE (recommended)
- Or any text editor for Markdown editing
- Git for version control

### Local Development

1. **Clone the repository**
   ```bash
   git clone https://github.com/engineeringfoundations/engineeringfoundations.github.io.git
   cd engineeringfoundations.github.io
   ```

2. **Open in Writerside**
   - Launch JetBrains Writerside
   - Open the project folder
   - The Writerside configuration will be automatically detected

3. **Preview locally**
   - Use Writerside's built-in preview functionality
   - Or build locally using the Writerside CLI

### Project Structure

```
engineering-foundations/
├── .github/
│   └── workflows/
│       └── build-docs.yml          # GitHub Actions workflow
├── Writerside/
│   ├── cfg/
│   │   └── buildprofiles.xml       # Build configuration
│   ├── images/                     # Static assets and styling
│   │   ├── ef-logo.svg            # Main logo
│   │   ├── favicon.svg            # Favicon
│   │   ├── ef-banner.svg          # Social media banner
│   │   └── ef-styles.css          # Custom styling
│   ├── topics/                    # All article content
│   │   ├── Home.md                # Main landing page
│   │   ├── Article-Template.md    # Template for new articles
│   │   └── [topic-files].md       # 60+ topic articles
│   ├── c.list                     # Categories configuration
│   ├── ef.tree                    # Main navigation structure
│   ├── v.list                     # Variables and branding
│   └── writerside.cfg             # Main Writerside config
└── README.md                      # This file
```

## Contributing

We welcome contributions from engineers who share our mission of comprehensive technical education.

### Content Guidelines

- **Depth over brevity**: Articles should be 3,000-10,000+ words
- **Comprehensive coverage**: Don't just show what, explain why and when
- **Practical examples**: Include real-world applications and code samples
- **Progressive structure**: Build concepts systematically
- **Quality focus**: Accuracy, clarity, and educational value are paramount
- **Language-agnostic**: Concepts apply to any language; use Node.js-style JavaScript for examples

### Writing Process

1. **Review existing content** to understand our style and avoid duplication
2. **Use the Article Template** (`Writerside/topics/Article-Template.md`)
3. **Follow our writing guidelines** for voice, tone, and structure
4. **Include practical examples** and code samples where appropriate
5. **Test all code examples** to ensure they work correctly

### Submission Process

1. **Fork the repository** and create a feature branch
2. **Write your content** following our guidelines
3. **Preview locally** using Writerside
4. **Submit a pull request** with a clear description
5. **Collaborate on review** to ensure quality and consistency

### Types of Contributions

- **New articles** on topics from our roadmap
- **Content enhancement** of existing articles
- **Code examples** and practical demonstrations
- **Technical review** for accuracy and clarity
- **Accessibility improvements** and typo fixes

## Design Philosophy

### Visual Identity

- **Primary Color**: `#1a365d` (Deep blue - trust, depth, stability)
- **Secondary Color**: `#2b6cb0` (Bright blue - innovation, clarity)
- **Accent Color**: `#38a169` (Green - growth, progress)
- **Typography**: Inter for text, JetBrains Mono for code
- **Logo**: Geometric foundation blocks representing solid engineering principles

### User Experience

- **Reading-optimized layout**: 75-character line length for optimal readability
- **Progressive navigation**: Clear learning paths from beginner to expert
- **Deep-focus design**: Minimal distractions to encourage thorough reading
- **Accessibility**: High contrast, clear hierarchy, semantic markup

## Build and Deployment

The site automatically builds and deploys via GitHub Actions when changes are pushed to the main branch:

1. **Build**: Writerside Docker container processes all content
2. **Test**: Automated testing for broken links and structure
3. **Deploy**: Static site deployment to GitHub Pages

### Manual Deployment

For manual builds or testing:

```bash
# Using Writerside CLI (if installed)
writerside build --instance ef --output dist/

# Or using Docker
docker run --rm -v $(pwd):/docs jetbrains/writerside:243.22562 build --instance ef
```

## Analytics and Performance

- **Reading time estimation** for all articles
- **Search functionality** across all content
- **GitHub integration** for easy contribution
- **Responsive design** for all device types
- **SEO optimization** for discoverability

## Community and Support

- **Issues**: Report bugs or suggest improvements via GitHub Issues
- **Discussions**: Join technical discussions in GitHub Discussions
- **Contributions**: See our contribution guidelines above
- **Contact**: Questions about the project or collaboration opportunities

## License

This project is open source and available under the [MIT License](LICENSE).

---

## Recognition

Engineering Foundations is built by engineers, for engineers. Special thanks to all contributors who help build this comprehensive resource for the software engineering community.

**Together, we're combating brain rot through quality technical education.**
