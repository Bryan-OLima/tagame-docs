# TAGAME — Commit Conventions

TAGAME uses the semantic intent of [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/#specification), adapted to require an uppercase bracketed type at the beginning of every commit message.

## Required format

```text
[TYPE] short imperative description
```

The type is uppercase and enclosed in square brackets. The description is concise, imperative, and written in English.

Examples:

```text
[FEAT] add device pairing endpoint
[FIX] reject revoked reader tokens
[DOCS] document game session lifecycle
[REFACTOR] isolate Steam command adapter
[TEST] cover repeated NFC tap behavior
[CHORE] update local development dependencies
```

## Allowed types

| Type | Use |
|---|---|
| `FEAT` | Adds a user-visible or system capability |
| `FIX` | Corrects a defect |
| `DOCS` | Changes documentation only |
| `REFACTOR` | Changes structure without changing intended behavior |
| `TEST` | Adds or changes tests |
| `PERF` | Improves performance without changing intended behavior |
| `STYLE` | Formatting or style-only changes |
| `CHORE` | Maintenance that does not fit another type |
| `BUILD` | Build tooling or dependency changes |
| `CI` | Continuous integration changes |
| `REVERT` | Reverts a previous change |
| `BREAKING` | Introduces an intentional incompatible change |

## Rules

1. Every commit MUST begin with one allowed bracketed type.
2. The description MUST be present after the type and a space.
3. Commit one logical change whenever practical.
4. Do not include credentials, raw device tokens, personal IP addresses, or private logs.
5. A breaking change MUST use `[BREAKING]` and explain the migration in the commit body.
6. Optional body and footer paragraphs follow the description after one blank line.
7. Scope may be included after the type when useful, for example `[FEAT] [API] add device pairing endpoint`.

## Breaking-change example

```text
[BREAKING] rename tags.token to tags.public_token

Update the database migration and all reader integrations before deploying.
```

The bracketed prefix is a TAGAME project convention. It intentionally improves readability for humans while preserving the semantic meanings of Conventional Commits types.

