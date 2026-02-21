# Data & Persistence Trade-Offs

Trade-offs around data storage, access patterns, and persistence strategies.

---

## JPA/Hibernate vs. Spring JDBC Template vs. jOOQ

**Choose JPA when:** Your domain model maps cleanly to tables, you want rapid development with less SQL, queries are straightforward CRUD, and you're okay with the abstraction's limitations.

**Choose JDBC Template when:** You need fine-grained SQL control, queries are complex with multiple joins, you want to avoid the ORM abstraction overhead, or you're working with legacy schemas that don't map well to JPA entities.

**Choose jOOQ when:** You want type-safe SQL in Java, you have complex queries but don't want raw strings, or you want the productivity of an ORM with the control of SQL.

**The real trade-off:** JPA is fast to develop with but hides what's happening at the SQL level. This is fine until it isn't — the N+1 query problem, unexpected lazy loading, and inefficient generated SQL can be painful to debug. JDBC Template gives you full control but more boilerplate. jOOQ is a middle ground with excellent type safety. For GigLedger: start with JPA for the simple CRUD, but be ready to drop to JDBC Template or jOOQ for the reporting queries in Phase 2.

---

## Flyway vs. Liquibase

**Choose Flyway when:** You prefer writing plain SQL migrations, you want simplicity, and your team is comfortable with SQL.

**Choose Liquibase when:** You need database-agnostic migrations (XML/YAML), you need rollback support built-in, or you're working in an enterprise environment that requires it.

**The real trade-off:** Both are solid. Flyway is simpler and more opinionated — SQL files with version numbers. Liquibase is more flexible but more complex. For GigLedger and most Spring Boot projects, Flyway is the pragmatic choice.

---

## Lazy Loading vs. Eager Loading (JPA)

**Choose lazy loading when:** You don't always need the associated data, the association is large, or you want to avoid loading the entire object graph.

**Choose eager loading when:** You always need the associated data, you want to avoid N+1 queries, or the association is small and cheap to load.

**The real trade-off:** Lazy loading is JPA's default and it's usually correct. The danger is the N+1 problem — loading a list of gigs and then triggering a separate query for each gig's client. The fix isn't switching to eager loading globally — it's using fetch joins or entity graphs for specific use cases. Always check what SQL your queries actually generate.

---

## UUID vs. Sequential ID for Primary Keys

**Choose UUIDs when:** You need to generate IDs without database coordination (important for distributed systems), you don't want to expose sequential IDs in URLs (security), or you need IDs before persisting.

**Choose sequential IDs when:** You want better index performance (sequential inserts are faster for B-tree indexes), storage size matters, or you need human-readable IDs for debugging.

**The real trade-off:** UUIDs are better for distributed systems and API exposure but worse for database index performance due to random insertion patterns. A good middle ground is UUIDv7 (time-ordered UUIDs) which gives you both uniqueness and sequential index behaviour. For GigLedger: UUIDs for external-facing IDs (in URLs and APIs), sequential IDs internally if performance matters.

---

## Normalised vs. Denormalised Schema

**Choose normalised when:** Data integrity is critical, you want to avoid update anomalies, storage is not a concern, and your read patterns involve flexible queries.

**Choose denormalised when:** Read performance is critical, your read patterns are well-known and stable, you're building read models for dashboards or reports, or you're optimising a specific bottleneck.

**The real trade-off:** Normalisation prevents data inconsistency but requires joins for reads. Denormalisation speeds up reads but means updating data in multiple places. In GigLedger: normalised for the core ledger (data integrity for financial records), denormalised read models for the tax dashboard in Phase 2 (CQRS pattern).

**DDIA reference:** Chapter 2 and 3 cover this in depth.

---

*Add your own entries below.*

#practice #spring
