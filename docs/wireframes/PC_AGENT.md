# TAGAME — PC Agent Low-Fidelity Wireframes

Status: approved under `UX001`.

## Purpose and boundaries

The TAGAME PC Agent is a small C#/.NET Windows Forms application with a `NotifyIcon`. It pairs one Windows PC to the TAGAME server, maintains an authenticated outbound connection, reports heartbeat and game-process state, and executes only constrained allowlisted Steam actions.

The agent is not an AI agent, does not accept arbitrary commands, and never displays its long-lived credential. Closing its window returns it to the notification area unless the user explicitly chooses Exit.

## Window conventions

- Follow native Windows behavior for window controls, keyboard navigation, scaling, and the tray context menu.
- Apply TAGAME visual identity to the setup and status windows only where it does not reduce native usability.
- Every connection status uses an icon, label, and explanatory text; color is supplementary.
- Password-like treatment is not used for the pairing code because it is temporary, but copying and exposure are kept deliberate.

---

## First launch — server and pairing

### Step 1: connect to the local server

```text
┌──────────────────────────────────────────────────┐
│ TAGAME Agent                               _ □ × │
├──────────────────────────────────────────────────┤
│ Connect this PC to TAGAME                        │
│                                                  │
│ Server address                                   │
│ [ http://tagame.local:port____________________ ] │
│                                                  │
│ The server must be reachable on your local       │
│ network. Public internet exposure is not needed. │
│                                                  │
│                              [ Test connection ] │
└──────────────────────────────────────────────────┘
```

Results:

- reachable and recognized TAGAME server: continue;
- unreachable: explain network/server checks and offer Retry;
- reachable but invalid service: explain that the address is not a compatible TAGAME server;
- insecure or externally routable configuration warning: do not imply the connection is safe merely because it responds.

### Step 2: enter pairing code

```text
┌──────────────────────────────────────────────────┐
│ TAGAME Agent                               _ □ × │
├──────────────────────────────────────────────────┤
│ Pair this PC                                     │
│                                                  │
│ Server  tagame.local                             │
│                                                  │
│ Pairing code                                     │
│ [ 8 4 9   1 5 5                               ] │
│                                                  │
│ Generate this temporary code from                │
│ TAGAME > Devices > PCs > Pair agent.             │
│                                                  │
│ [ Back ]                              [ Pair PC ] │
└──────────────────────────────────────────────────┘
```

The agent submits the short-lived pairing code and its local PC identity. It does not ask the user to paste a long-lived agent credential.

### Pairing progress

```text
┌──────────────────────────────────────────────────┐
│ Pairing Gaming PC…                               │
│                                                  │
│ (✓) Server reached                               │
│ (✓) Pairing code accepted                        │
│ (●) Registering this PC                          │
│ ( ) Saving protected credential                  │
│ ( ) Sending first heartbeat                      │
│                                                  │
│                                        [ Cancel ]│
└──────────────────────────────────────────────────┘
```

Cancel revokes or abandons incomplete pairing where possible. If the result is uncertain after connection loss, the agent checks registration state before repeating the exchange.

### Success

```text
┌──────────────────────────────────────────────────┐
│ (✓) Gaming PC is connected                       │
│                                                  │
│ Server       tagame.local                        │
│ PC identity  Gaming PC                           │
│ Connection   Online                              │
│                                                  │
│ TAGAME Agent will continue running in the        │
│ Windows notification area.                       │
│                                                  │
│                            [ Open status window ] │
│                            [ Finish and minimize ]│
└──────────────────────────────────────────────────┘
```

---

## Status window

### Connected and idle

```text
┌──────────────────────────────────────────────────┐
│ TAGAME Agent — Gaming PC                   _ □ × │
├──────────────────────────────────────────────────┤
│ (●) Connected                                    │
│                                                  │
│ Server             tagame.local                  │
│ Registered PC      Gaming PC                     │
│ Agent version      0.1.0                         │
│ Last heartbeat     8 seconds ago                 │
│                                                  │
│ CURRENT GAME                                      │
│ No TAGAME game is running                        │
│                                                  │
│ LATEST COMMAND                                   │
│ Stop Hades · Completed · 18:12                   │
│                                                  │
│ [ Reconnect ] [ Diagnostics ] [ Settings ]       │
└──────────────────────────────────────────────────┘
```

Closing with `×` hides the window and keeps the agent running. An optional first-time explanation communicates this behavior once.

### Connected with active session

```text
┌──────────────────────────────────────────────────┐
│ TAGAME Agent — Gaming PC                   _ □ × │
├──────────────────────────────────────────────────┤
│ (●) Connected                                    │
│                                                  │
│ CURRENT GAME                                     │
│ Elden Ring                                       │
│ Session      Running · 01:42:18                  │
│ Process      Detected                            │
│ Heartbeat    8 seconds ago                       │
│                                                  │
│ LATEST COMMAND                                   │
│ Start Elden Ring                                 │
│ Delivery     Completed                           │
│ Result       Running confirmed                   │
│                                                  │
│ [ Open TAGAME web app ]       [ Diagnostics ]    │
└──────────────────────────────────────────────────┘
```

Session state and command status remain separate even in this compact window.

### Disconnected

