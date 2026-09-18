# TAGAME — Project Vision

## Purpose

TAGAME is a self-hosted, local-first NFC game launcher. A user taps a physical NFC tag with an authorized Android or iOS device, TAGAME resolves the tag to a configured game, and a lightweight agent on the selected PC launches or stops that game through Steam.

The central application, database, administration panel, API, event queue, and audit history run on the user's CasaOS server. The phone is a reader and API client; the PC agent is an execution bridge.

## Core flow

```text
NFC tag
  -> Android/iOS reader
  -> TAGAME API on CasaOS
  -> tag and device validation
  -> tag-to-game resolution
  -> PC agent event
  -> Steam launch/stop action
  -> session state and audit log
```

The NFC tag stores an opaque UUID token. It must not store shell commands, credentials, or a Steam command. Changing the game associated with a tag is performed in the TAGAME panel and does not require rewriting the tag.

## Product goals

- Make launching frequently played Steam games feel physical and immediate.
- Keep the source of truth for users, devices, tags, games, PCs, and sessions in CasaOS.
- Support multiple authorized Android and iOS readers.
- Allow an administrator to manage guests, associations, PCs, and devices.
- Support a second tap as a start/stop toggle when the same game's session is active.
- Preserve an auditable history of requests, outcomes, and errors.
- Keep the system local-first: no public internet exposure is required.

## Explicit non-goals

- The NFC tag does not execute code.
- TAGAME does not need to be CasaOS itself; it is an application hosted by CasaOS.
- The CasaOS container cannot directly launch a game on another machine. The PC agent or an equivalent remote execution bridge is required.
- A UUID is an identifier, not proof of authenticity. Device authentication and API authorization are still required.

## Planned components

1. **Web application** — administration and user-facing interface.
2. **API** — authenticated local endpoints for readers, administration, agents, sessions, and logs.
3. **Database** — relational source of truth described in `DB.md`.
4. **Event dispatcher** — delivers validated launch/stop events to a selected PC agent.
5. **PC agent** — C#/.NET Windows application with a small setup window and tray interface that receives allowed events, reports state, and invokes Steam.
6. **Reader integrations** — Android automation/app integration and iOS Shortcuts or a future native reader app.

## Vocabulary

- **Tag**: a physical NFC tag identified by the UUID stored in its NDEF payload.
- **Device**: an authorized phone, tablet, or web client that can request actions.
- **PC**: a computer registered as a possible game execution target.
- **Game**: a configured Steam title. `steam_appid` is the external Steam identifier.
- **Session**: the tracked lifecycle of one game execution on one PC.
- **Agent**: the non-AI background bridge running on a PC.
