# Logging, Diagnostics and Metrics

## Logging

Use structured logging through standard abstractions.

Review:

- level choice;
- disabled-log allocation;
- source-generated logging where useful;
- correlation;
- sensitive data/redaction;
- log injection;
- formatting before level checks.

Do not log high-frequency hot-path events at `Information` by default.

## Diagnostics

Use the appropriate standard mechanisms without binding core code to one monitoring vendor:

- `EventSource`;
- `DiagnosticSource`;
- `Activity`;
- `Meter`;
- OpenTelemetry adapters.

Useful internal performance counters can include:

- fast-path hits;
- slow-path hits;
- pool misses;
- fallback allocations;
- buffer growth;
- queue saturation;
- sync vs async completion;
- spin-to-block transitions;
- contention;
- dropped work.

Diagnostics themselves must not become the hot-path bottleneck.
