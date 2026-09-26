# Collections, Memory and Buffering

## Choose structures from workload

For frequently used collections determine:

- element count;
- key-space density;
- read/write ratio;
- ordering;
- uniqueness;
- range-query needs;
- lifetime;
- concurrency;
- hot/cold path.

Do not use `Dictionary<TKey,TValue>` as a universal solution.

Consider as appropriate:

- `Dictionary` / `HashSet`;
- `FrozenDictionary` / `FrozenSet`;
- `SearchValues`;
- `SortedDictionary` / `SortedSet`;
- `PriorityQueue`;
- immutable collections;
- concurrent collections;
- arrays/direct lookup tables;
- bit masks/bit sets;
- ring buffers;
- channels;
- SPSC/MPSC queues.

## Frozen collections

For build-once/read-many mapping/membership, prefer considering `FrozenDictionary`/`FrozenSet` as the default read-only lookup structure.

Do not reject Frozen collections merely because the set is small; their factory can choose specialized internal representations.

For string keys:

- use the comparer that matches the contract;
- use alternate `ReadOnlySpan<char>` lookup when the target framework and comparer support it;
- avoid `span.ToString()` only for lookup.

Do not manually build complex `switch(length)`/hash-dispatch logic merely on the assumption that it will beat Frozen. Measure if such micro-optimization matters.

Exceptions:

- 1–2 direct comparisons may be clearer;
- dense integer/byte key spaces favor direct indexing;
- mutable registries require a mutable structure.

## Dense key spaces

For small dense integer ranges, especially byte-sized `0..255`, prefer array/span/direct lookup.

Do not replace direct indexing with Dictionary/FrozenDictionary.

## Small collections

For very small collections, linear scan over contiguous storage can outperform hashing.

Consider:

- hash cost;
- allocation;
- branching;
- cache locality;
- indirection.

Do not invent hard element-count thresholds without workload evidence.

## Sorted/priority use cases

If the code repeatedly needs sorted traversal, min/max or priority extraction, consider data structures that maintain the required order instead of repeatedly sorting a general-purpose collection.

## Capacity

When approximate size is known, set capacity for `List`, `Dictionary`, `HashSet`, `ArrayBufferWriter`, etc. when it meaningfully avoids reallocations/rehashes.

## Allocation review

Look for avoidable:

- `ToString`;
- `ToArray`;
- `ToList`;
- `Substring`;
- `Split`;
- temporary collections;
- LINQ materialization;
- closures;
- boxing;
- delegate allocations;
- Task allocations.

Materialization is justified when ownership, snapshot semantics, random access, repeated traversal or isolation from mutable source is required.

## Ownership and lifetime

For every buffer-oriented API make clear:

- who creates the memory;
- who owns it;
- borrowed vs owned;
- pooled vs non-pooled;
- when it becomes invalid;
- who returns it to a pool;
- whether it survives callbacks/await.

Use `Span<T>`/`ReadOnlySpan<T>` for synchronous borrowed views; `Memory<T>`/`ReadOnlyMemory<T>` for memory that must survive asynchronous/lifetime boundaries.

Zero-copy is a goal, not a reason to make ownership unsafe.

## Stackalloc and pooling

Use `stackalloc` for small bounded temporary buffers.

Never derive an unbounded stack allocation size from external input.

For larger or variable buffers consider:

- `ArrayPool<T>`;
- `MemoryPool<T>`;
- `IBufferWriter<T>`;
- caller-provided destination.

Use a threshold or pool fallback when size is not tightly bounded.

## InlineArray

Do not treat `InlineArray` as an automatic replacement for fixed-size arrays.

Use it when:

- size is compile-time fixed;
- inline storage/value semantics are beneficial;
- avoiding a separate array object/indirection matters;
- accidental copying of the potentially large value type is controlled.

For immutable deterministic lookup tables, generated/static read-only data may be a better fit than an `InlineArray`.

Avoid passing large inline-array structs by value.

## Binary and bit operations

For binary parsing/writing prefer `BinaryPrimitives` where it expresses endianness and intent better than manual shifts/OR.

For compact flags/membership/windows consider `BitOperations`, masks and bit sets where natural.

## MemoryMarshal and CollectionsMarshal

Use `MemoryMarshal`/reinterpretation only when layout, managed-reference safety, alignment and endianness are understood.

Use `CollectionsMarshal` only in controlled hot paths where lifetime and structural modification are tightly controlled. It is not a default public API.
