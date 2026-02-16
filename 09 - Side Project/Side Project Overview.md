# Side Project: GigLedger

A gig economy financial management platform for UK self-employed individuals. Track gigs, income, expenses, clients, and generate HMRC-ready tax reports. Built as a single evolving application that grows with each phase of the learning roadmap.

## Concept

Self-employed people in the UK need to track income and expenses throughout the year, categorise them correctly for HMRC self-assessment, and generate reports at tax time. Most either use spreadsheets or pay for overengineered accounting software. GigLedger sits in the middle — purpose-built for gig workers, simple to use, and smart enough to help with tax categories and reporting.

---

## Domain Model (Initial)

### Core Entities
- **User** — The self-employed individual
- **Client** — Who they work for (recurring or one-off)
- **Gig** — A unit of work (date, description, client, status, payment terms)
- **Income** — Money received (linked to gig, amount, date received, payment method)
- **Expense** — Money spent (amount, date, category, receipt reference, allowable for tax)
- **TaxCategory** — HMRC self-assessment categories (travel, equipment, office costs, professional fees, etc.)
- **TaxYear** — Reporting period (6 Apr - 5 Apr, UK tax year)
- **Report** — Generated tax summary for a given tax year

### Key Business Rules
- UK tax year runs 6 April to 5 April
- Expenses must be categorised as allowable or disallowable for tax relief
- VAT threshold tracking (currently £90,000) — alert when approaching
- Payment terms tracking — flag overdue invoices
- Mileage allowance: 45p/mile first 10,000, 25p/mile after
- Flat rate expenses for working from home

---

## Phase 1 Build (Weeks 1-5): Foundation

**Goal:** A clean Spring Boot API with a well-modelled domain, React frontend for basic CRUD, and a solid CI/CD pipeline.

### Architecture
```
┌─────────────────┐     ┌──────────────────┐     ┌────────────┐
│  React Frontend │────▶│  Spring Boot API  │────▶│ PostgreSQL │
│  (Vite + TS)    │     │  (Java 21+)       │     │            │
└─────────────────┘     └──────────────────┘     └────────────┘
```

### Backend Tasks
- [ ] Initialise Spring Boot 3.x project (Spring Initializr)
- [ ] Model the domain using DDD patterns
  - User, Client, Gig as Aggregates
  - Money as a Value Object (never use float/double for currency)
  - Income and Expense as entities within their aggregates
  - TaxCategory as an enum or reference data
- [ ] Implement repository layer with Spring Data JPA
- [ ] Build REST API
  - `POST/GET/PUT /api/gigs` — CRUD for gigs
  - `POST/GET /api/income` — Record and list income
  - `POST/GET /api/expenses` — Record and list expenses with category
  - `GET /api/dashboard` — Summary endpoint (total income, total expenses, estimated tax)
- [ ] Input validation with Bean Validation
- [ ] Error handling with proper HTTP status codes and problem details
- [ ] Integration tests with JUnit 5 + Testcontainers (PostgreSQL)
- [ ] Flyway for database migrations
- [ ] Apply Effective Java API design principles throughout
- [ ] Apply 2-3 refactoring patterns from Fowler once initial code is working

### Frontend Tasks
- [ ] Initialise React project with Vite + TypeScript
- [ ] Set up Tailwind CSS
- [ ] Dashboard page — income/expense summary, recent gigs
- [ ] Gig management — list, create, edit
- [ ] Income tracker — log payments received
- [ ] Expense tracker — log expenses with category selection
- [ ] Basic responsive design

### CI/CD
- [ ] GitHub Actions pipeline from day one
  - Build and test backend (Maven/Gradle)
  - Build frontend
  - Run integration tests with Testcontainers
  - Lint and format checks
- [ ] Docker Compose for local development (PostgreSQL, backend, frontend)
- [ ] Document architecture decisions as ADRs in [[06 - Leadership/Leadership Index]]

