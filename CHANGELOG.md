# Changelog

## 1.0.0 — 2026-09-27

Initial public release of the modular .NET Code Quality Skill.

Highlights:

- English is the normative/default language for skill and reference content.
- Ukrainian student-facing documentation is available in `README.uk.md`.
- Added knowledge-governance workflow for rule type, scope, status, evidence, conflicts and verification.
- Added modular guidance for architecture/API design, Value Objects, parsing/formatting, collections/memory, async/concurrency, runtime performance, AOT/trimming/source generation, reliability/security/testing and observability.
- Added a high-throughput networking profile.
- Added structured audit reporting guidance.
- Added Git Flow contribution policy.
- Consolidated accepted practices from the original code-quality audit guidance without carrying its duplication into the stable skill.

### Accepted practices included in 1.0.0

- true Try-pattern for expected failure;
- early destination-capacity validation;
- one canonical allocation-free/buffer-oriented implementation with convenience wrappers on top;
- BCL-contract-first API design;
- constructor-based immutable/value types instead of `required`/`init` by default;
- explicit Value Object semantics instead of defaulting to `record`;
- VoloGen for repetitive explicit Value Object contracts;
- `ISpanParsable<T>` / `ISpanFormattable` and UTF-8 span contracts where natural;
- coherent generic-math capability groups where semantically valid;
- Value Object-owned canonical/multiple formats;
- `Type` preferred over `Kind` unless the domain distinguishes them;
- strong encapsulation inside assemblies; `internal` is not a convenience default;
- static get-only properties for public predefined immutable values;
- a single source of truth for well-known values;
- `FrozenDictionary`/`FrozenSet` for build-once/read-many lookup where appropriate;
- direct indexing for dense numeric key spaces;
- selective `AggressiveInlining` for tiny hot helpers;
- fluent APIs primarily for builders/configuration/composition;
- parse early / format late;
- semantic typing without over-typing;
- reusable parser mechanics extracted into narrow helpers;
- ordinal/ASCII semantics for case-insensitive protocol tokens when specified;
- no redundant wire-format methods when `ToString` is the sole canonical representation;
- case-specific assumptions do not leak into unrelated reviews.
