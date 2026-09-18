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
| UX001 | UX | Define information architecture and low-fidelity wireframes | ready | — | — |
| UI001 | UI | Define visual identity and design system | blocked | — | UX001 |
| F001 | Frontend | Create admin panel | blocked | — | UX001, UI001, B001 |
| B001 | Backend | Create API and database foundation | ready | — | — |
| DB001 | Database | Create initial migrations | ready | — | — |
| OPS001 | DevOps | Create CasaOS application stack | blocked | — | B001, DB001 |
| M001 | Mobile | Integrate Android NFC reader flow | blocked | — | B001 |
| M002 | Mobile | Integrate iOS NFC shortcut flow | blocked | — | B001 |
| PC001 | PC Agent | Create C#/.NET tray agent | blocked | — | B001 |
| QA001 | QA | Test synthetic UUID launch flow | blocked | — | B001, PC001 |

## Task lifecycle

1. A ready task specification starts in `tasks/backlog/`.
2. An agent claims it by moving it to `tasks/active/`, setting `in_progress`, and recording ownership.
3. Progress is tracked in `.agent/CURRENT_TASK.md`.
4. On completion, the task is summarized in `tasks/archive/TASK_ARCHIVE.md` and removed from `tasks/active/`.
