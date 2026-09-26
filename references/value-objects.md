# Value Object Design

## Preferred model

For domain/value types, prefer explicit semantics over compiler-generated `record` semantics.

Default model:

- ordinary `readonly struct`, struct, or immutable class as appropriate;
- constructor establishes invariants;
- get-only/readonly state;
- explicit supported capabilities;
- full relevant BCL-compatible contract;
- VoloGen generates repetitive boilerplate.

Do not implement capabilities that are not semantically natural for the type.

## Equality

When equality is meaningful, implement a coherent capability group:

- `IEquatable<T>`;
- `Equals(T)`;
- `Equals(object?)`;
- `GetHashCode()`;
- `==` and `!=`;
- `IEqualityOperators<...>` where applicable.

All equality paths must use the same semantics.

For case-insensitive protocol/text values, use the correct ordinal/ASCII semantics; do not normalize by allocating `ToLower()`/`ToUpper()` strings.

## Ordering

Only define total ordering when the domain actually has one.

If ordering is meaningful, implement the coherent group:

- `IComparable`;
- `IComparable<T>`;
- `<`, `<=`, `>`, `>=`;
- `IComparisonOperators<...>`.

Do not add ordering merely because an underlying primitive is comparable.

## Parsing

For textual Value Objects, use `ISpanParsable<TSelf>` as the preferred base contract when parsing is natural.

Canonical path:

```text
ReadOnlySpan<char> -> TryParse -> typed value
```

`IParsable<TSelf>` comes as part of the broader contract but should not force string materialization.

If the type naturally participates in UTF-8/wire/file processing, also implement `IUtf8SpanParsable<TSelf>`.

Parsing should be tolerant only where the domain/specification permits it. For protocol tokens whose comparison is case-insensitive, prefer `OrdinalIgnoreCase`/equivalent ASCII semantics.

## Formatting

For textual Value Objects, prefer `ISpanFormattable`.

Canonical path:

```text
typed value -> TryFormat(Span<char>) -> text
```

If the type naturally has UTF-8 representation, also implement `IUtf8SpanFormattable`.

`ToString()` and other allocating overloads are convenience layers over the canonical formatting logic.

### One canonical representation

If a Value Object has one natural canonical textual representation, use the standard formatting contract directly. Do not add redundant methods such as:

- `ToWireToken()`;
- `ToProtocolString()`;
- `AsText()`.

Use a separate format specifier only when the type genuinely has multiple natural representations.

### Multiple formats

When a Value Object legitimately has multiple representations, keep those representations inside the type.

Examples of semantic categories:

- general/default;
- canonical/round-trip;
- compact;
- display;
- protocol/wire;
- diagnostic.

Do not build the representation externally with interpolation, concatenation or ad-hoc helpers if it is conceptually a format of the Value Object.

## Numeric and bounded Value Objects

When the semantics fit, consider:

- `IMinMaxValue<TSelf>`;
- `IAdditionOperators`;
- `ISubtractionOperators`;
- `IMultiplyOperators`;
- `IDivisionOperators`;
- `IModulusOperators`;
- unary operators;
- increment/decrement operators;
- `IAdditiveIdentity`;
- `IMultiplicativeIdentity`.

Use `INumberBase<TSelf>` / `INumber<TSelf>` only when the type truly behaves as a number. A type wrapping a number is not automatically a numeric type.

## Cloning

Use the generic `IClonable<T>` contract when cloning is a real semantic operation. Do not introduce the non-generic `ICloneable` merely for completeness.

## Predefined values and canonicalization

Known immutable values should normally be exposed as static get-only properties.

Parsing known values should return canonical predefined instances where practical instead of allocating equivalent duplicate values.

Use one source of truth for the known-value catalog.

## Default struct semantics

If `default(T)` intentionally maps to a valid semantic value, centralize that rule in one helper/constant/path.

Do not scatter expressions such as:

```csharp
_field ?? "default-literal"
```

through equality, formatting, hashing and parsing.

If default is invalid, do not silently map it to an arbitrary valid value.

## VoloGen

VoloGen is the preferred source-generation mechanism for repetitive Value Object API surface.

Use VoloGen to reduce boilerplate for:

- equality members/operators;
- comparison members/operators;
- parse/TryParse families;
- char-span and UTF-8 parsing;
- formatting/TryFormat families;
- generic-math capability groups;
- clone/copy contracts;
- predefined repetitive members where appropriate.

The developer chooses the semantics explicitly; VoloGen expands that choice into a consistent BCL-compatible API surface.

Generated boilerplate is preferred to hidden semantics when it keeps the contract explicit and reviewable.
