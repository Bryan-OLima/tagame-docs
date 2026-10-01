# TAGAME — Current State

## Status

**Phase:** product definition and documentation.

No production application, database migration, reader integration, or PC agent has been implemented yet.

## Approved decisions

- The project is named TAGAME.
- CasaOS is the local host for the web application, API, database, dispatcher, and logs.
- Android and iOS devices act as NFC readers and authenticated API clients.
- NFC tags store opaque UUID tokens rather than commands or Steam AppIDs.
- Internal database IDs use UUIDs; `steam_appid` remains an external integer.
- Users have `admin` or `guest` roles.
- Devices and PC agents use revocable tokens stored as hashes in the database.
- The PC agent is a small background execution bridge, not an AI agent.
- The PC agent implementation direction is C#/.NET for Windows.
- The initial agent UI will use Windows Forms with `NotifyIcon` and start with the user's Windows session.
- Command delivery and game process state use separate lifecycles; an agent acknowledgement does not mean the game is already running.
- A second tap may stop the same active game; runtime state is tracked with sessions and agent confirmation.
- The system is local-first and does not require public exposure.
- The initial database engine is SQLite.
- SQLite will use WAL mode, foreign-key enforcement, a write busy timeout, and versioned migrations.
- The backend will use Express with TypeScript, starting from the project's generic `ExpressTsScaffolding` repository.
- Zod will validate untrusted API input and provide inferred TypeScript types at API boundaries.
- Prisma will provide the typed database access layer and migration tooling for SQLite.
- The web frontend will use Angular.
- The web frontend will authenticate users with short-lived JWT Bearer access tokens.
- Reader devices and PC agents will use separate revocable credentials; they will not use the web user's JWT.
- The reader and PC agent will communicate through the CasaOS API; the reader will not communicate directly with the agent.
- Internal UUIDs will be stored as `TEXT`; `steam_appid` remains an integer external identifier.

## Not yet decided

- Password hashing and the detailed JWT lifecycle, including refresh-token policy, token storage in the Angular client, key management, and revocation strategy.
- Exact Android integration: Tasker/Termux or a native reader app.
- Exact iOS integration: Shortcuts or a native reader app.
- Agent transport: WebSocket, polling, or HTTP callback. SSH remains a temporary development option only.
- Exact graceful-stop implementation per game.
- Single-PC versus multi-PC default selection behavior.

## Next recommended milestone

Build a documentation-only prototype contract: define the first API request/response shapes, create synthetic database fixtures, and test the full flow with a manually supplied tag UUID before purchasing NFC tags.
