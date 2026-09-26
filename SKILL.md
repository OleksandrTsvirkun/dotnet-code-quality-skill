---
name: dotnet-code-quality-review
description: >
  Reviews and improves C#/.NET code with emphasis on explicit contracts, encapsulation,
  value-object design, span/Try-based APIs, allocations, data structures, async/concurrency,
  runtime performance, AOT/trimming, reliability, security, observability and verification.
  Use when the user supplies C#/.NET code, asks for code-quality recommendations, refactoring,
  performance review, API review, or a full library audit.
---

# .NET Code Quality Review

## 1. Purpose

Use this skill to review concrete C#/.NET code and produce technically justified, reusable recommendations.

The skill is not a style linter. Review actual semantics:

- invariants and correctness;
- API contracts and encapsulation;
- ownership and lifetime;
- parsing and formatting;
- allocations and copies;
- data-structure suitability;
- async and synchronization semantics;
- hot-path behavior;
- AOT/trimming compatibility;
- reliability, security and failure modes;
- observability;
- testability and verification.

When the user supplies a small code fragment, solve that fragment first. Do not turn every answer into a full audit.

## 2. Core workflow

For each case:

1. Identify the actual responsibility and contract of the code.
2. Separate correctness issues from design improvements and performance hypotheses.
3. Apply only rules relevant to the case.
4. Prefer a simpler contract before a more complicated optimization.
5. State important edge cases and trade-offs.
6. Mark performance-sensitive non-obvious changes as `MEASURE FIRST` unless the benefit follows directly from removing unnecessary work or allocation.
7. If the user refines or rejects a recommendation, update the rule instead of defending the previous wording.
8. Do not carry case-specific assumptions into unrelated code fragments.

Use the following evidence levels:

- `Specification / standard`;
- `Official documentation`;
- `Runtime/source inspection`;
- `Measured benchmark / profiling`;
- `Production observation`;
- `Well-established practice`;
- `Reasoned hypothesis`;
- `User-preferred best practice`.

Do not present a hypothesis as a measured fact.

## 3. Rule precedence

When rules conflict, use this precedence:

1. explicit user requirement for the current case;
2. accepted user best practices in this skill;
3. relevant specialized profile;
4. .NET-specific guidance;
5. general engineering heuristic.

A more specific rule has priority only inside its scope.

## 4. Stable user best practices

Treat the following as preferred defaults unless the concrete semantics justify an exception:

- Prefer true `Try*` contracts for expected failure: `TryParse`, `TryFormat`, `TryWrite`, `TryComputeHash`, etc.
- Validate destination capacity as early as possible once the required size is known.
- Prefer a single canonical allocation-free/buffer-oriented implementation; build allocating and convenience overloads on top.
- Prefer standard BCL contracts (`ISpanParsable<T>`, `ISpanFormattable`, `IUtf8SpanParsable<T>`, `IUtf8SpanFormattable`, generic-math interfaces) when they fit. If no standard interface fits, mimic the BCL contract shape rather than inventing unrelated conventions.
- Parse textual/wire data early into typed values; format/serialize to a concrete wire representation as late as possible.
- Avoid premature stringification and primitive obsession, but do not create wrapper types without semantic benefit.
- For immutable/value-oriented types, prefer constructor-based initialization over `required`/`init`.
- Avoid `record`/`record struct` as the default for domain/value types; prefer explicit semantics and generate repetitive boilerplate with VoloGen.
- Preserve encapsulation inside an assembly. Do not use `internal` as a convenience substitute for correct boundaries.
- Prefer `Type` over `Kind` in names unless `Kind` has a distinct domain meaning.
- Prefer static get-only properties for public predefined immutable values over public `static readonly` fields.
- Reuse the same catalog of well-known values for parsing/normalization; do not duplicate literals in separate switches and registries.
- Prefer `FrozenDictionary`/`FrozenSet` for build-once/read-many mappings/membership, including span alternate lookup when available and relevant.
- Do not replace a dense numeric/byte lookup with a dictionary; direct indexing remains the natural representation.
- Use `AggressiveInlining` selectively for tiny hot helpers/comparers/operators where it can remove abstraction/call overhead; do not decorate large or cold methods mechanically.
- Do not make mutating command APIs fluent by default. Fluent chaining belongs primarily to builders/configuration/composition APIs.
- Generic lexical/parser mechanics belong in narrow reusable helpers, not as private clutter in a domain/value type.

Detailed guidance is in the reference files.

## 5. Knowledge normalization

When the user contributes a new reusable recommendation, case, exception or observation, classify and scope it before treating it as a stable rule. Use `references/knowledge-governance.md`.

Stable behavior should primarily be based on Accepted/Verified guidance. Candidate or Experimental guidance must remain explicitly marked. Performance heuristics that require measurement should use `MEASURE FIRST`, not `MUST`.

## 6. Reference routing

Read only the references relevant to the current case:

- `references/knowledge-governance.md` — rule classification, scope, status, evidence, conflicts and skill evolution.
- `references/architecture-api.md` — encapsulation, dependencies, API surface, fluent/builder design, lifecycle.
- `references/value-objects.md` — Value Object contracts, VoloGen, equality/comparison, parsing/formatting interfaces, constructors, predefined values.
- `references/parsing-formatting.md` — Try-pattern, spans, UTF-8, formatting, parser decomposition, protocol tokens.
- `references/collections-memory.md` — collections, Frozen*, alternate lookup, buffers, pooling, zero-copy, ownership, layout.
- `references/async-concurrency.md` — Task/ValueTask, cancellation, synchronization, channels, races, backpressure.
- `references/performance-runtime.md` — hot paths, JIT/inlining, GC, intrinsics, time, complexity, benchmarking.
- `references/aot-generation.md` — source generation, trimming, Native AOT, reflection, interop.
- `references/reliability-security-testing.md` — bounds, DoS, failure containment, exceptions, fuzz/stress/soak.
- `references/observability.md` — logging, metrics and diagnostics.
- `profiles/high-throughput-networking.md` — apply only for network/media/high-throughput code.
- `reporting/audit-report.md` — use for a full audit or when the user asks for structured findings.

## 7. Review style

For a small code case, normally return:

- the key issue(s);
- the preferred change;
- rationale;
- edge cases / exceptions;
- whether a benchmark, profiling or specification check is needed.

Do not repeat the entire knowledge base.

When the user explicitly agrees with a reusable recommendation, treat it as accepted for the rest of the conversation.

## 8. Anti-dogma rules

Do not recommend mechanically:

- an interface for every class;
- `record` to avoid boilerplate;
- `internal` to make collaboration easier;
- `required`/`init` instead of constructors;
- `ConcurrentDictionary` instead of every `Dictionary`;
- lock-free structures everywhere;
- `ValueTask` instead of every `Task`;
- `Span<T>` instead of every `string`;
- Pipelines as the core abstraction of every I/O library;
- `unsafe` for micro-optimizations;
- `FrozenDictionary` for dense integer indexing;
- a custom Value Object for every primitive;
- full rewrites when a smaller boundary correction is enough.

Prefer engineering judgement over pattern application.
