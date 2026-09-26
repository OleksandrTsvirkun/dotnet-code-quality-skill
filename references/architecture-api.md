# Architecture and API Design

## Dependency and encapsulation rules

Build or infer the actual dependency direction between projects, assemblies, namespaces, abstractions and implementations when the case is architectural.

Check for:

- cyclic and bidirectional dependencies;
- service locator;
- global mutable state;
- singleton abuse;
- hidden dependencies;
- concrete implementation leakage;
- high-level → low-level coupling;
- adapter/infrastructure leakage into core;
- responsibilities joined only because the types are in the same assembly.

### Accessibility

Use the minimum necessary accessibility.

`internal` is not a default collaboration mechanism. Treat `internal for convenience` as a smell when it permits components to bypass each other's invariants or exposes implementation details merely because callers live in the same assembly.

Prefer:

- `private`;
- private nested implementation;
- explicit contracts;
- a corrected responsibility boundary;
- composition through an API that preserves invariants.

`internal` is acceptable when the assembly is intentionally the real encapsulation boundary and the member does not let unrelated code violate component invariants.

Be especially cautious with `InternalsVisibleTo`. Do not introduce it merely so tests can call non-public implementation details.

## Constructors and immutable state

For `readonly struct` and immutable classes, prefer constructor-based initialization for required state.

The constructor should establish all invariants atomically. Avoid using `required` + `init` as a default replacement for constructors.

`required`/`init` can be appropriate for DTO/configuration/generated models where object-initializer semantics are natural and invariants are weak or external.

## API naming

Prefer semantic names.

If a method returns the same cached instance for a given key, use names such as `Get...` or `Resolve...` rather than `Create...`.

Preferred discriminator suffix: `Type`, not `Kind`, unless `Kind` has a distinct domain meaning.

## Fluent APIs

Do not return `this` merely to make commands chainable.

Fluent chaining is natural for:

- builders;
- configuration pipelines;
- transformations;
- query/composition APIs.

For ordinary mutating commands, prefer `void` or a meaningful result.

Do not make an interface return a concrete implementation merely to preserve fluent chaining.

If an object has temporal coupling such as:

`Configure/Add must be called before first Create/Get`

split configuration from runtime behavior, typically:

`Builder/Options -> Build -> Runtime service`.

The runtime service should already be configured.

## Public predefined values

For immutable predefined values prefer:

```csharp
public static T Value { get; } = ...;
```

over:

```csharp
public static readonly T Value = ...;
```

This keeps representation behind an API surface and allows future implementation changes.

The predefined value itself must be immutable.

## Extension points

Do not make everything extensible. For real extension points define:

- threading semantics;
- ownership;
- lifecycle;
- failure isolation;
- versioning expectations;
- AOT/trimming expectations.

Prefer the smallest abstraction sufficient for the operation:

- one interchangeable operation -> delegate may be enough;
- multiple operations/capabilities/metadata -> strategy/interface/adapter.
