# Senior SWE → Tech Lead Learning Roadmap
### A 5-day-a-week, 1-hour-a-day plan for career acceleration at SAS Institute

---

## Your Book Stack: An Assessment

### Tier 1 — Read These First (Highest ROI)

**Designing Data-Intensive Applications (Kleppmann)** — The single most important book on your shelf. As a tech lead working with data analytics at SAS, the concepts here (replication, partitioning, stream processing, batch processing, consistency models) are foundational to every system design conversation you'll lead. Still incredibly relevant.

**The Staff Engineer's Path (Reilly)** — Read within your first month at SAS. It's about operating with influence, navigating ambiguity, and leading without direct authority. Your operating manual for the IC leadership track.

**Refactoring (Fowler)** — Essential for reviewing code, setting team standards, and mentoring. The refactoring patterns are the language you'll use when coaching engineers. Pairs perfectly with AI-assisted development — knowing *what* to refactor matters more than ever when agents handle the *how*.

### Tier 2 — Deep Value, Read Strategically

**Effective Java (Bloch)** — Use as reference rather than cover-to-cover. Focus on concurrency, generics, and API design items — these are the areas where you'll set patterns for your team.

**Design Patterns (Gang of Four)** — Use as reference material. The real value is recognising when a pattern applies and articulating it during design reviews.

**Head First Design Patterns** — Deprioritise given your experience level. Good refresher if needed.

### Tier 3 — Lower Priority

**Think Like a Programmer (Spraul)** — Aimed at a more junior audience. Give it to a mentee on your new team.

### Missing From Your Stack

- **"Building Microservices" by Sam Newman (2nd ed)** — Bridges code-level patterns and system-level architecture
- **"Staff Engineer" by Will Larson** — Specifically about the IC track with interviews from Staff+ engineers
- **"Team Topologies" by Skelton & Pais** — Changes how you think about team structure and cognitive load
- **"Fundamentals of Software Architecture" by Richards & Ford** — Bridge between coding expertise and architectural thinking

---

## The 20-Week Structured Plan

### Phase 1: Foundations for the New Role (Weeks 1-5)

**Week 1-2: Leadership & Influence**
- Read: The Staff Engineer's Path (~50 pages/day)
- Practice: Journal one concrete application for SAS after each session
- Friday: Write your "tech lead operating principles"

**Week 3-4: System Design Foundations**
- Read: DDIA Part 1 (Chapters 1-4)
- Practice: Sketch architectures of systems you've worked on, annotate with DDIA concepts
- Friday: Work through one system design problem for 1 hour

**Week 5: Refactoring & Code Quality**
- Read: Refactoring — code smells sections and catalogue
- Practice: Apply 2-3 refactoring patterns to a codebase
- Friday: Draft a code review checklist for your SAS team

### Phase 2: Deep Technical Skills (Weeks 6-12)

**Week 6-8: Advanced Java & Spring Boot**
- Read: Effective Java — Concurrency, Generics, Lambdas/Streams, API Design
- Practice (30 min reading, 30 min hands-on):
  - Build a Spring Boot project with clean DDD domain model
  - Go deeper with Spring Modulith
  - Implement event-driven patterns with Spring Application Events + Kafka

**Week 9-10: Distributed Systems**
- Read: DDIA Part 2 (Chapters 5-9)
- Practice: 2 medium-complexity system design problems per week
- Friday: Study one real-world architecture paper

**Week 11-12: Data Processing**
- Read: DDIA Part 3 (Chapters 10-12)
- Practice: Design a data pipeline architecture relevant to SAS's analytics domain
- White papers: Google MapReduce, Kafka "The Log" (Jay Kreps), Raft consensus

### Phase 3: AI & The New Abstraction Layer (Weeks 13-17)

**Week 13: Understanding the Landscape**
- Read Anthropic's "2026 Agentic Coding Trends Report"
- Watch Andrej Karpathy's "Intro to LLMs"
- Follow Simon Willison's blog (simonwillison.net)

**Week 14-15: Working with AI Coding Agents**
- Full hour hands-on practice each day
- Use an AI agent for a complete feature: spec → implementation → test → refactor
- Practice reviewing AI-generated code critically
- Learn MCP (Model Context Protocol) — the standard for connecting agents to tools
- Key skills: effective specifications, knowing when to delegate vs. do it yourself, git coordination with agents

**Week 16: AI Architecture & Integration Patterns**
- RAG patterns, function calling, structured outputs
- Agent architectures: single-agent vs. multi-agent, orchestrator patterns
- Security and governance for AI in enterprise (relevant for SAS)

**Week 17: AI Strategy for Your Team**
- Draft a proposal for AI tool adoption at SAS
- Define guardrails and review processes
- Write an "AI adoption playbook" for your team

### Phase 4: Leadership Mastery & Continuous Growth (Weeks 18-20+)

**Week 18-19: Engineering Leadership**
- Read: Staff Engineer (Larson) or Team Topologies
- Practice: Draft ADRs, design a technical onboarding plan, create a team tech radar

**Week 20: Consolidation**
- Review everything learned
- Identify top 3 areas for continued depth
- Set up ongoing learning system

---

## Ongoing Weekly Rhythm

| Day | Focus | Activity |
|-----|-------|----------|
| Monday | Reading | Book chapter, white paper, or long-form article |
| Tuesday | Hands-on | Coding, system design problem, or AI experimentation |
| Wednesday | Industry | Newsletters, podcasts, new developments |
| Thursday | Deep work | Side project, open source, or writing |
| Friday | Reflection | Journal learnings, prepare something to share with team |

---

## Key Resources

### Newsletters
- The Pragmatic Engineer (Gergely Orosz)
- ByteByteGo (Alex Xu)
- Simon Willison's Weblog
- Tech World With Milan

### Research & Papers
- arXiv cs.SE section
- Papers We Love (GitHub)
- ACM Queue
- CMU Software Engineering Institute

### Java & Spring
- Baeldung
- Inside Java (Oracle)
- Spring Blog (official)

---

## Priority Stack (If Overwhelmed)

1. The Staff Engineer's Path — first 2 weeks
2. DDIA — over 6 weeks
3. Hands-on AI agent practice — 2 weeks
4. System design problems — 2 per week, ongoing
5. The Pragmatic Engineer + Simon Willison's blog — subscribe immediately
