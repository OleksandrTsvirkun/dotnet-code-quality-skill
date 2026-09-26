# Audit Reporting Contract

Use this format for a full audit. Do not force it onto a small code snippet.

## Report sections

1. Executive assessment
2. Current architecture/dependency graph
3. Encapsulation/SOLID findings
4. API/lifetime/ownership findings
5. Value Object/type-model findings
6. Parsing/formatting findings
7. Data-structure findings
8. Allocation/memory/GC findings
9. Async findings
10. Synchronization/concurrency findings
11. AOT/trimming/source-generation findings
12. Reliability/security findings
13. Logging/observability findings
14. Performance findings
15. Proposed architecture
16. Refactoring plan
17. Verification/benchmark plan

## Finding schema

For each material finding include:

```text
Location
Rule / principle
Evidence
Impact
Recommended change
Alternative(s)
Breaking change?
Verification required
Confidence
Evidence level
```

Suggested evidence levels:

- specification/standard;
- official documentation;
- runtime/source inspection;
- measured benchmark/profiling;
- production observation;
- well-established practice;
- reasoned hypothesis;
- user-preferred best practice.

Do not assign performance certainty to a hypothesis that has not been measured.

## Severity discipline

Severity should reflect concrete impact, not the existence of a pattern name.

Do not label something a God Object, Primitive Obsession, Large Switch, etc. without explaining the actual impact on correctness, maintainability, performance or extensibility.