### Concepts Applied
- DDD (Aggregates, Value Objects, Bounded Contexts, Repositories)
- Spring Boot application structure and REST API design
- React with TypeScript
- Refactoring patterns (Fowler)
- GitHub Actions CI/CD
- Database migrations (Flyway)
- Testcontainers

### Design Decisions Log


---

## Phase 2 Build (Weeks 6-12): Event-Driven Architecture & Tax Engine

**Goal:** Extract a tax calculation/reporting service, introduce event-driven patterns, and build the report generation engine.

### Architecture
```
┌──────────┐     ┌──────────────────┐     ┌────────────┐
│  React   │────▶│  Ledger Service  │────▶│ PostgreSQL │
│ Frontend │     │  (Spring Boot)   │     │ (Ledger)   │
└──────────┘     └───────┬──────────┘     └────────────┘
                         │ Kafka Events
                         │ (gig.completed, expense.recorded,
                         │  income.received)
                         ▼
                 ┌──────────────────┐     ┌────────────┐
                 │  Tax Service     │────▶│ PostgreSQL │
                 │  (Go)            │     │ (Tax)      │
                 └──────────────────┘     └────────────┘
                         │
                         ▼
                 PDF Report Generation
```

### Ledger Service (Spring Boot) Tasks
- [ ] Refactor into Spring Modulith — define modules: gig, income, expense, client
- [ ] Implement domain events with Spring Application Events internally
  - `GigCompleted`, `IncomeReceived`, `ExpenseRecorded`
- [ ] Bridge domain events to Kafka for cross-service communication
- [ ] Implement the Outbox Pattern for reliable event publishing
- [ ] Add proper error handling and dead letter queues
- [ ] Apply DDIA concepts: think about consistency guarantees (what if Kafka is down when a gig is completed?)

### Tax Service (Go) Tasks
- [ ] Set up a Go service that consumes Kafka events
- [ ] Maintain a running tax position per user per tax year
  - Total income, categorised expenses, estimated tax liability
  - Apply UK tax bands and National Insurance thresholds
- [ ] Implement report generation
  - Generate PDF tax summary matching HMRC self-assessment categories
  - Include: income by client, expenses by category, mileage log, net profit, estimated tax
- [ ] REST API for the frontend to request reports
- [ ] Handle idempotency — events may be delivered more than once

### Frontend Tasks
- [ ] Tax dashboard — running tax position, estimated liability
- [ ] Report generation page — select tax year, generate and download PDF
- [ ] Expense analytics — charts showing spending by category over time
- [ ] Client analytics — income by client breakdown

### Infrastructure
- [ ] Kafka via Docker Compose
- [ ] GitHub Actions: multi-service build (Java + Go)
- [ ] Observability: Micrometer (Spring) + Prometheus metrics (Go) + Grafana dashboards
- [ ] Load test to understand performance characteristics

### Concepts Applied
- Kafka event-driven patterns and consumer design
- Spring Modulith module boundaries
- Go service development (new language, focused scope)
- DDIA chapters 5-9 (consistency, partitioning, transactions)
- Effective Java concurrency items
- Outbox Pattern, idempotency
- CQRS (write path in Ledger, read/reporting path in Tax Service)
- Multi-language distributed system

### Design Decisions Log


---

## Phase 3 Build (Weeks 13-17): AI Integration

**Goal:** Add AI capabilities that make GigLedger genuinely smarter — auto-categorisation, natural language queries, and an MCP server.

### Architecture
```
┌──────────┐     ┌──────────────────┐
│  React   │────▶│  Ledger Service  │──── Kafka ────▶ Tax Service
│ Frontend │     └──────────────────┘
└──────────┘              │
                          │
    ┌─────────────────────┤
    │                     │
    ▼                     ▼
┌──────────┐     ┌──────────────────┐
│  Claude  │◀───▶│  MCP Server      │
│ Desktop  │     │  (GigLedger)     │
└──────────┘     └──────────────────┘
```

