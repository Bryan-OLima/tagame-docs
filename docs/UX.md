# TAGAME — User Experience Specification

Status: initial information architecture approved; low-fidelity wireframes pending under `UX001`.

## Purpose

This document defines how people understand, navigate, and operate TAGAME. It translates the product, database, architecture, and business rules into user-facing areas and flows without exposing internal tables as the primary navigation model.

The web application is hosted on the CasaOS notebook. Android and iOS devices act as NFC readers. The C#/.NET tray agent runs on the Steam PC.

## UX principles

1. Organize the application around user goals, not database tables.
2. Make system state visible: server, reader, agent, command, and game-session states must not be confused.
3. Keep dangerous or administrative actions explicit and role-restricted.
4. Never display raw device tokens, agent tokens, password hashes, or arbitrary execution commands.
5. Make recovery actions available next to failures where practical.
6. Prefer guided pairing and tag-assignment flows over technical forms.
7. Keep the guest experience smaller than the administrator experience.
8. Support desktop and narrow/mobile web layouts.

## Roles

### Administrator

Administrators can access the complete product: configuration, users, devices, tags, games, PCs, activity, audit history, backups, and system diagnostics.

### Guest

Guests can use authorized reader devices and view only the status and history explicitly allowed by the business rules. They cannot manage users, roles, tags, games, PCs, assignments, or credentials.

## Primary navigation

```text
Dashboard

Library
├── Games
└── Tags

Devices
├── Readers
└── PCs

Activity
├── Sessions
└── Events

Administration
├── Users
└── Audit logs

Settings
Profile
```

The assignment relationship is managed through the tag experience as `tag -> game -> PC`; it is not exposed as a technical `tag_assignments` navigation item.

## Page inventory

| Area | Page | Admin | Guest | Purpose |
|---|---|---:|---:|---|
| Access | First-time setup | Yes | No | Create the initial administrator and initialize TAGAME |
| Access | Login | Yes | Yes | Authenticate a user |
| Access | Account recovery | Yes | Limited | Recover or administratively reset access |
| Onboarding | Getting started | Yes | No | Complete the initial operational checklist |
| Dashboard | Dashboard | Full | Limited | Show current system, agent, game, and activity state |
| Library | Games | Yes | No | List and manage configured Steam games |
| Library | Game details | Yes | No | Inspect a game, associations, PCs, and recent sessions |
| Library | Tags | Yes | No | List and manage NTAG213 identifiers and assignments |
| Library | Tag setup | Yes | No | Generate/register a UUID and assign game and PC |
| Devices | Readers | Yes | Own summary only | List paired Android/iOS reader devices |
| Devices | Pair reader | Yes | No | Pair a phone with a temporary credential flow |
| Devices | PCs | Yes | No | List PCs and agent health |
| Devices | PC details | Yes | No | Inspect agent state, sessions, errors, and pairing |
| Devices | Pair agent | Yes | No | Pair the C# agent to a registered PC |
| Activity | Sessions | Yes | Own/allowed | Show game process lifecycle |
| Activity | Events | Yes | Own/allowed | Show start/stop request history |
| Activity | Event details | Yes | Own/allowed | Show a diagnostic event timeline |
| Administration | Users | Yes | No | Manage accounts, roles, and status |
| Administration | User details | Yes | Self only via Profile | Inspect role, devices, and activity |
| Administration | Audit logs | Yes | No | Review administrative and security-sensitive changes |
| Settings | Settings | Yes | No | Configure system, security, backups, logs, and diagnostics |
| Account | Profile | Yes | Yes | Manage personal account data and sessions |
| System | Access denied | Yes | Yes | Explain insufficient permission |
| System | Not found | Yes | Yes | Handle invalid routes or missing records |
| System | Server unavailable | Yes | Yes | Explain API or CasaOS connection loss |
| System | Unexpected error | Yes | Yes | Present a safe error and recovery action |

## Access and onboarding

### First-time setup

Shown only when no administrator exists. It creates the initial admin account and verifies that the local server can persist data. The page must not allow creation of a second initial admin after setup is complete.

### Login

Accepts the user's email and password. It should show invalid-credential, inactive-account, rate-limit, and server-unavailable states without revealing whether a specific account exists.

### Account recovery

Because TAGAME is local-first, recovery must not assume an external email provider. The exact recovery mechanism remains an open decision. Administrators may reset guest access, but an initial-admin recovery strategy must be approved separately.

### Getting started

The administrator onboarding checklist is:

```text
1. Initial administrator created
2. First PC agent paired
3. First reader device paired
4. First Steam game registered
5. First NTAG213 registered
6. Tag assigned to game and PC
7. End-to-end test completed
```

The checklist should link directly to the relevant guided flow and remain dismissible after completion.

## Dashboard

The administrator dashboard should prioritize current state:

- CasaOS/TAGAME server health;
- connected and offline PC agents;
- current game session, if any;
- latest tag activation;
- recent start/stop events;
- errors, rejections, and timeouts requiring attention;
- onboarding or configuration warnings;
- shortcuts to register a game, tag, reader, or PC.

The guest dashboard is restricted to permitted personal status and history. It must not reveal other users' devices, audit data, credentials, or administrative controls.

## Library

### Games

The list should show:

- game name;
- Steam AppID;
- active/inactive state;
- number of associated tags;
- last execution time;
- relevant health or configuration warning.

