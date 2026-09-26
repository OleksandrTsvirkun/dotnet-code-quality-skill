# Performance and Runtime

## Measure the right thing

For each hot-path API consider together:

- correctness;
- Big-O;
- allocations/op and bytes/op;
- copies;
- passes over input;
- hash lookups;
- virtual/interface dispatch;
- delegate/closure cost;
- branches and predictability;
- cache locality;
- struct copies;
- atomics;
- lock contention;
- context switches;
- syscalls;
- GC effects;
- worst-case external input;
- AOT/native code size;
- tail latency.

Do not optimize blindly.

Non-trivial complexity added solely for performance should normally be justified by representative benchmark/profiling.

## AggressiveInlining

`MethodImplOptions.AggressiveInlining` is a targeted optimization, not decoration.

Good candidates:

- tiny hot comparers;
- `Equals`/`GetHashCode` helpers;
- tiny value-object operators/wrappers;
- bit/ASCII helpers;
- trivial adapters whose inlining exposes further JIT optimization.

If a tiny wrapper calls another tiny hot helper, both levels can be candidates.

Do not mechanically apply it to:

- large methods;
- loops with substantial bodies;
- cold paths;
- exception-heavy code;
- code where inlining meaningfully increases code size.

Trivial singleton/property getters usually need no hint because the JIT can inline them itself.

For non-obvious cases inspect codegen or benchmark.

## Static deterministic tables

If a lookup table is a pure function of compile-time constants, prefer generated/static data over runtime construction when startup/allocation matters.

Runtime construction is reasonable when:

- data is parameterized;
- configuration is dynamic;
- source size matters more than startup;
- measurement shows the construction is irrelevant.

## Hardware intrinsics

For algorithms with direct ISA support, check hardware-accelerated paths before spending effort on a sophisticated software fallback.

Pattern:

```text
hardware fast path
    -> portable fallback
```

Examples include CRC, vectorizable byte operations and crypto primitives.

Keep a portable fallback and gate intrinsics by support checks.

## GC architecture

Analyze more than B/op:

- allocation rate/sec;
- Gen0/Gen1/Gen2;
- LOH/POH;
- pinning;
- promotion;
- pool retention;
- pause time.

Avoid long-lived pinning without need.

Use `GC.AllocateUninitializedArray` only when the array is guaranteed to be fully overwritten before read and there is evidence the change matters.

## Hot/cold splitting

Keep the common valid path short.

Move rare malformed/diagnostic/fallback/multi-segment cases to slow paths when it improves readability and potentially branch/layout behavior.

## Large value types

Review large structs for:

- collection storage;
- by-value parameters;
- by-value returns;
- copies.

Consider `readonly struct`, `in`, `ref`, `ref readonly`, `scoped` where semantics justify them.

## Time

Use `TimeProvider` or an injectable time abstraction for time-dependent logic.

Measure elapsed duration with monotonic time, not wall-clock time.

## Integer semantics

Check:

- overflow/underflow;
- checked/unchecked;
- signed/unsigned;
- narrowing casts;
- wraparound;
- shift width;
- fixed-point semantics.

Use integer/fixed-point arithmetic when the protocol/algorithm is naturally integer-based.

## Benchmarks

Representative benchmarks should include relevant:

- throughput;
- latency/p50/p95/p99;
- allocations/op;
- bytes/op;
- CPU;
- contention;
- context switches.

Do not rely only on happy-path uncontended microbenchmarks.
