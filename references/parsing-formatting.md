# Parsing, Formatting and Representation Boundaries

## Core philosophy

Use:

```text
Parse early.
Format late.
```

At input boundaries:

```text
wire/string/bytes
    -> parse + validate
    -> typed representation
```

Inside the system, keep typed values.

At output boundaries:

```text
typed representation
    -> format/serialize at the last practical moment
    -> wire/string/bytes
```

Avoid premature stringification.

## Semantic typing without over-typing

Do not pass a protocol/domain concept as `string`/primitive merely because its wire form is textual.

Create a Value Object when it adds:

- invariants;
- parsing/formatting rules;
- comparison rules;
- reusable domain behavior;
- clearer units or meaning.

Prefer an existing standard type when it already models the concept, e.g. `TimeSpan` for a duration when no stronger domain constraints are needed.

Do not create wrappers mechanically. An opaque tag can remain a `string` if a dedicated type adds no value.

## Try-pattern as the preferred contract

Expected failure should normally be modeled without exceptions.

Prefer:

```csharp
bool TryParse(..., out T value);
bool TryFormat(Span<char> destination, out int charsWritten, ...);
bool TryWrite(Span<byte> destination, out int bytesWritten);
bool TryComputeHash(..., Span<byte> destination, out int bytesWritten);
```

Use exceptions for:

- programming errors;
- invariant violations;
- truly exceptional infrastructure/runtime failures.

Do not use exceptions as normal parser/formatting control flow.

## Capacity validation

When the required output size is cheaply knowable, validate destination capacity before expensive work or partial output.

For a `Try*` API on insufficient capacity:

- return `false`;
- set `written` to `0` unless the documented contract says otherwise;
- do not expose a logically partial result.

If a required-size query is natural, provide one.

## One canonical optimized implementation

For performance-sensitive transformations, prefer one canonical non-allocating core.

Examples:

```text
TryFormat(Span<char>)
    -> ToString convenience

TryComputeHash(..., Span<byte>)
    -> byte[] convenience overload

TryWrite(IBufferWriter<byte>/Span<byte>)
    -> allocating convenience API
```

Do not implement the same transformation logic independently in multiple overloads.

Convenience APIs may be ordinary overloads or extension methods. Do not require extension methods when they add no benefit.

## Standard contracts first

Use standard BCL contracts when they fit naturally:

- `ISpanParsable<TSelf>`;
- `ISpanFormattable`;
- `IUtf8SpanParsable<TSelf>`;
- `IUtf8SpanFormattable`;
- `IFormattable`.

If a standard interface does not fit exactly, mimic established BCL semantics:

- `Try*`;
- caller-provided destination;
- `out written`;
- predictable failure behavior;
- non-allocating core;
- allocating convenience wrapper.

Do not contort the domain model merely to implement an interface.

## String and span

Do not materialize a `string` solely to parse, compare or look up a `ReadOnlySpan<char>`.

Avoid:

```csharp
span.ToString()
```

when an alternate/span-aware API is available.

Similarly, avoid:

```text
UTF-8 bytes -> string -> parse
value -> string -> UTF-8 bytes
```

when direct UTF-8 parsing/formatting is available.

## Case-insensitive protocol tokens

When the protocol defines case-insensitive token semantics, parse with `StringComparison.OrdinalIgnoreCase` or the equivalent ASCII comparer.

Do not allocate normalized strings through `ToLower()`/`ToUpper()`.

Formatting should emit one canonical representation; parsing may accept permitted casing/aliases.

## Domain parser vs generic parser mechanics

A domain/value type should own domain semantics, not generic lexical mechanics.

Keep inside the domain parser:

- scheme/type interpretation;
- known parameter meaning;
- domain-specific validation;
- construction of a valid result.

Move reusable lexical mechanics into narrow helpers:

- whitespace skipping;
- separator/comma handling;
- token scanning;
- quoted-string scanning;
- quote/unquote;
- escape/unescape;
- delimiter handling;
- ASCII/token validation.

Avoid monolithic `StringHelper`/`ParsingHelper` classes. Use narrow helpers at the correct reuse level.

## Single-pass parsing

For untrusted/protocol input, prefer a single-pass parser where practical.

Avoid temporary `Dictionary<string,string>` construction when the parser already knows the finite set of parameters it needs.

Parse known values directly into locals/typed values.

Define explicit policy for:

- malformed quoted strings;
- duplicate parameters;
- unknown parameters;
- incomplete syntax;
- escaped sequences;
- whitespace around delimiters.

Do not let incidental collection behavior become parser policy.

## Formatting composition

`ValueStringBuilder`, `IBufferWriter<T>` or a focused writer can be appropriate adapters over caller/local buffers.

Ensure child values are appended through span formatting rather than hidden per-item `ToString()` allocations.

For trivial hot formats, direct writes can outperform generic formatting machinery, but use benchmarks before replacing a clear reusable implementation.