Filtering should support name, AppID, and active state. Creation and editing may use a dedicated page or a side panel, but destructive actions require confirmation.

### Game details

The detail view should show:

- configured name and Steam AppID;
- optional executable name used for process detection;
- associated tags and target PCs;
- current or recent sessions;
- recent errors;
- a controlled test action available only to admins.

The UI must never accept an arbitrary shell command.

### Tags

The tag list should show:

- friendly name;
- TAGAME UUID token;
- associated game;
- target PC;
- active/inactive state;
- last use time.

The UUID may be copied by an administrator for writing to the NTAG213. Device and agent credentials must never be displayed alongside it.

### Tag setup

The guided flow is:

```text
Generate or register UUID
  -> assign friendly name
  -> select active game
  -> select active PC
  -> review payload
  -> write/copy NTAG213 payload
  -> perform test
```

Reassigning a tag changes the server-side association and does not require rewriting the physical tag.

## Devices

### Reader devices

The reader list should show name, platform, owner, status, creation time, last access, and revocation state. Admins may revoke a reader but cannot retrieve its original token.

### Pair reader

The flow should use a short-lived pairing code or QR code. Completion must identify the user, create the device record, issue the raw token once, and confirm the device name and platform.

### PCs and agents

The PC list should show:

- friendly PC name and hostname;
- online, degraded, or offline state;
- agent version;
- last heartbeat;
- current game/session;
- configuration or update warning.

### PC details

The detail view should include agent connection, last heartbeat, active session, recent events, recent errors, repair/re-pair actions, and an allowlisted test command.

### Pair agent

The webapp creates a short-lived pairing code. The user enters it in the C# agent setup window. On success, the server registers the PC, issues the agent credential once, and the agent begins its authenticated outbound connection.

## Activity

### Sessions

Sessions represent the actual game process lifecycle:

```text
starting -> running -> stopping -> stopped
                     \-> error
```

The active session should be prominent and show game, PC, triggering tag/device, start time, duration, and latest agent heartbeat.

### Events

Events represent request delivery and execution progress:

```text
received
  -> validated
  -> dispatched
  -> agent_acknowledged
  -> launch_requested / stop_requested
  -> completed
```

`rejected`, `error`, and `timeout` are terminal outcomes.

### Event details

The page should present a chronological diagnostic timeline, for example:

```text
20:30:01.120  received
20:30:01.135  validated
20:30:01.148  dispatched
20:30:01.162  agent_acknowledged
20:30:01.215  launch_requested
20:30:02.430  completed — session running
```

The page should show a safe explanation, correlation IDs where useful, and eligible recovery actions without displaying credentials or internal stack traces.

## Administration

### Users

The list should show name, email, role, active state, last login, and device count. Only admins can access it.

### User details

Admins can activate/deactivate an account, change `admin`/`guest` role, review assigned devices, inspect permitted activity, and reset access. Role escalation requires explicit confirmation and an audit entry.

### Audit logs

Audit entries should show actor, action, entity, timestamp, and safe structured details. The interface must support filtering by user, action, entity type, and date without exposing secrets.

## Settings

The settings area is divided into tabs or sections:

- General;
- Security;
- Network and agent communication;
- SQLite and backups;
- Log retention;
- Appearance;
- System diagnostics;
- About and versions.

The exact settings must be derived from implemented capabilities; placeholders must not imply unsupported features.

## Profile

Available to both roles. It includes name, email, password change, authenticated sessions, personal activity, and a summary of owned devices. A guest cannot use Profile to change role or restore a revoked device.

## Cross-cutting interface states

Every data-oriented screen must define:

- `loading` — content is being fetched;
- `empty` — no records exist, with a relevant next action;
- `success` — an operation completed;
- `validation_error` — correctable form input problem;
- `permission_denied` — role or ownership prevents access;
- `offline` — server, reader, or agent is unavailable;
- `timeout` — an expected response was not received;
- `rejected` — the request was validly received but business rules denied it;
- `unexpected_error` — safe generic failure with correlation information.

Destructive actions require confirmation and must explain their effect on historical records.

## Responsive behavior

Desktop is the primary administration experience. Narrow/mobile web layouts must preserve status visibility and core inspection flows, using stacked cards, drawers, or condensed tables where appropriate. Wide tables should become summaries with detail pages rather than forcing horizontal interaction for essential information.

Reader automation does not require the entire admin interface. Android/iOS integrations need only clear success, pending, rejection, offline, timeout, and error feedback for a scan.

## PC agent interface

The C#/.NET agent has a much smaller surface:

- first-launch pairing window;
- server address and pairing-code entry;
- connected/disconnected status;
- registered PC identity;
- last heartbeat;
- current game/session;
- latest command/result;
- reconnect and diagnostics actions;
- minimize to tray and exit actions.

The tray menu should provide status, open TAGAME Agent, reconnect, diagnostics/settings, and exit. Closing the setup/status window should return the application to the tray unless the user explicitly chooses Exit.

## Open UX decisions

- Initial-admin recovery method without assuming email delivery.
- Whether guests can see their own reader-device details or only a summary.
- Whether a different running game is rejected or can be replaced after confirmation; current business-rule default is rejection.
- Whether game/tag editing uses full pages, drawers, or dialogs after wireframe validation.
- How Android and iOS display asynchronous `running` confirmation after the immediate acknowledgement.
- Exact backup and restore confirmation flow.

These questions must be resolved during `UX001` before final wireframes are approved.

