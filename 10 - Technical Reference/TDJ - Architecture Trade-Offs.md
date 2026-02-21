# Architecture Trade-Offs

Trade-offs at the system and service level. These are the decisions that show up in design reviews and architectural discussions.

---

## Monolith vs. Microservices

**Choose monolith when:** You're a small team (fewer than ~8 engineers), the domain boundaries aren't clear yet, you need to move fast and iterate, or you're building a new product where the domain model is still evolving.

**Choose microservices when:** You have clear bounded contexts, teams need to deploy independently, different parts of the system have genuinely different scaling requirements, or you're at a scale where a monolith is a coordination bottleneck.

**The real trade-off:** Microservices buy you independent deployment and team autonomy at the cost of distributed systems complexity — network failures, eventual consistency, distributed tracing, and operational overhead. The question isn't "are microservices better?" — it's "is the coordination cost of a monolith higher than the operational cost of distribution?" For most teams, the answer is monolith-first. GigLedger starts as a monolith for exactly this reason.

**Common mistake:** Breaking into microservices before you understand the domain boundaries. You end up with a distributed monolith — all the costs of both approaches with the benefits of neither.

---

## Synchronous (REST) vs. Asynchronous (Events/Messaging)

**Choose synchronous when:** The caller needs an immediate response, the operation is fast, you need strong consistency, or the workflow is naturally request-response.

**Choose asynchronous when:** The operation is long-running, you need to decouple services, the downstream system might be unavailable, you need to handle spikes in load, or eventual consistency is acceptable.

**The real trade-off:** Synchronous is simpler to reason about, debug, and test. You get an immediate response and know if something failed. Asynchronous decouples services and handles failures more gracefully, but introduces complexity: message ordering, idempotency, dead letter queues, eventual consistency. In GigLedger, recording a gig is synchronous (user needs confirmation), but updating the tax summary when an expense is recorded can be asynchronous via Kafka.

---

## CQRS vs. Single Model

**Choose CQRS when:** Read and write patterns are significantly different, you need to optimise reads independently (e.g., denormalised views for dashboards), or the write model is complex but reads need to be fast and simple.

**Choose single model when:** Read and write patterns are similar, the added complexity isn't justified, or you're in the early stages of a project and don't yet know where the bottlenecks are.

**The real trade-off:** CQRS lets you optimise each side independently but doubles your models and introduces synchronisation complexity. In GigLedger, the write side (recording gigs, expenses, income) has different needs from the read side (tax dashboard, annual report). That's a natural CQRS candidate — but only introduce it in Phase 2 once the domain is stable.

---

## SQL vs. NoSQL

**Choose SQL (PostgreSQL) when:** Your data is relational, you need ACID transactions, you need complex queries and joins, data integrity is critical, or your schema is well-understood.

**Choose NoSQL when:** Your data is genuinely document-shaped, you need horizontal scaling beyond what a single Postgres can handle, your schema is highly variable, or you're storing large volumes of semi-structured data.

**The real trade-off:** SQL databases are incredibly capable and PostgreSQL specifically handles JSON, full-text search, and many "NoSQL" workloads well. The common mistake is choosing NoSQL because it's trendy, then struggling with the lack of transactions and joins. For GigLedger — financial data with strong consistency requirements — PostgreSQL is the obvious choice.

**DDIA reference:** Chapter 2 covers this extensively.

---

## Modular Monolith (Spring Modulith) vs. True Microservices

**Choose modular monolith when:** You want bounded contexts and clean module boundaries without the operational overhead of separate deployments. You get compile-time enforcement of boundaries, in-process communication, and simple transactions, with the option to extract modules into services later.

**Choose true microservices when:** Modules genuinely need independent deployment, different scaling characteristics, or different technology stacks.

**The real trade-off:** Spring Modulith gives you 80% of the organisational benefits of microservices (clear boundaries, independent teams, domain isolation) with 20% of the operational cost. The migration path to true microservices is straightforward if you later need it. This is why GigLedger uses Modulith in Phase 2 before extracting the Tax Service.

---

## Event Sourcing vs. State-Based Persistence

**Choose event sourcing when:** You need a complete audit trail, the business cares about the history of changes (not just current state), you need to rebuild state from different points in time, or the domain naturally describes itself in terms of events.

**Choose state-based (CRUD) when:** You only care about current state, the overhead of rebuilding from events isn't justified, the team isn't experienced with event sourcing patterns, or the domain is simple.

**The real trade-off:** Event sourcing gives you a complete audit trail and the ability to replay events, but it's significantly more complex — you need snapshot strategies, event versioning, and the team needs to think in events rather than state. For GigLedger, state-based persistence is the right call for the core ledger. But the Kafka events between services give you some of the audit trail benefits without full event sourcing.

---

## API Gateway vs. Direct Service-to-Service

**Choose an API gateway when:** You have multiple services behind a single public API, you need cross-cutting concerns (auth, rate limiting, logging) in one place, or you want to shield clients from internal service topology.

**Choose direct communication when:** You have a simple topology, the overhead of another infrastructure component isn't justified, or services are internal-only.

**The real trade-off:** API gateways centralise cross-cutting concerns but introduce a single point of failure and another thing to operate. For GigLedger, the React frontend talks to the Ledger Service directly in Phase 1. An API gateway becomes worth considering only if the service topology gets complex enough to justify it.

---

*Add your own entries below as you encounter architectural trade-offs.*

#practice #system-design
