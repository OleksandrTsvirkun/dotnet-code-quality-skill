# Async and Concurrency

## Async as a contract

For each async API ask:

- is the operation actually asynchronous?
- is it I/O-bound?
- does it often complete synchronously?
- does it allocate a Task/state machine?
- is cancellation correct?
- is there sync-over-async?
- is `Task.Run` wrapping async I/O?
- is backpressure defined?

Do not make CPU-only helpers async for uniformity alone.

## Task vs ValueTask

Default to `Task`/`Task<T>`.

Use `ValueTask`/`ValueTask<T>` as a performance optimization when:

- the method is hot;
- synchronous completion is common;
- Task allocation is measurable;
- consumer semantics remain simple.

Check for misuse: multiple awaits, repeated `AsTask`, storage for later consumption, concurrent consumption.

Use `IValueTaskSource<T>` only for extreme hot paths with strict lifecycle and tests.

## Cancellation

Do not create `CancellationTokenSource`, linked CTS or `CancelAfter` per tiny operation without need.

Consider session cancellation, deadlines, central scheduling and `TimeProvider`.

## Synchronization

Do not hold a synchronous lock across `await`.

Choose primitives from semantics and workload, considering:

- read/write ratio;
- critical-section duration;
- async boundaries;
- fairness assumptions;
- reentrancy;
- contention;
- single-owner alternatives.

Candidates include:

- `System.Threading.Lock`;
- `ReaderWriterLockSlim`;
- `SemaphoreSlim`;
- `Interlocked`;
- `Volatile`;
- concurrent collections;
- `Channel<T>`;
- events/barriers/countdown primitives;
- specialized lock-free structures where justified.

Do not model a multi-field invariant as independent atomics without a correctness proof.

## Channels and queues

Use bounded channels/queues when producer rate can exceed consumer rate.

Define:

- capacity;
- overflow/drop policy;
- producer throttling;
- slow-consumer behavior.

No externally driven queue should grow without a bound/budget.

## Races and reentrancy

Look for:

- lock inversion;
- callbacks under lock;
- check-then-act races;
- dispose races;
- timer-after-dispose;
- send-after-close;
- double initialization;
- double completion;
- double release;
- synchronous callback reentrancy.

Move state to a valid state before invoking external callbacks.

For `TaskCompletionSource`/custom awaitables, consider `RunContinuationsAsynchronously` when synchronous continuation execution could run inside a critical section.

## Lock-free and spin

Use spin/lock-free structures only for clearly justified low-level hot paths.

Verify:

- ABA;
- memory ordering;
- false sharing;
- starvation/progress;
- bounded memory;
- CPU cost.

Do not use unbounded busy spin.
