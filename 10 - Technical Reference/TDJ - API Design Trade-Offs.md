# API Design Trade-Offs

Trade-offs around API design, contracts, and communication patterns.

---

## REST vs. GraphQL vs. gRPC

**Choose REST when:** Your API serves multiple clients with different needs, you want simplicity and broad tooling support, the API is resource-oriented, or you're building a public API.

**Choose GraphQL when:** Clients need flexible queries (fetch exactly the fields they need), you have a complex data graph with many relationships, or you want to avoid multiple round trips for nested data.

**Choose gRPC when:** You need high-performance service-to-service communication, you want strong typing via Protocol Buffers, or you're in a polyglot microservices environment where schema-first matters.

**The real trade-off:** REST is the simplest and most widely understood. GraphQL gives clients flexibility but moves query complexity to the server and makes caching harder. gRPC is fastest but requires more tooling and isn't browser-friendly without a proxy. For GigLedger: REST for the frontend API (simple, well-understood), consider gRPC for the internal Ledger→Tax Service communication in Phase 2 if performance matters.

---

## DTOs vs. Exposing Domain Objects

**Choose DTOs when:** You want to decouple your API contract from your domain model, you need to shape data differently for different consumers, or you want to avoid leaking internal implementation details.

**Choose exposing domain objects when:** The domain model and API contract are genuinely the same thing, the overhead of mapping isn't justified, or you're building a quick prototype.

**The real trade-off:** Exposing domain objects is faster to build but creates tight coupling between your API contract and your internal model. Changing your domain model breaks your API. DTOs add a mapping layer but give you freedom to evolve the domain independently. For GigLedger: always use DTOs for the public API. Use records for the DTOs — they're perfect for this.

---

## Versioning: URL Path vs. Header vs. No Versioning

**Choose URL path versioning (/v1/gigs) when:** You want explicit, visible versioning that's easy to test and route.

**Choose header versioning when:** You want a cleaner URL structure and are okay with less visibility.

**Choose no explicit versioning when:** You commit to backward-compatible evolution (additive changes only) and use feature flags or content negotiation.

**The real trade-off:** URL path versioning is the simplest and most common. The cost is ugly URLs when you reach v3, v4. The real question is: how often will you actually break backward compatibility? If you design additive-only APIs from the start, you may never need to version. For GigLedger: start without versioning, design for backward compatibility, add /v2 only if you need a breaking change.

---

## Pagination: Offset vs. Cursor-Based

**Choose offset (page/size) when:** You need to jump to arbitrary pages, the dataset is small to medium, or the simplicity matters more than edge cases.

**Choose cursor-based when:** The dataset is large, data changes frequently (new records would shift pages), or you need consistent results for infinite scroll.

**The real trade-off:** Offset pagination is simpler but breaks when data is inserted or deleted between pages (users see duplicates or miss records). It's also slow for large offsets (the database still scans all skipped rows). Cursor-based is more robust but you can't jump to "page 47." For GigLedger: offset pagination is fine initially — the dataset per user won't be huge. Switch to cursor-based if performance becomes an issue.

---

*Add your own entries below.*

#practice
