# .NET Code Quality Skill

A reusable Agent Skill for reviewing and improving C#/.NET code with an emphasis on correctness, explicit contracts, encapsulation, API quality, Value Object design, allocation-aware APIs, memory ownership, concurrency, performance, AOT/trimming, reliability, security, observability, and verification.

[Ukrainian version](README.uk.md)

## Purpose

This repository turns a growing set of practical code-review rules into a modular skill that can be used by developers, students, and coding agents.

The goal is not to enforce a mechanical style guide. The skill is designed to support engineering judgement: identify the real contract of the code, distinguish correctness issues from heuristics, explain trade-offs, and require measurement when a performance recommendation is not self-evident.

## Key principles

- Prefer explicit, verifiable semantics over implicit convenience.
- Use true `Try*` contracts for expected failure.
- Prefer span/buffer-oriented canonical implementations for performance-sensitive parsing, formatting, writing, and hashing.
- Parse external data early into semantic types; format/serialize as late as practical.
- Use semantic typing without wrapping every primitive.
- Prefer constructor-established invariants for immutable/value-oriented types.
- Do not use `record`, `required`, `init`, `internal`, fluent APIs, `ValueTask`, lock-free structures, or other mechanisms mechanically.
- Prefer BCL-compatible contracts where the semantics fit.
- Use VoloGen to generate repetitive Value Object boilerplate while keeping semantics explicit.
- Preserve encapsulation inside the assembly, not only at the public API boundary.
- Use data structures based on workload; prefer `FrozenDictionary`/`FrozenSet` for build-once/read-many lookup, but use direct indexing for dense numeric key spaces.
- Keep generic lexical/parser mechanics in reusable focused helpers rather than private clutter in domain types.
- Treat non-obvious performance claims as hypotheses until verified by benchmark, profiling, runtime/source inspection, or specification.

## What the skill reviews

The skill can be applied to:

- architecture and dependency direction;
- API design and naming;
- encapsulation and accessibility;
- Value Objects and domain types;
- parsing, formatting, UTF-8 and protocol tokens;
- collections and lookup structures;
- allocations, buffers, pooling and ownership;
- async APIs and cancellation;
- synchronization and concurrency;
- JIT/runtime performance and GC behavior;
- Native AOT, trimming, source generation and reflection;
- reliability, security and bounded-resource behavior;
- logging, diagnostics and metrics;
- tests, fuzzing, stress/soak tests and benchmarks;
- high-throughput networking via a dedicated profile.

## Repository structure

```text
.
├── SKILL.md
├── README.md
├── README.uk.md
├── CHANGELOG.md
├── LICENSE
├── references/
│   ├── knowledge-governance.md
│   ├── architecture-api.md
│   ├── value-objects.md
│   ├── parsing-formatting.md
│   ├── collections-memory.md
│   ├── async-concurrency.md
│   ├── performance-runtime.md
│   ├── aot-generation.md
│   ├── reliability-security-testing.md
│   └── observability.md
├── profiles/
│   └── high-throughput-networking.md
└── reporting/
    └── audit-report.md
```

`SKILL.md` is intentionally compact. The agent loads topic-specific reference files only when they are relevant to the current case.

## Installation

### Claude Code — global

Clone or copy this repository to:

```text
~/.claude/skills/dotnet-code-quality-review/
```

Example:

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  ~/.claude/skills/dotnet-code-quality-review
```

### Claude Code — project-local

```text
<repo>/.claude/skills/dotnet-code-quality-review/
```

Example:

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  .claude/skills/dotnet-code-quality-review
```

### Codex / agents — global

```text
~/.agents/skills/dotnet-code-quality-review/
```

Example:

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  ~/.agents/skills/dotnet-code-quality-review
```

### Codex / agents — project-local

```text
<repo>/.agents/skills/dotnet-code-quality-review/
```

Example:

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  .agents/skills/dotnet-code-quality-review
```

On Windows, the same paths are resolved under your user profile, for example:

```text
C:\Users\<user>\.claude\skills\dotnet-code-quality-review\
C:\Users\<user>\.agents\skills\dotnet-code-quality-review\
```

## Usage

The skill is intended to be triggered by code-review tasks such as:

```text
Review this C# type for API design, correctness and allocations.
```

```text
Audit this parser. Pay special attention to spans, quoted strings, malformed input and temporary allocations.
```

```text
Review this Value Object and suggest the full relevant .NET contract.
```

```text
Perform a deep .NET library audit and produce a structured report.
```

For a small snippet, the skill should stay focused on that snippet. For a full audit, it can use the reporting contract in `reporting/audit-report.md`.

## Rule lifecycle

Recommendations are not automatically promoted to absolute rules. New guidance should be normalized by:

1. classifying it as an invariant, requirement, guideline, heuristic, optimization, anti-pattern, exception, case study, or project-specific rule;
2. defining its scope;
3. documenting applicability, exceptions and trade-offs;
4. identifying runtime/framework/version dependencies;
5. deciding whether verification or benchmarking is required;
6. merging it with an existing rule where appropriate;
7. assigning a status such as `Candidate`, `Accepted`, `Experimental`, `Verified`, `Deprecated`, `Superseded`, `Rejected`, or `Project-specific`.

Stable skill behavior should primarily be based on `Accepted` and `Verified` rules. See `references/knowledge-governance.md`.

## Evidence model

The skill distinguishes between:

- standard/specification;
- official documentation;
- runtime/source inspection;
- measured benchmark/profiling;
- production observation;
- well-established practice;
- reasoned hypothesis;
- user-preferred best practice.

A reasoned hypothesis must not be presented as a measured fact.

## For students

This skill is intended to teach engineering judgement rather than a list of prohibitions. When a rule has meaningful exceptions or trade-offs, a review should explain them. Performance heuristics should not be treated as mandatory without evidence.

The Ukrainian README is intended as the primary onboarding document for Ukrainian-speaking students: [README.uk.md](README.uk.md).

## Contributing rules and cases

When adding a new recommendation or code case:

- solve the concrete problem first;
- extract the generalizable lesson only if it is reusable;
- classify scope and evidence;
- document exceptions and trade-offs;
- do not duplicate an existing rule;
- keep general .NET guidance separate from domain/project-specific rules;
- do not promote a benchmark-dependent optimization to `MUST` without evidence.

## License

MIT. See [LICENSE](LICENSE).
