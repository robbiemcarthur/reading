# Testing Trade-Offs

Trade-offs around testing strategies, scope, and tooling.

---

## Unit Tests vs. Integration Tests

**Choose unit tests when:** You're testing pure business logic, the logic is complex with many edge cases, you want fast feedback, or the behaviour is independent of external systems.

**Choose integration tests when:** You need to verify that components work together correctly, you're testing database queries, API endpoints, or message handling, or the behaviour depends on infrastructure.

**The real trade-off:** Unit tests are fast and precise but can miss issues that only appear when components interact. Integration tests catch real bugs but are slower and more fragile. The classic testing pyramid (many unit tests, fewer integration tests, even fewer E2E tests) is a good default, but for Spring Boot applications the "testing honeycomb" is often more practical — a thin layer of unit tests for domain logic, a thick layer of integration tests with Testcontainers for the service layer, and minimal E2E tests.

---

## Testcontainers vs. In-Memory Databases (H2)

**Choose Testcontainers when:** You want tests that run against the real database, you're using database-specific features (PostgreSQL JSON, full-text search), or you want confidence that your SQL actually works in production.

**Choose H2 when:** You need speed above all else, your queries are standard SQL with no vendor-specific features, or you're in an environment where Docker isn't available.

**The real trade-off:** H2 behaves differently from PostgreSQL in subtle ways — different SQL syntax, different type handling, different index behaviour. Tests that pass on H2 can fail in production. Testcontainers eliminates this entirely by running the real database. The startup cost is a few seconds per test class. For GigLedger: always Testcontainers. Financial calculations must be tested against the real database.

---

## Mocking vs. Real Dependencies

**Choose mocks when:** You're isolating a specific unit of logic, the real dependency is slow or unreliable, you need to simulate error conditions, or you're testing how your code handles specific responses.

**Choose real dependencies when:** You want to verify actual integration behaviour, the dependency is cheap to run (via Testcontainers), or mocking would hide real bugs.

**The real trade-off:** Over-mocking creates tests that test your mocks rather than your code. The test passes but the real integration is broken. Under-mocking creates slow, flaky tests. The sweet spot: mock at architectural boundaries (external APIs, third-party services) but use real implementations for internal dependencies and databases.

---

## TDD vs. Test-After

**Choose TDD when:** You're implementing complex business logic with clear requirements, you want the tests to drive your design, or you're working on code where correctness is critical (financial calculations in GigLedger).

**Choose test-after when:** You're exploring or prototyping, the design is still fluid, or you're working with UI code where the feedback loop is visual.

**The real trade-off:** TDD produces better-designed code (because untestable code gets caught early) and higher test coverage, but it's slower upfront and can feel constraining during exploration. For GigLedger's tax calculation logic: TDD is ideal. For the initial domain model exploration: test-after is fine while you're figuring out the shape of things.

---

*Add your own entries below.*

#practice
