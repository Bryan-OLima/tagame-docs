# TAGAME — Business Rules

## Roles and access control

### Administrator (`admin`)

Administrators may:

- create, edit, disable, and reactivate users;
- assign or remove the `admin` and `guest` roles;
- register and revoke reader devices;
- register and manage PCs and their agents;
- create, edit, disable, and delete games;
- create, edit, disable, and reassign tags;
- view all launch events, sessions, and audit logs;
- retry or cancel eligible pending events.

### Guest (`guest`)

Guests may:

- authenticate with an active account;
- use active devices assigned to them;
- trigger active tags and authorized game actions;
- view only their own relevant history, if history viewing is enabled.

Guests may not:

- promote themselves or another user to `admin`;
- manage users, devices, PCs, games, tags, or assignments;
- change Steam AppIDs or execution commands;
- view secrets, raw tokens, or another user's private audit information.

Only an existing administrator may grant or revoke administrator access. The initial administrator is created during first-time setup.

## Authentication and device authorization

1. Every API request that triggers an action must identify an authorized device.
2. Each device receives a random UUID token during pairing.
3. The raw token is stored only on the device; TAGAME stores its hash.
4. A device can be disabled or revoked without deleting its user.
5. Revoked, inactive, or unknown device tokens are rejected.
6. Agent credentials follow the same rule: use a revocable secret and store only its hash.
7. A browser login session does not automatically authorize background NFC requests; the reader integration must use a paired device credential.

## NFC tag behavior

1. A tag contains only an opaque UUID token in its NDEF payload.
2. A tag never contains a shell command, password, agent credential, or unrestricted AppID command.
3. The token identifies the tag; it does not prove that the tag is authentic.
4. The active game association is stored in TAGAME and can be changed without rewriting the tag.
5. Inactive or unknown tags are rejected.
6. A phone reads the tag and sends the API request. The tag itself does not make network requests.
7. NFC reader support depends on the device and integration. Android and iOS are supported through reader-specific integrations; a generic browser is not assumed to support background NFC.

## Tag-to-game resolution

1. TAGAME resolves `tag.token` to the single active `tag_assignments` row.
2. The assignment must reference an active tag, game, and PC.
3. The resolved `steam_appid` is obtained from the configured game record.
4. The client cannot override the game, PC, or command by sending extra request fields.
5. Reassigning a tag ends the previous assignment and creates a new assignment record.

## Launch behavior

1. A valid start request creates a `launch_event` with `action = start`.
2. The event is accepted only after validating the device, tag, assignment, game, and PC.
3. The CasaOS application sends a constrained event to the selected PC agent.
4. The agent may launch only configured games and only for its registered PC.
5. The launch mechanism may use Steam's `steam://rungameid/<steam_appid>` protocol or an equivalent platform-specific command.
6. The agent reports acceptance, success, failure, or timeout back to TAGAME.
7. The session state is updated from agent confirmation, not merely from the request being created.

## Event lifecycle

1. Every reader request begins as `received`.
2. It becomes `validated` only after device authentication, authorization, tag resolution, assignment checks, and PC availability checks succeed.
3. It becomes `dispatched` when the backend sends the constrained command to the intended PC agent.
4. It becomes `agent_acknowledged` only when that agent confirms receipt and identity validation.
5. A start event becomes `launch_requested` when the agent asks Windows/Steam to start the configured game. A stop event becomes `stop_requested` when the agent begins the configured stop procedure.
6. `completed` means the requested final state was confirmed: `running` for start or `stopped` for stop.
7. `rejected`, `error`, and `timeout` are terminal outcomes and must include a safe diagnostic reason where available.
8. The API may return an early acknowledgement to the reader after `agent_acknowledged`; it must not describe the game as running until the agent confirms the process state.
9. Invalid transitions must be rejected and recorded. A terminal event cannot return to an active state.

## Start/stop toggle

1. `tags.is_active` means the tag is enabled for use; it does not mean the game is running.
2. Runtime state is tracked in `game_sessions`.
3. If a tag is tapped and no active `starting` or `running` session exists for its assignment, TAGAME requests `start`.
4. If the same tag has an active session, TAGAME requests `stop` and marks the session `stopping`.
5. The agent must confirm that the game stopped before the session becomes `stopped`.
6. If a game is closed manually, the agent should report the change through heartbeat or process detection.
7. If another game is running on the same PC, the configured policy must decide whether to reject the new start, stop the existing game first, or allow concurrent games. The default policy is to reject and record the event.
8. Stopping a game must be implemented by the agent using safe process/game handling. Starting via a Steam URI does not by itself define a reliable stop operation.

## PC agent rules

1. The agent is a small background execution bridge, not an AI system.
2. It must not accept arbitrary shell text from TAGAME, a device, or an NFC tag.
3. It validates the event signature/token and the target PC identity.
4. It maps the requested game record to a preconfigured allowed action.
5. It reports heartbeats and lifecycle results.
6. An offline agent causes a clear `timeout` or `error` result; the API must not silently claim success.
7. The agent should maintain an outbound connection to CasaOS when practical, avoiding public inbound exposure on the PC.

## Network and deployment rules

1. TAGAME is local-first and normally runs only on the home network through CasaOS.
2. No router port forwarding or public exposure is required.
3. If remote access is later enabled, it must use an authenticated encrypted channel such as a VPN or HTTPS reverse proxy.
4. The API must validate input and use parameterized database queries.
5. Secrets must not be written to `launch_events`, `audit_logs`, application logs, or error messages.

## Audit and retention

1. Every accepted, rejected, successful, failed, or timed-out start/stop request creates a `launch_events` record.
2. Administrative changes create an `audit_logs` record.
3. Logs include actor/device, tag, game, PC, action, result, and timestamp where known.
4. Logs may contain a source IP for local diagnostics, but never raw tokens or passwords.
5. Deleting a tag, game, device, or user should preserve historical event records through nullable references or archival state rather than silently rewriting history.
