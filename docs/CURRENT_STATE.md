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
- The information architecture, role visibility, primary user flows, responsive behavior, and low-fidelity webapp and PC-agent wireframes are approved under `UX001`.
- First-time setup creates only the initial administrator and continues to normal Login after success.
- Initial-administrator recovery uses a secret recovery code displayed once, with local CasaOS physical-access recovery as fallback; implementation security details remain to be specified.
- Guests may see the PC name and safe event details only for their own permitted activity, and see only a summary of their own reader devices.
- Complex creation, pairing, assignment, and recovery flows use full pages; simple edits may use desktop drawers; mobile uses full pages.
- Reader feedback separates acknowledgement from final game-session state.
- Database restoration requires administrator reauthentication, a pre-restore backup, an impact summary, and typed confirmation.
- Changing the PC agent's server address requires re-pairing, and its tray icon distinguishes online-idle from game-running state.
- The visual system is approved under `UI001`: a dark-only cyberpunk-operational theme that favors accessible operational clarity over literal game-HUD imitation.
- The approved visual type roles are Oxanium for display, Inter for functional UI, and JetBrains Mono for technical data; frontend font assets will be self-hosted.
- The normalized frontend design-token source is `tagame-server/frontend/src/styles/tokens.css`; components must consume semantic tokens rather than literal visual values.

## Not yet decided

- Password hashing and the detailed JWT lifecycle, including refresh-token policy, token storage in the Angular client, key management, and revocation strategy.
- Exact Android integration: Tasker/Termux or a native reader app.
- Exact iOS integration: Shortcuts or a native reader app.
- Agent transport: WebSocket, polling, or HTTP callback. SSH remains a temporary development option only.
- Exact graceful-stop implementation per game.
- Single-PC versus multi-PC default selection behavior.

## Next recommended milestone

Build a documentation-only prototype contract: define the first API request/response shapes, create synthetic database fixtures, and test the full flow with a manually supplied tag UUID before purchasing NFC tags.
