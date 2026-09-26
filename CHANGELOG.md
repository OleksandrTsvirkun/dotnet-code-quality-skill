# Changelog

## 2026-09-27 — Consolidation from code-review session

The original monolithic .NET audit guidance was consolidated into one modular skill with topic-specific references.

New/clarified accepted user best practices include:

- true Try-pattern as the preferred contract for expected failure;
- early destination-capacity validation;
- one canonical allocation-free/buffer-oriented implementation with convenience wrappers built on top;
- BCL-contract-first API design and BCL-pattern mimicry when no interface fits exactly;
- constructor-based immutable/value types instead of `required`/`init` by default;
- avoiding `record`/`record struct` as the default Value Object representation;
- VoloGen as the preferred source-generation mechanism for repetitive explicit Value Object contracts;
- `ISpanParsable<T>` / `ISpanFormattable` as baseline textual Value Object contracts;
- UTF-8 span parsing/formatting when natural for the type;
- generic-math/`IMinMaxValue<T>` capability groups when semantically valid;
- canonical/multiple formats owned by the Value Object instead of external formatting logic;
- `Type` preferred over `Kind`;
- strong encapsulation inside assemblies; `internal` is not a convenience default;
- static get-only properties for public predefined immutable values;
- single source of truth for well-known values and canonical parser results;
- `FrozenDictionary`/`FrozenSet` for build-once/read-many mappings, with span alternate lookup where appropriate;
- selective `AggressiveInlining` for tiny hot helpers/comparers;
- fluent APIs reserved primarily for builders/configuration/composition;
- parse early / format late and semantic typing without over-typing;
- generic parser mechanics extracted into narrow reusable helpers;
- case-insensitive protocol token parsing with ordinal/ASCII semantics when specified;
- no redundant `ToWireToken`/`ToProtocolString` when `ToString` is the sole canonical representation;
- case-specific context must not leak into unrelated review cases.

The original audit themes retained and normalized include architecture, API design, collections, allocations, buffers, async/concurrency, GC/runtime performance, AOT/trimming, reliability, security, observability, fuzz/stress/soak testing and benchmarking.
