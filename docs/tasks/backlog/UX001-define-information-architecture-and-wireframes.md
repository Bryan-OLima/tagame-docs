# UX001 — Define Information Architecture and Low-Fidelity Wireframes

Status: ready
Area: UX
Owner: —
Dependencies: none

## Objective

Define how users understand and navigate TAGAME before frontend implementation. Produce an approved information architecture, screen inventory, primary user flows, and low-fidelity wireframes based on the existing product, database, architecture, and business-rule documents.

## Required inputs

- `PROJECT_VISION.md`
- `ARCHITECTURE.md`
- `DB.md`
- `RULES.md`
- `CURRENT_STATE.md`
- `ROADMAP.md`
- Existing `UX.md`, which contains the approved initial page inventory and must be refined rather than discarded.

## Scope

- Define the navigation hierarchy for authenticated `admin` and `guest` users.
- Group database entities by user goal instead of creating one navigation item per table.
- Define the dashboard, library, devices, activity, administration, onboarding, and error-recovery areas.
- Describe the primary flows for first admin setup, game registration, NTAG213 registration, tag assignment, reader-device pairing, PC-agent pairing, start, stop, offline-agent handling, and history review.
- Define loading, empty, success, validation-error, permission-denied, offline, timeout, and unexpected-error states.
- Produce responsive low-fidelity wireframes for the CasaOS web application.
- Produce small low-fidelity wireframes for the C# agent setup window, connection-status window, and tray menu.
- Identify unresolved UX decisions without inventing new business rules.

## Out of scope

- Final colors, typography, icons, illustrations, branding, or visual identity.
- Production frontend code.
- Choosing a frontend framework or component library.
- Changing database or authorization rules to simplify a screen.
- Designing native Android or iOS applications beyond documenting the reader feedback required by the current flow.

## Deliverables

- Updated `docs/UX.md` containing the reviewed information architecture, role visibility, screen inventory, user flows, state definitions, decisions, and rationale.
- `docs/wireframes/WEBAPP.md` containing desktop and mobile low-fidelity webapp wireframes.
- `docs/wireframes/PC_AGENT.md` containing low-fidelity agent setup and tray wireframes.
- A list of open UX questions requiring owner approval.

## Allowed files

- `docs/UX.md`
- `docs/wireframes/WEBAPP.md`
- `docs/wireframes/PC_AGENT.md`
- `docs/CURRENT_STATE.md` only for decisions explicitly approved during this task.
- This task file, `docs/TASKS.md`, `.agent/CURRENT_TASK.md`, and the task archive as required by the task workflow.

## Acceptance criteria

- Every primary database concept is reachable through a user-oriented screen or flow.
- Admin-only and guest-visible areas are clearly distinguished.
- The start/stop command lifecycle and the game-session lifecycle are represented separately.
- Offline, timeout, rejected, and error states are visible and understandable.
- The webapp wireframes cover desktop and narrow/mobile widths.
- The PC-agent wireframes cover first setup, connected/disconnected status, tray behavior, and exit/reconnect actions.
- Wireframes remain low fidelity and do not prematurely define visual branding.
- No wireframe permits arbitrary commands, raw token disclosure, or a client-supplied Steam AppID.

## Verification

- Trace every flow against `RULES.md` and `ARCHITECTURE.md`.
- Trace every displayed entity or status against `DB.md`.
- Confirm all deliverables exist and all open questions are explicitly listed.
- Review the result with the project owner before marking the task complete.
