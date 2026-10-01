# TAGAME — Initial API Contract

## Status

Initial contract for the first implementation. This document defines the minimum HTTP surface for the CasaOS API, the web application, authenticated reader devices, and the PC agent.

The API is versioned under `/api/v1`. Additive and backward-compatible changes may remain in `v1`; incompatible changes require a new version.

## Conventions

- Base URL: `/api/v1`
- Format: JSON unless otherwise stated.
- Timestamps: UTC ISO 8601 strings.
- Internal identifiers: canonical UUID strings; never sequential numeric IDs.
- Authentication: `Authorization: Bearer <token>`.
- Request bodies are validated with Zod before entering application services.
- Unknown fields must be rejected or stripped consistently by the implementation.
- `GET /health` and `GET /ready` remain unversioned for deployment probes.
- Destructive historical records are not hard-deleted through the normal API. Use disable, revoke, or archive operations where applicable.

## Authentication model

The API has three client credential categories:

1. Web users authenticate with a short-lived JWT Bearer access token.
2. Reader devices authenticate with their own revocable device token.
3. PC agents authenticate with their own revocable agent token.

A web JWT must not be reused as a reader or agent credential. The API must validate the credential category and the permissions required by each route.

Refresh-token sessions are not part of the first contract. If added later, they will use a separate revocable session resource and will not be placed inside the access JWT.

## Response and error shape

Successful single-resource responses use:

```json
{
  "data": {}
}
```

Successful collection responses use:

```json
{
  "data": [],
  "meta": {
    "page": 1,
    "page_size": 25,
    "total": 0
  }
}
```

Errors use:

```json
{
  "error": {
    "code": "TAG_INACTIVE",
    "message": "The tag is not active.",
    "request_id": "uuid"
  }
}
```

The message must be safe for the caller. Secrets, password hashes, raw device tokens, raw agent tokens, and internal stack traces must never be returned.

## Health and readiness

### `GET /health`

Returns application liveness. It must not require authentication.

Response `200`:

```json
{
  "status": "ok",
  "service": "tagame-api"
}
```

### `GET /ready`

Returns application and database readiness. It must not require authentication.

Response `200`:

```json
{
  "status": "ready",
  "database": "ready"
}
```

Response `503` is used when the application is alive but cannot serve requests safely.

## Web authentication

### `POST /api/v1/auth/login`

Authenticates an `admin` or `guest` account and returns a short-lived JWT access token.

Request:

```json
{
  "email": "user@example.com",
  "password": "password"
}
```

Response `200`:

```json
{
  "data": {
    "access_token": "jwt",
    "token_type": "Bearer",
    "expires_in": 900,
    "user": {
      "id": "uuid",
      "name": "Bryan Lima",
      "email": "user@example.com",
      "role": "admin",
      "profile_photo_url": null
    }
  }
}
```

Invalid credentials and inactive accounts use the same safe error response. The API must not reveal whether an email exists.

### `GET /api/v1/auth/me`

Returns the authenticated web user's current account data.

Requires a web JWT. The response must not include `password_hash` or credentials.

### `POST /api/v1/auth/logout`

Ends the client-side web session for the initial access-token-only flow. The client discards the access token. Server-side token revocation is not required until a revocation strategy is selected.

## Reader device endpoints

These endpoints use a paired reader-device credential, not the web user's JWT.

### `POST /api/v1/reader/tags/activate`

Reads a tag token and requests the TAGAME start/stop toggle.

Headers:

```text
Authorization: Bearer <reader-device-token>
Idempotency-Key: <unique-request-id>
```

Request:

```json
{
  "tag_token": "550e8400-e29b-41d4-a716-446655440000"
}
```

The client cannot provide or override the game, PC, Steam AppID, command, or action. The API resolves those values from the active assignment and current session state.

Response `202`:

```json
{
  "data": {
    "event_id": "uuid",
    "action": "start",
    "status": "received"
  }
}
```

The API may reject the request before creating or after creating an event according to the event-audit rule. Every accepted, rejected, failed, or timed-out action must be represented in `launch_events`.

### `GET /api/v1/reader/devices/me`

Returns the reader device's safe identity and status. It must not return the raw device token.

### `POST /api/v1/reader/devices/:device_id/revoke`

Allows an administrator to revoke a reader device. A guest may not revoke arbitrary devices.

## PC agent endpoints

These endpoints use the paired agent credential. The agent must only access records and events for its registered `pc_id`.

### `POST /api/v1/agent/heartbeat`

Reports that the agent is connected and provides safe runtime information.

Request:

```json
{
  "agent_version": "0.1.0",
  "status": "online",
  "running_game": {
    "steam_appid": 1245620,
    "process_name": "eldenring.exe"
  }
}
```

