# TAGAME — Task Index

This file is the central task board. Detailed task specifications live under `tasks/backlog/` or `tasks/active/`. Completed work is summarized in `tasks/archive/TASK_ARCHIVE.md`.

## Status values

- `ready` — eligible to be selected when dependencies are satisfied.
- `in_progress` — assigned to an agent and currently being worked on.
- `blocked` — cannot continue without a decision, input, or external change.
- `done` — completed and archived.
- `cancelled` — intentionally not being implemented.

## Selection rules

Tasks are selected by area prefix and numeric order. When the user says “pick a frontend task”, select the first eligible `F` task; when the user says “pick a backend task”, select the first eligible `B` task. Dependencies and ownership must be checked before selection.

## Current queue

| ID | Area | Title | Status | Owner | Dependencies |
|---|---|---|---|---|---|
| F001 | Frontend | Create admin panel | ready | — | UX001, UI001, B001 (done) |
| OPS001 | DevOps | Create CasaOS application stack | ready | — | B001 (done) |
| M001 | Mobile | Integrate Android NFC reader flow | ready | — | B001 (done) |
| M002 | Mobile | Integrate iOS NFC shortcut flow | ready | — | B001 (done) |
| PC001 | PC Agent | Create C#/.NET tray agent | ready | — | B001 (done) |
| QA001 | QA | Test synthetic UUID launch flow | blocked | — | B001 (done), PC001 (pending) |

## Recently completed

| ID | Area | Title | Status | Owner |
|---|---|---|---|---|
| B001 | Backend | Create API and database foundation | done | Codex |

## Task lifecycle

1. A ready task specification starts in `tasks/backlog/`.
2. An agent claims it by moving it to `tasks/active/`, setting `in_progress`, and recording ownership.
3. Progress is tracked in `.agent/CURRENT_TASK.md`.
4. On completion, the task is summarized in `tasks/archive/TASK_ARCHIVE.md` and removed from `tasks/active/`.
