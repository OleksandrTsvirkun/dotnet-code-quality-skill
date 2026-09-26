# AOT, Trimming, Source Generation and Interop

## Native AOT / trimming

For reusable libraries, prefer designs friendly to:

- trimming;
- Native AOT;
- source generation.

When applicable, verify with current SDK analyzers and publish smoke tests rather than relying on assumptions.

## Reflection

Review runtime use of:

- `Assembly.GetTypes`;
- `Type.GetType`;
- `GetMethod`;
- `Activator.CreateInstance`;
- `MethodInfo.Invoke`;
- attribute scanning;
- dynamic assembly loading.

If mappings are known at compile time, prefer explicit or generated registration.

If reflection is unavoidable, use the appropriate trimming/dynamic-code annotations and do not silence meaningful warnings indiscriminately.

## Runtime code generation

Avoid building core functionality around:

- `Reflection.Emit`;
- `DynamicMethod`;
- runtime IL generation;
- dynamic proxies;

unless runtime codegen is truly a requirement.

## Source generation

Prefer source generation when it removes repeated runtime discovery/reflection or repetitive API boilerplate.

Good uses include:

- generated registries;
- serializer metadata;
- logging;
- regex;
- value-object boilerplate through VoloGen;
- static lookup data.

Generated code should keep semantics explicit and reviewable.

## Native interop

For P/Invoke review:

- `LibraryImport` where appropriate;
- blittability;
- layout;
- calling convention;
- `SafeHandle`;
- callback lifetime;
- marshalling;
- pinning/lifetime.

## Generic design

Balance generic specialization/devirtualization/boxing avoidance against AOT native code-size growth. Avoid combinatorial generic explosion.
