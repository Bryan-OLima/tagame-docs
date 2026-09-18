# DB001 — Create Initial Migrations

Status: ready
Area: database
Owner: —
Dependencies: none

## Objective

Implement the first database migration set described in `DB.md`.

## Scope

- Create tables, UUID primary keys, foreign keys, unique constraints, and lifecycle fields.
- Add the active-assignment integrity constraint.
- Use UTC timestamps and English `snake_case` field names.
- Target SQLite and store UUID values as canonical `TEXT`.

## Acceptance criteria

- A clean database can be migrated up successfully.
- The schema matches `DB.md`.
- A migration rollback or documented recovery path exists.
- No secrets or personal data are included in fixtures.

## Verification

Run migration up/down checks and inspect the resulting schema.
