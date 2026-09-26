# High-Throughput Networking Profile

Apply this profile only to network/media/high-throughput libraries or code paths where these concerns are relevant.

## Pipelines

Do not make `System.IO.Pipelines` the fundamental public/core abstraction automatically.

Prefer architecture such as:

```text
Core semantics
    <- reader/transport abstraction
    <- Socket / Stream / Pipelines adapters
```

Pipelines are natural for:

- stream framing;
- incremental parsing;
- segmented input;
- proxies/gateways.

Do not force Pipelines onto naturally datagram-oriented flows where packets are already contiguous/owned.

When Pipelines are used, verify `AdvanceTo`, partial messages, multi-segment input, cancellation/completion and writer flush semantics.

## Socket backend

For high-throughput socket code compare representative workloads using:

- Task/ValueTask socket APIs;
- `SocketAsyncEventArgs`;
- batching/vectored I/O where available.

If using `SocketAsyncEventArgs`:

- reuse/pool operation contexts;
- define buffer ownership;
- handle synchronous completion/reentrancy;
- consider ExecutionContext costs.

## Scatter/gather and copies

Do not concatenate header + payload + trailer into a new buffer when the transport supports vectored/scatter-gather writes and ownership/lifetime allow it.

Classify every copy:

- required by ownership/lifetime;
- justified;
- avoidable;
- critical hot-path copy.

## Batch APIs

Consider batch send/receive/read APIs where they reduce:

- syscalls;
- scheduler overhead;
- contention.

## System-call budget

Count:

- send/receive;
- flush;
- timer registration;
- kernel waits;
- wakeups;
- context switches.

Managed allocations are only part of the cost model.

## Queues and timers

Do not use `List<T>` as a universal queue/priority/timer/history structure.

Consider:

- queues/ring buffers;
- `PriorityQueue`;
- SPSC/MPSC;
- bounded channels;
- timer wheels for very large deadline populations.

Do not create a timer/`CancelAfter` per tiny operation without examining scale.

## Fast path

Common valid packet/frame processing should be short.

Move malformed input, extensions, diagnostics, fallback and multi-segment handling into clear slow paths when appropriate.

## Bounded networking state

Network-controlled queues, fragments, pending operations, headers, attributes and sessions require explicit limits/budgets.