```text
┌──────────────────────────────────────────────────┐
│ TAGAME Agent — Gaming PC                   _ □ × │
├──────────────────────────────────────────────────┤
│ (!) Disconnected                                 │
│                                                  │
│ The agent cannot reach the local TAGAME server.  │
│ Last connected  2 minutes ago                    │
│ Next retry      12 seconds                       │
│                                                  │
│ Existing game processes are not stopped merely  │
│ because the connection was lost.                 │
│                                                  │
│ [ Reconnect now ] [ Diagnostics ] [ Settings ]   │
└──────────────────────────────────────────────────┘
```

The agent does not claim that a remote request succeeded while disconnected. Automatic reconnect uses a bounded backoff and the window remains usable.

### Degraded or error state

```text
┌──────────────────────────────────────────────────┐
│ (!) Connected with a problem                     │
│                                                  │
│ Server connection is active, but process-state   │
│ confirmation failed for Elden Ring.              │
│                                                  │
│ Safe reference  EVT-…                            │
│ [ Retry detection ] [ Diagnostics ]              │
└──────────────────────────────────────────────────┘
```

User-facing diagnostics avoid stack traces, credentials, raw tokens, and unrestricted command text.

---

## Tray icon and menu

### Icon states

| State | Icon treatment | Accessible tooltip example |
|---|---|---|
| Online, idle | Normal TAGAME icon | `TAGAME Agent — Connected` |
| Game running | Normal icon plus activity indicator | `TAGAME Agent — Elden Ring running` |
| Connecting | Progress treatment | `TAGAME Agent — Connecting` |
| Degraded | Warning treatment | `TAGAME Agent — Attention required` |
| Offline | Offline treatment | `TAGAME Agent — Disconnected` |
| Error | Error treatment | `TAGAME Agent — Error` |

The final icon must remain recognizable at standard Windows notification-area sizes and in light and dark system themes.

### Context menu

```text
┌──────────────────────────────────┐
│ TAGAME Agent — Connected         │  Disabled status header
│ Gaming PC                        │  Disabled identity line
├──────────────────────────────────┤
│ Open TAGAME Agent                │
│ Open TAGAME web app              │
├──────────────────────────────────┤
│ Reconnect                        │
│ Diagnostics and settings         │
├──────────────────────────────────┤
│ Exit…                            │
└──────────────────────────────────┘
```

`Reconnect` is disabled while connected unless it explicitly means restarting the connection. The label must match implemented behavior.

### Exit confirmation

```text
Exit TAGAME Agent?

This PC will stop receiving game start and stop requests and will appear
offline in TAGAME. A currently running game will not be closed automatically.

[ Cancel ]                                      [ Exit agent ]
```

Exit is the only normal action that stops the background process. Closing the status window does not show this confirmation because it only hides the window.

---

## Settings and diagnostics

### Settings

```text
Settings

Server address       [ http://tagame.local:port____________ ]
Start with Windows   [✓]
Minimize to tray     [✓]

[ Save ]

Agent registration
Gaming PC · Paired
[ Re-pair agent ]
```

Re-pairing clearly warns that the existing credential will be replaced or revoked. The credential itself is never shown or copied.

### Diagnostics

```text
Diagnostics

Server reachability       Passed
Authentication            Passed
Registered PC identity    Gaming PC
Last heartbeat            8 seconds ago
Steam availability        Detected
Current process detection Passed

[ Run checks again ] [ Copy safe diagnostic summary ]
```

The copied summary is allowlisted and redacts credentials, tokens, personal paths, unrestricted command lines, and other sensitive data.

---

## Required states and recovery

| State | Explanation | Recovery |
|---|---|---|
| First launch | No registration exists | Server test and pairing flow |
| Pairing code invalid | Code is invalid, expired, used, or for a different target | Generate a new code in the web app |
| Server unavailable | Local server cannot be reached | Retry, verify address, CasaOS state, and local network |
| Authentication rejected | Stored credential is no longer accepted | Re-pair after explicit explanation |
| Connecting | Outbound connection is being established | Wait or open diagnostics after timeout |
| Online | Heartbeats and events are operating | No recovery required |
| Degraded | Connection works but a subsystem requires attention | Targeted diagnostic and retry |
| Offline | Connection is lost | Automatic retry plus manual reconnect |
| Command rejected | Event failed identity, target, state, or allowlist validation | Report safe reason to server; never execute |
| Command timeout | Expected action confirmation did not arrive | Preserve real process state as unknown/error and retry only when safe |
| Update warning | Version is unsupported or outdated | Show only when an update mechanism exists |
| Unexpected error | Safe message and reference | Retry, diagnostics, and safe log collection |

## Approved PC-agent decisions

1. The status window and tray menu include `Open TAGAME web app`.
2. Changing the configured server address requires explicit re-pairing.
3. A copied diagnostic summary may contain application and agent versions, registered PC display name, connection and session states, safe timestamps, heartbeat age, Steam/process-detection result, and non-sensitive correlation or error codes. It excludes credentials, tokens, passwords, personal filesystem paths, source IPs, unrestricted command lines, and raw private-network details.
4. The tray icon distinguishes `game running` from merely `online` using a simple activity indicator that remains legible at Windows notification-area sizes.

No unresolved PC-agent UX questions remain for `UX001`.
