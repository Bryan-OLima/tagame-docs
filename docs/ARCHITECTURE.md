# TAGAME — Architecture

## Architectural style

TAGAME is a local-first, self-hosted web application deployed on CasaOS. It is divided into a control plane on CasaOS and an execution bridge on each target PC.

```text
Android / iOS reader
        |
        | authenticated HTTPS/HTTP on the local network
        v
CasaOS: Web UI + API + database + event dispatcher
        |
        | authenticated agent channel (prefer outbound from PC)
        v
PC agent -> Steam
```

## Components

### Web UI

Provides login, administration, tag/game/PC management, device pairing, session status, and audit history. It must never expose raw tokens or arbitrary commands.

### API

Validates users, devices, tags, assignments, sessions, and agent events. Reader clients submit a tag token; they do not submit shell commands or unrestricted Steam commands. The API is the central communication and authorization boundary: the reader and the PC agent do not communicate directly.

Web users authenticate with JWT Bearer access tokens. Reader devices and PC agents authenticate with separate revocable credentials, whose hashes are stored by TAGAME. A user's web JWT must not be reused as a reader or agent credential.

### Database

Stores the source of truth for configuration and history. The initial engine is SQLite, running on the same CasaOS notebook as the backend. Runtime requirements are defined in `DB.md`.

### Event dispatcher

Converts a validated request into a constrained `start` or `stop` event for a specific PC. It tracks acknowledgement, timeout, and result.

### PC agent

A small non-AI C#/.NET Windows application installed on the PC. Its initial UI uses Windows Forms and `NotifyIcon`: it shows setup and connection status, then remains in the Windows notification area. It authenticates with TAGAME, accepts only events targeted at its registered PC, reports heartbeat/state, and invokes an allowed Steam action.

## Trust boundaries

1. NFC tags are untrusted identifiers.
2. Reader devices are trusted only after pairing and token validation.
3. The CasaOS API is the authorization boundary.
4. The PC agent is the only component allowed to execute game processes.
5. The agent must not interpret arbitrary command text from the network.

## Communication model

The first implementation will use authenticated local HTTP requests from readers and an authenticated outbound connection from the PC agent. The initial agent transport will be selected between polling, HTTP callback, or WebSocket; it must preserve the API as the central boundary. SSH may be used for a temporary development prototype only; it is not the primary production transport.

The logical flow is:

```text
Reader -> API: authenticated tag activation request
API -> Agent: constrained start/stop event
Agent -> API: acknowledgement, heartbeat, and lifecycle result
API -> Web UI: authenticated status and history queries
```

## Command and session lifecycle

TAGAME separates command-delivery progress from the real game process state. Receiving a command is not equivalent to successfully starting a game.

Command delivery follows this progression:

```text
received
  -> validated
  -> dispatched
  -> agent_acknowledged
  -> launch_requested / stop_requested
  -> completed
```

`rejected`, `error`, and `timeout` are terminal command outcomes that may occur before `completed`.

After a start command is accepted, the associated game session follows its own lifecycle:

```text
starting -> running -> stopping -> stopped
                     \-> error
```

The reader can receive a fast acknowledgement after `agent_acknowledged`, while the UI continues tracking the session until `running`, `stopped`, `error`, or `timeout`. The agent is authoritative for the actual PC process state.

## Deployment model

TAGAME should run as a CasaOS application or compose stack containing the web/API service and SQLite database. The C# PC agent is installed separately and starts with the user's Windows session, initially returning to the tray after setup. Public exposure, router port forwarding, and cloud dependencies are not required.

## State ownership

- Database: users, permissions, devices, tags, games, assignments, sessions, and audit history.
- API: authorization, resolution, lifecycle transitions, and event creation.
- Agent: actual PC process state and Steam interaction.
- Reader: NFC detection and authenticated request submission only.
