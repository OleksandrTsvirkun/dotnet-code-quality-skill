# Knowledge Governance

Use this reference when the user contributes a new recommendation, case, observation, exception, benchmark result, or code-quality rule that may become reusable knowledge.

## Do not promote observations directly to universal rules

For a new recommendation, first determine what it is:

- invariant;
- requirement;
- guideline;
- heuristic;
- optimization;
- anti-pattern;
- exception;
- case study;
- project-specific rule.

Then determine its scope:

- General;
- language/platform-specific;
- domain-specific;
- project-specific.

A specific rule may narrow or override a more general heuristic inside its scope, but should not silently replace it globally.

## Required review questions

Before stabilizing a rule, check:

- where it applies;
- where it does not apply;
- important exceptions;
- trade-offs;
- runtime/framework/version dependencies;
- whether a benchmark, profile, source inspection, specification, or other verification is required;
- whether an equivalent rule already exists;
- whether this is a specialization of an existing rule;
- whether the new rule conflicts with existing guidance.

If there is a conflict, define precedence, an exception, or a replacement explicitly. Do not silently delete the previous rule.

## Normalized knowledge item

For substantial rules, use this shape where useful:

```text
ID
Title
Type
Scope
Status
Rule
Rationale
When applicable
When not applicable
Exceptions
Verification
Examples / Counterexamples
Related rules
Source / Origin
Version notes
```

Not every field is mandatory at the candidate stage.

## Statuses

Use:

- `Candidate`;
- `Accepted`;
- `Experimental`;
- `Verified`;
- `Deprecated`;
- `Superseded`;
- `Rejected`;
- `Project-specific`.

Do not include `Candidate` or `Experimental` guidance in stable skill behavior without explicit marking.

## Obligation level

When useful, classify strength as:

- `MUST`;
- `SHOULD`;
- `MAY`;
- `AVOID`;
- `DO NOT`;
- `MEASURE FIRST`.

Do not promote a performance heuristic to `MUST` without strong evidence.

## Evidence classes

Distinguish:

- standard/specification;
- official documentation;
- runtime/source inspection;
- measured benchmark/profiling;
- production observation;
- well-established practice;
- reasoned hypothesis;
- personal/user preference.

A reasoned hypothesis must never be presented as measured or specified behavior.

When a rule depends on current API/runtime behavior, verify current documentation or source before promoting it to `Verified`.

## Case workflow

For a code case:

1. solve the concrete problem;
2. identify a generalizable lesson;
3. create or refine a rule only if the lesson is reusable;
4. keep the case as an example/counterexample where useful;
5. link it to the relevant rule.

Not every code case deserves a new rule.

## General vs specific

Keep separate:

- general engineering rules;
- .NET/C# rules;
- domain-specific rules;
- project-specific rules.

A specialized profile should add, narrow, or override base guidance only where its scope justifies it. Do not duplicate the entire general rule set into every profile.

## Skill construction

Stable reusable skills should primarily include `Accepted` and `Verified` rules.

Before incorporating rules into a skill:

- define scope;
- remove duplicates;
- resolve conflicts;
- surface important exceptions;
- define verification workflow;
- include examples where useful;
- exclude project-specific details from general skills unless intentionally parameterized.

Student-facing guidance should explain rationale, show useful good/bad examples, surface exceptions, and teach engineering judgement rather than mechanical prohibition.
