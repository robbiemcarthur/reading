# Code Design Trade-Offs

Trade-offs you encounter at the code level. For each, the key question isn't "which is better" but "which is better *for this situation* and why."

---

## Composition vs. Inheritance

**Choose composition when:** You need runtime flexibility, the relationship is "has-a" rather than "is-a", you want to avoid coupling to a base class, or the behaviour might change independently.

**Choose inheritance when:** There's a genuine is-a relationship, you want to enforce a contract across a family of types, the hierarchy is shallow and stable, or you're working within a framework that expects it (e.g., extending Spring's AbstractController).

**The real trade-off:** Inheritance is simpler upfront but creates tight coupling that's painful to change later. Composition requires more boilerplate but gives you flexibility. In long-lived enterprise codebases (like at Barclays or SAS), composition almost always wins because the cost of changing an inheritance hierarchy in a system with hundreds of subclasses is enormous.

**Effective Java reference:** Item 18 — "Favour composition over inheritance"

---

## Immutability vs. Mutability

**Choose immutability when:** You're modelling value objects (Money, Address, DateRange), working with concurrent code, want to reason about state easily, or building domain objects that represent facts.

**Choose mutability when:** Performance is critical and object creation is a measured bottleneck (rare), you're working with builder patterns during construction, or the framework requires it (some JPA entities).

**The real trade-off:** Immutable objects are thread-safe for free, easier to reason about, and eliminate entire categories of bugs. The cost is object creation overhead, which in modern JVMs is almost never a real problem. Default to immutable; make mutable only when you have a measured reason.

**Effective Java reference:** Item 17 — "Minimise mutability"

---

## Checked vs. Unchecked Exceptions

**Choose checked exceptions when:** The caller can reasonably recover from the error and should be forced to handle it. In practice, this is rare in application code.

**Choose unchecked (runtime) exceptions when:** The error represents a programming bug, the caller can't meaningfully recover, or you're building an API where checked exceptions would pollute every method signature up the call stack.

**The real trade-off:** Checked exceptions were a bold Java experiment that the industry has mostly moved away from. They add safety but create coupling — every intermediate layer must either handle or declare them. Spring uses unchecked exceptions throughout for this reason. Modern Java practice: use unchecked for almost everything, reserve checked for truly recoverable situations at API boundaries.

**Effective Java reference:** Items 70-72

---

## Early Return vs. Single Return

**Choose early return when:** It reduces nesting, makes guard clauses clear, and improves readability (most of the time).

**Choose single return when:** The method has complex cleanup logic, you're working in a codebase with that convention, or the control flow is genuinely clearer with one exit point.

**The real trade-off:** This is mostly a readability question. Early returns reduce cognitive load by eliminating nesting and letting the reader stop thinking about edge cases. Single return made more sense in C where you needed cleanup code. In Java with try-with-resources and finally blocks, early return is almost always cleaner.

---

## Primitive Obsession vs. Value Objects

**Choose value objects when:** A concept has validation rules (email, money, postcode), a concept is used in multiple places, you want type safety beyond what primitives offer, or you're doing DDD.

**Choose primitives when:** The value truly is just a number/string with no business meaning, wrapping it would add complexity with no benefit, or you're in a performance-critical hot path.

**The real trade-off:** Value objects make the domain model self-documenting and push validation to the edges. `Money amount` is clearer than `BigDecimal amount` — you can't accidentally pass a quantity where an amount is expected. The cost is more classes, but in a well-structured DDD codebase that's a feature not a bug. GigLedger is a perfect place to practice this — Money, TaxYear, GigId should all be value objects.

---

## Stream API vs. Imperative Loops

**Choose streams when:** You're doing a chain of transformations (filter → map → collect), the pipeline reads like a description of what you want, or you're working with collections in a functional style.

**Choose imperative loops when:** You need to break early, you need mutable accumulation that's awkward in a reduce, the logic has complex branching, or the stream pipeline is getting hard to read.

**The real trade-off:** Streams are more declarative and often more readable for simple transformations. But complex streams with multiple flatMaps, custom collectors, and side effects become harder to debug and reason about than a straightforward loop. The test is: can a team member read this stream pipeline and understand it in 10 seconds? If not, use a loop.

**Effective Java reference:** Items 45-48

---

## Records vs. Classes

**Choose records when:** You're modelling immutable data carriers (DTOs, value objects, API responses), you want equals/hashCode/toString for free, or the class is primarily about holding data.

**Choose classes when:** You need mutable state, the object has complex behaviour beyond data access, you need inheritance, or you're working with JPA entities (records don't work well with JPA's proxy-based lazy loading).

**The real trade-off:** Records enforce immutability and reduce boilerplate massively. Use them aggressively for DTOs, API request/response objects, and value objects. Don't use them for JPA entities or objects with complex lifecycle.

---

*Add your own entries below as you encounter trade-offs in your daily work and GigLedger project.*

#practice #spring