`running_game` is nullable. The agent must not submit arbitrary commands or executable paths as an instruction.

### `GET /api/v1/agent/events/next`

Polling endpoint for the next pending event targeted at the authenticated agent's PC.

Response `200` when an event exists:

```json
{
  "data": {
    "event_id": "uuid",
    "action": "start",
    "steam_appid": 1245620,
    "session_id": "uuid"
  }
}
```

Response `204` when no event is available.

The response contains only the constrained action and configured Steam AppID. It must not contain shell text or an arbitrary command.

### `POST /api/v1/agent/events/:event_id/acknowledge`

Confirms that the authenticated agent received and accepted the event for its registered PC.

Response `200`:

```json
{
  "data": {
    "event_id": "uuid",
    "status": "agent_acknowledged"
  }
}
```

### `POST /api/v1/agent/events/:event_id/result`

Reports the constrained action and the observed process result.

Request:

```json
{
  "status": "completed",
  "result": "success",
  "process_state": "running",
  "error_message": null
}
```

The API validates that the event transition is legal and that the event targets the authenticated agent's PC.

## Administrative resources

All endpoints in this section require a web JWT with the `admin` role.

### Games

```text
GET    /api/v1/games
POST   /api/v1/games
GET    /api/v1/games/:game_id
PATCH  /api/v1/games/:game_id
POST   /api/v1/games/:game_id/disable
POST   /api/v1/games/:game_id/reactivate
```

The create and update payloads include `name`, `steam_appid`, optional `cover_url`, optional `cover_source`, and optional `executable_name`. The client cannot submit arbitrary execution commands.

### Tags

```text
GET    /api/v1/tags
POST   /api/v1/tags
GET    /api/v1/tags/:tag_id
PATCH  /api/v1/tags/:tag_id
POST   /api/v1/tags/:tag_id/disable
POST   /api/v1/tags/:tag_id/reactivate
```

Creating a tag generates or registers the opaque UUID token. The API must not allow a client to attach commands, credentials, or a Steam AppID to the tag payload.

### Tag assignments

```text
POST   /api/v1/tags/:tag_id/assignments
GET    /api/v1/tags/:tag_id/assignments
POST   /api/v1/tags/:tag_id/assignments/:assignment_id/end
```

Creating a new active assignment ends the previous active assignment for that tag in the same transaction.

### PCs and agents

```text
GET    /api/v1/pcs
POST   /api/v1/pcs
GET    /api/v1/pcs/:pc_id
PATCH  /api/v1/pcs/:pc_id
POST   /api/v1/pcs/:pc_id/disable
POST   /api/v1/pcs/:pc_id/reactivate
GET    /api/v1/pcs/:pc_id/agent
POST   /api/v1/pcs/:pc_id/agent/revoke
```

The raw agent credential is returned only during a secure pairing flow and is never retrievable later.

### Reader devices

```text
GET    /api/v1/devices
GET    /api/v1/devices/:device_id
POST   /api/v1/devices/:device_id/revoke
POST   /api/v1/devices/:device_id/reactivate
```

### Users

```text
GET    /api/v1/users
POST   /api/v1/users
GET    /api/v1/users/:user_id
PATCH  /api/v1/users/:user_id
POST   /api/v1/users/:user_id/disable
POST   /api/v1/users/:user_id/reactivate
```

Only administrators may manage users or roles. The only supported roles are `admin` and `guest`.

## Activity and audit endpoints

### `GET /api/v1/sessions`

Lists game sessions with filters for `status`, `game_id`, `pc_id`, `tag_id`, and time range.

### `GET /api/v1/sessions/:session_id`

Returns a session and its safe lifecycle information.

### `GET /api/v1/launch-events`

Lists launch events with filters for `status`, `action`, `game_id`, `pc_id`, `tag_id`, `device_id`, and time range.

### `GET /api/v1/launch-events/:event_id`

Returns the event timeline, including delivery and agent confirmation states.

### `GET /api/v1/audit-logs`

Lists administrative and security-sensitive changes. Only administrators may access this endpoint.

## Initial implementation boundary

The first implementation should prioritize:

```text
GET  /health
GET  /ready
POST /api/v1/auth/login
GET  /api/v1/auth/me
POST /api/v1/reader/tags/activate
POST /api/v1/agent/heartbeat
GET  /api/v1/agent/events/next
POST /api/v1/agent/events/:event_id/acknowledge
POST /api/v1/agent/events/:event_id/result
GET  /api/v1/sessions
GET  /api/v1/launch-events
```

CRUD and pairing endpoints can be implemented alongside the administration panel and agent setup flow.
