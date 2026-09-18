# TAGAME — Roadmap

## Phase 0 — Documentation and decisions

- [x] Define project vision.
- [x] Define database entities and relationships.
- [x] Define business rules and roles.
- [x] Define initial architecture.
- [x] Define commit conventions.
- [ ] Define information architecture and low-fidelity wireframes (`UX001`).
- [ ] Define visual identity and design system (`UI001`).
- [ ] Record the remaining technology decisions.

## Phase 1 — Local proof of concept

- [ ] Create the application skeleton for CasaOS.
- [ ] Create database migrations for users, devices, PCs, games, tags, assignments, sessions, events, and audit logs.
- [ ] Implement admin login and guest authorization.
- [ ] Implement tag/game/PC CRUD in the web UI.
- [ ] Add a manual API request accepting a synthetic tag UUID.
- [ ] Create a C#/.NET development PC agent with a Windows Forms/`NotifyIcon` tray UI that logs start/stop events without launching games.
- [ ] Test the complete flow without a physical NFC tag.

## Phase 2 — Steam execution

- [ ] Register a real PC agent.
- [ ] Implement allowlisted Steam launch actions.
- [ ] Implement agent heartbeat and process-state reporting.
- [ ] Implement start/stop session transitions and timeouts.
- [ ] Add audit and error views.

## Phase 3 — Reader integrations

- [ ] Integrate Android NFC reading.
- [ ] Integrate iOS NFC automation or reader app.
- [ ] Pair and revoke reader devices.
- [ ] Test multiple authorized devices.
- [ ] Purchase and program genuine NTAG213 physical tags.

## Phase 4 — Hardening

- [ ] Add rate limiting and duplicate-tap protection.
- [ ] Add backup and restore for the database.
- [ ] Add migrations and rollback guidance.
- [ ] Validate local-network firewall behavior.
- [ ] Add automated tests for authorization, assignments, sessions, and agent events.

## Phase 5 — Extensions

- [ ] Multiple PCs and per-tag target selection.
- [ ] Support for non-Steam applications.
- [ ] Guest-specific tag permissions.
- [ ] Richer dashboards and launch history.
- [ ] Optional VPN-based remote access.
