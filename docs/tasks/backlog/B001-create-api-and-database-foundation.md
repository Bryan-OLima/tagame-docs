# B001 — Create API and Database Foundation

Status: ready
Area: backend
Owner: —
Dependencies: none

## Objective

Create the initial TAGAME backend structure and database foundation for users, devices, PCs, games, tags, assignments, sessions, events, and audit logs.

## Scope

- Configure the backend against the approved SQLite database engine.
- Add the initial migration structure based on `DB.md`.
- Add a health endpoint.
- Add configuration loading without committing secrets.

## Acceptance criteria

- The backend starts locally using documented steps.
- The database can be created from a clean environment.
- SQLite uses WAL mode, foreign-key enforcement, and a write busy timeout.
- Required tables and constraints from `DB.md` are represented.
- The health endpoint reports application and database readiness.

## Verification

Run the documented development start command and apply migrations against a clean database.
