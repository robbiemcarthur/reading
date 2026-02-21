# Operational Trade-Offs

Trade-offs around deployment, observability, and running systems in production.

---

## Containerised vs. Bare Metal / VM Deployment

**Choose containers when:** You want reproducible environments, you need to scale horizontally, your team uses Docker locally, or you want infrastructure-as-code.

**Choose VMs/bare metal when:** You need maximum performance with no overhead, your organisation doesn't have container orchestration expertise, or the application is a single long-running process with simple deployment needs.

**The real trade-off:** Containers add a small performance overhead but give you reproducibility, isolation, and portability. For GigLedger and most modern applications, containers are the default. The real decision is whether you need Kubernetes (complex but powerful) or something simpler like Docker Compose, Railway, or Fly.io.

---

## Feature Flags vs. Long-Lived Branches

**Choose feature flags when:** You want trunk-based development, you need to deploy incomplete features safely, you want to enable features for specific users, or you need to quickly disable a broken feature.

**Choose long-lived branches when:** The feature requires fundamental architectural changes that can't coexist with the current code, or you're working on a version release.

**The real trade-off:** Long-lived branches diverge from main and create painful merges. Feature flags let you merge to main continuously and deploy dark features. The cost is flag management — you need to clean up flags after features are stable, or you end up with a codebase full of dead conditionals. For GigLedger: use trunk-based development with feature flags from the start. This is also the pattern SAS will likely follow.

---

## Structured Logging vs. Unstructured Logging

**Choose structured (JSON) logging when:** You use centralised log aggregation (ELK, Datadog, Splunk), you need to query logs programmatically, or you're running multiple services.

**Choose unstructured when:** You're debugging locally, the application is simple, or you want human-readable console output during development.

**The real trade-off:** Structured logging is slightly harder to read in a terminal but dramatically easier to search, filter, and alert on in production. For GigLedger: structured logging from day one. Use Logback with JSON output for production, plain text for local development (Spring profiles).

---

## Circuit Breaker vs. Simple Retry

**Choose circuit breaker when:** Downstream services can be slow or unavailable, you want to fail fast rather than queue up requests, or cascading failures are a risk.

**Choose simple retry when:** Failures are transient and rare, the operation is idempotent, and the downstream service usually recovers quickly.

**The real trade-off:** Retries are simpler but can make things worse — if a downstream service is struggling, retrying adds load. Circuit breakers detect sustained failures and stop trying, giving the downstream time to recover. For GigLedger Phase 2: the Ledger Service calling the Tax Service should use a circuit breaker (Resilience4j) to handle Tax Service outages gracefully.

---

## Centralised Config vs. Environment Variables vs. Config Files

**Choose centralised config (Spring Cloud Config, Consul) when:** You have multiple services that need shared configuration, you need to change config without redeploying, or you need audit trails on config changes.

**Choose environment variables when:** You're deploying to containers, following twelve-factor app principles, or the config is simple (database URLs, API keys, feature flags).

**Choose config files when:** The application is simple, you want everything in version control, or you're running locally.

**The real trade-off:** Environment variables are the standard for containerised applications and the simplest approach. Centralised config adds operational complexity but is valuable when you have many services with shared config. For GigLedger: environment variables via Docker Compose and GitHub Actions secrets. Only introduce centralised config if the service count justifies it.

---

*Add your own entries below.*

#practice
