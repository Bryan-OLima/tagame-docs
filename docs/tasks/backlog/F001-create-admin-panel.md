# F001 — Create Admin Panel

Status: blocked
Area: frontend
Owner: —
Dependencies: UX001, UI001, B001

## Objective

Create the initial authenticated web layout for TAGAME administration.

## Scope

- Add login and authenticated shell placeholders.
- Add navigation for users, devices, tags, games, PCs, sessions, and audit logs.
- Do not implement business logic in the frontend.

## Acceptance criteria

- An authenticated user can reach the admin shell.
- Navigation reflects the documented entities.
- Admin and guest visibility rules are represented without exposing secrets.
- The UI works with API loading, empty, and error states.

## Verification

Run the frontend checks and manually verify the authenticated and guest navigation states.
