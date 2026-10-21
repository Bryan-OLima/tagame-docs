# B001 — Create API and Database Foundation

Status: ready
Area: backend
Owner: —
Dependencies: none

## Objective

Create the initial executable TAGAME backend and the complete first SQLite migration set for users, devices, PCs, agents, games, tags, assignments, sessions, events, and audit logs. This task is the single owner of the initial backend and database foundation; it incorporates the former `DB001` scope.

## Required inputs

- `ARCHITECTURE.md`
- `DB.md`
- `RULES.md`
- `CURRENT_STATE.md`
- The approved portions of `API.md`; endpoints still marked as draft must not be treated as approved without project-owner confirmation.

## Scope

- Configure the backend against the approved SQLite database engine.
- Create the first versioned migration set described in `DB.md`.
- Create all documented tables, UUID primary keys, foreign keys, unique constraints, lifecycle fields, and active-assignment integrity constraints.
- Represent unresolved rejected attempts using nullable relationships and safe fingerprints exactly as defined in `DB.md` and `RULES.md`.
- Configure SQLite WAL mode, foreign-key enforcement for every connection, and a write busy timeout.
- Add application liveness and database-readiness endpoints.
- Add configuration loading without committing secrets.
- Document clean setup, migration, development start, verification, and database recovery procedures.

## Out of scope

- Implementing API endpoints from `API.md` that have not yet received explicit approval, except the approved liveness/readiness foundation required by this task.
- Frontend screens.
- NFC reader integrations.
- Steam launch execution or the Windows PC agent.
- Production deployment to CasaOS; that belongs to `OPS001`.

## Acceptance criteria

- The backend starts locally using documented steps.
- The database can be created from a clean environment.
- SQLite uses WAL mode, foreign-key enforcement, and a write busy timeout.
- Every required table, UUID key, foreign key, unique constraint, lifecycle field, and active-assignment rule from `DB.md` is represented.
- Rejected attempts can be stored when tag, device, assignment, or action resolution fails, without persisting raw credentials or unrestricted caller input.
- The liveness endpoint reports application health independently from the readiness endpoint, which verifies database availability.
- Migrations can be applied to a clean database and have either a tested rollback or a documented safe recovery procedure.
- No secrets or personal data are present in source-controlled configuration or fixtures.

## Verification

1. Install dependencies from a clean checkout using the documented command.
2. Apply every migration against a new SQLite database.
3. Inspect the resulting schema and constraints against `DB.md`.
4. Verify WAL mode, foreign-key enforcement, and busy timeout on active connections.
5. Exercise storage of a rejected attempt with unresolved nullable relationships and safe fingerprints.
6. Verify liveness and readiness behavior with the database available and unavailable.
7. Execute the documented rollback or recovery procedure.
8. Run backend formatting, type-checking, linting, and automated tests.