### MCP Server Tasks
- [ ] Build an MCP server exposing GigLedger data to AI agents
  - Tools: `get_tax_summary`, `list_uncategorised_expenses`, `search_gigs`, `get_client_breakdown`, `suggest_expense_category`
  - Follow MCP spec at modelcontextprotocol.io
  - Test with Claude Desktop as the client
- [ ] Enable natural language interactions:
  - "How much tax do I owe so far this year?"
  - "Which expenses haven't I categorised yet?"
  - "Show me my income from ClientX this quarter"
  - "What's my biggest expense category?"

### AI Features Tasks
- [ ] Auto-categorisation of expenses using LLM
  - User enters "Bought a monitor from Amazon" → suggest "Equipment" category
  - Build a prompt that understands HMRC allowable expense categories
  - Let user confirm or override
- [ ] Smart receipt parsing (stretch goal)
  - Upload a receipt image → extract amount, date, vendor, suggest category
- [ ] Tax insight generation
  - "You're £5,000 away from the VAT threshold"
  - "You could save £X by claiming home office expenses"

### AI-Assisted Development
- [ ] Use Claude Code or Cursor to build at least one feature end-to-end
- [ ] Document: What worked? What went wrong? How did you prompt it?
- [ ] Compare AI-generated code vs. your own approach
- [ ] Evaluate for: correctness, security (financial data!), performance, codebase consistency

### Concepts Applied
- MCP server development (author's perspective)
- LLM integration patterns (function calling, structured outputs)
- AI agent evaluation and review
- Security and governance for AI with financial data
- Prompt engineering for domain-specific tasks

### Design Decisions Log


---

## Phase 4 Build (Weeks 18-20+): Write It Up & Ship It

**Goal:** Polish, document, and potentially ship. Turn it into leadership artifacts.

### Ship It
- [ ] Deploy to a cloud provider (consider Railway, Fly.io, or AWS)
- [ ] Set up a landing page
- [ ] Get a few real users testing it (self-employed friends/family)
- [ ] Iterate based on feedback

### Write It Up
- [ ] ADRs for every significant architectural decision across all phases
- [ ] RFC: "How event-driven architecture and AI integration could improve [relevant SAS product area]"
- [ ] Technical onboarding document for the codebase
- [ ] Lessons learned document
- [ ] Tech talk presentation: monolith → distributed → AI-integrated
- [ ] Blog post on building an MCP server for a real application

### Concepts Applied
- ADR and RFC writing (Staff IC core skill)
- Technical communication and documentation
- Architecture storytelling
- Shipping and iterating on a real product
- Teaching and mentoring through writing

---

## Tech Stack

| Layer | Technology | Why |
|-------|-----------|-----|
| Language (primary) | Java 21+ | Your primary language, SAS stack |
| Language (secondary) | Go | Tax/reporting service — learn a new language with focused scope |
| Language (frontend) | TypeScript | Type safety for React |
| Backend Framework | Spring Boot 3.x | Core framework at SAS |
| Frontend Framework | React (Vite) | Modern, widely used |
| Modules | Spring Modulith | Internal architecture boundaries |
| Database | PostgreSQL | Solid relational foundation |
| Messaging | Apache Kafka | Event-driven communication |
| CI/CD | GitHub Actions | What you'll use at SAS |
| Containers | Docker / Docker Compose | Local dev + deployment |
| Observability | Micrometer + Prometheus + Grafana | Production-readiness |
| AI Integration | Custom MCP Server | Learning MCP from server side |
| AI/LLM | Anthropic API | Expense categorisation, NL queries |
| Testing | JUnit 5, Testcontainers, Go testing | Integration testing with real deps |
| PDF Generation | Go (goldmark + chromedp or wkhtmltopdf) | Tax report output |
| DB Migrations | Flyway | Schema versioning |

---

## Repository

**Repo:** *create and add URL*
**Branch strategy:** trunk-based with short-lived feature branches
**CI/CD:** GitHub Actions

#practice #spring #kafka #ddd #ai #mcp #go
