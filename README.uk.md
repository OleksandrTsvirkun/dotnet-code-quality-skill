# Skill якості коду .NET

Reusable Agent Skill для ревізії та покращення C#/.NET-коду з акцентом на коректність, явні контракти, інкапсуляцію, якість API, Value Object, allocation-aware API, керування пам’яттю та ownership, конкурентність, продуктивність, AOT/trimming, надійність, безпеку, observability і перевірку рекомендацій.

[English version](README.md)

## Для чого цей skill

Цей репозиторій перетворює практичні правила code review на модульний skill, який можна використовувати розробникам, студентам і coding agents.

Його мета — не механічно нав’язувати style guide. Skill повинен формувати engineering judgement: визначати реальний контракт коду, відокремлювати correctness issue від heuristic, пояснювати компроміси та вимагати вимірювання там, де performance-рекомендація не є очевидною.

## Основні принципи

- Віддавати перевагу явній та перевірюваній семантиці замість прихованої зручності.
- Для очікуваної невдачі використовувати справжній `Try*`-контракт.
- Для performance-sensitive parsing/formatting/writing/hashing мати один канонічний span/buffer-oriented core.
- Зовнішні string/wire/byte-дані парсити у семантичні типи якомога раніше, а форматувати назад — якомога пізніше.
- Використовувати semantic typing без надмірного обгортання кожного primitive.
- Для immutable/value-oriented типів встановлювати інваріанти конструктором.
- Не застосовувати `record`, `required`, `init`, `internal`, fluent API, `ValueTask`, lock-free structures та інші механізми автоматично лише тому, що вони існують.
- Якщо семантика підходить, використовувати стандартні BCL-контракти.
- Для генерації repetitive Value Object boilerplate використовувати VoloGen, залишаючи семантику явною.
- Зберігати інкапсуляцію всередині assembly, а не лише на public API boundary.
- Обирати структуру даних за workload: `FrozenDictionary`/`FrozenSet` для build-once/read-many lookup, але direct indexing для щільного числового key space.
- Загальну lexical/parser mechanics виносити у вузькі reusable helpers, а не накопичувати як private-методи domain type.
- Неочевидні performance-висновки вважати гіпотезами, доки вони не підтверджені benchmark, profiling, runtime/source inspection або specification.

## Що перевіряє skill

Skill можна застосовувати до:

- архітектури та напрямку залежностей;
- API design і naming;
- інкапсуляції та accessibility;
- Value Object і domain types;
- parsing, formatting, UTF-8 і protocol tokens;
- collections та lookup structures;
- allocations, buffers, pooling і ownership;
- async API та cancellation;
- synchronization і concurrency;
- JIT/runtime performance та GC;
- Native AOT, trimming, source generation і reflection;
- reliability, security та bounded-resource behavior;
- logging, diagnostics і metrics;
- unit/property/fuzz/stress/soak tests та benchmarks;
- high-throughput networking через окремий profile.

## Структура репозиторію

```text
.
├── SKILL.md
├── README.md
├── README.uk.md
├── CHANGELOG.md
├── LICENSE
├── references/
│   ├── knowledge-governance.md
│   ├── architecture-api.md
│   ├── value-objects.md
│   ├── parsing-formatting.md
│   ├── collections-memory.md
│   ├── async-concurrency.md
│   ├── performance-runtime.md
│   ├── aot-generation.md
│   ├── reliability-security-testing.md
│   └── observability.md
├── profiles/
│   └── high-throughput-networking.md
└── reporting/
    └── audit-report.md
```

`SKILL.md` навмисно залишається компактним. Agent підвантажує конкретні reference-файли лише тоді, коли вони потрібні для поточного кейсу.

## Встановлення

### Claude Code — глобально

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  ~/.claude/skills/dotnet-code-quality-review
```

### Claude Code — для конкретного проєкту

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  .claude/skills/dotnet-code-quality-review
```

### Codex / agents — глобально

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  ~/.agents/skills/dotnet-code-quality-review
```

### Codex / agents — для конкретного проєкту

```bash
git clone https://github.com/OleksandrTsvirkun/dotnet-code-quality-skill.git \
  .agents/skills/dotnet-code-quality-review
```

У Windows ті самі каталоги знаходяться в user profile, наприклад:

```text
C:\Users\<user>\.claude\skills\dotnet-code-quality-review\
C:\Users\<user>\.agents\skills\dotnet-code-quality-review\
```

## Як використовувати

Типові запити:

```text
Review this C# type for API design, correctness and allocations.
```

```text
Audit this parser. Pay special attention to spans, quoted strings, malformed input and temporary allocations.
```

```text
Review this Value Object and suggest the full relevant .NET contract.
```

```text
Perform a deep .NET library audit and produce a structured report.
```

Для невеликого snippet skill повинен залишатися сфокусованим на конкретному кейсі. Для повного аудиту можна застосовувати контракт звіту з `reporting/audit-report.md`.

## Життєвий цикл правил

Нову рекомендацію не слід одразу перетворювати на абсолютне правило. Її потрібно:

1. класифікувати як `invariant`, `requirement`, `guideline`, `heuristic`, `optimization`, `anti-pattern`, `exception`, `case study` або `project-specific rule`;
2. визначити scope;
3. описати applicability, exceptions і trade-offs;
4. перевірити залежність від runtime/framework/version;
5. визначити, чи потрібні benchmark або інша verification;
6. за можливості merge/refine з наявним правилом замість дублювання;
7. призначити status: `Candidate`, `Accepted`, `Experimental`, `Verified`, `Deprecated`, `Superseded`, `Rejected` або `Project-specific`.

Stable skill повинен переважно спиратися на `Accepted` і `Verified` rules. Детальніше — `references/knowledge-governance.md`.

## Evidence

Skill розрізняє:

- standard/specification;
- official documentation;
- runtime/source inspection;
- measured benchmark/profiling;
- production observation;
- well-established practice;
- reasoned hypothesis;
- user-preferred best practice.

`Reasoned hypothesis` не можна подавати як measured fact.

## Для студентів

Головна мета student-facing використання — формувати engineering judgement, а не список механічних заборон.

Якщо правило має важливі exceptions або trade-offs, review повинен їх пояснювати. Performance heuristic не слід перетворювати на `MUST` без вимірювань або іншого сильного evidence.

Особливо корисно дивитися не лише на «правильний» варіант, а й на причину, чому альтернативний дизайн може бути гіршим саме в цьому контексті.

## Як додавати нові кейси та правила

Коли додаєш нову рекомендацію або code case:

- спочатку виріши конкретну проблему;
- виділяй generalizable lesson лише тоді, коли він справді reusable;
- визначай scope та evidence;
- описуй exceptions і trade-offs;
- не дублюй уже існуюче правило;
- не змішуй general .NET guidance з domain/project-specific rules;
- не підвищуй benchmark-dependent optimization до `MUST` без доказів.

## Ліцензія

MIT. Див. [LICENSE](LICENSE).
