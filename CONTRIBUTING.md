# Contributing

Contributions should preserve the skill's evidence-driven, non-dogmatic approach.

## Git Flow

This repository uses Git Flow:

- `main` contains released versions;
- `develop` contains integrated work for the next release;
- `feature/*` branches are created from `develop` and merged back into `develop`;
- `release/*` branches are created from `develop`, finalized, merged into `main`, and then merged back into `develop`;
- `hotfix/*` branches are created from `main` for urgent released-version fixes and merged into both `main` and `develop`.

Do not commit normal development directly to `main`.

## Adding a rule

Before adding a reusable rule:

1. classify its type and scope;
2. identify evidence;
3. document applicability, exceptions and trade-offs;
4. check for overlap or conflict with existing guidance;
5. mark benchmark-dependent performance advice as `MEASURE FIRST` unless strong evidence justifies a stronger obligation;
6. keep project/domain-specific rules out of the general skill unless they are intentionally placed in a specialized profile.

See `references/knowledge-governance.md`.

## Language

Normative skill content and reference files are written in English.

`README.md` is the default English documentation. `README.uk.md` is the Ukrainian alternative intended in particular for students.

## Pull requests

Keep pull requests focused. Describe:

- what rule or behavior changes;
- why;
- evidence level;
- affected scope/profile;
- whether the change supersedes or narrows an existing rule;
- whether verification or benchmarking is required.
