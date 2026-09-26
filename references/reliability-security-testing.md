# Reliability, Security and Verification

## Bounded everything

Any structure whose growth can be driven by external input/caller behavior should have an explicit:

- hard/soft limit;
- expiry;
- eviction;
- budget;
- backpressure policy.

## Algorithmic DoS

Look for input that causes:

- O(n²) behavior;
- repeated parsing;
- unbounded backtracking;
- hash flooding;
- excessive sorting;
- CPU amplification;
- memory amplification;
- unbounded allocation.

Protocol parsers should use explicit budgets where needed.

## Failure containment

One operation/item/connection should not unnecessarily terminate a shared loop, worker or process.

Define exception boundaries deliberately.

## Exception policy

Distinguish:

- programming error;
- invalid external input;
- transient infrastructure failure;
- timeout;
- cancellation;
- resource exhaustion;
- invariant violation.

Do not use empty `catch (Exception)`.

Do not use exceptions for expected parse/format/buffer failure when a Try-contract is appropriate.

## Shutdown and lifecycle

`Stop`, `Close`, `Dispose`, `DisposeAsync`, `Cancel` should be reviewed for:

- repeat invocation;
- concurrent invocation;
- partial initialization;
- exception during shutdown.

Stateful async components should enforce at-most-once invariants:

- completion <= 1;
- release <= 1;
- no callback after final disposal;
- timeout vs normal completion has one arbitration mechanism.

## Security

Review:

- input validation;
- resource exhaustion;
- integer overflow;
- secret lifetime;
- sensitive logging;
- unsafe/native boundaries;
- timing-sensitive comparison where relevant.

## Parser verification

Protocol parsers should explicitly test:

- malformed input;
- partial input;
- invalid lengths;
- duplicate parameters;
- unknown values;
- quoting/escaping;
- boundary sizes;
- case behavior;
- hostile complexity cases.

## Testing

Use as appropriate:

- unit tests;
- property-based tests;
- fuzzing;
- concurrency stress tests;
- stress tests;
- soak tests;
- chaos/fault injection.

## Performance regression gates

For critical APIs establish baselines and make regressions visible in:

- allocations;
- latency;
- throughput;
- contention.

When 0 B/op is a realistic contract, test it explicitly.

Document important complexity expectations where regression would be easy to introduce.
