# TAGAME — Web Application Low-Fidelity Wireframes

Status: approved under `UX001`.

## Purpose

These wireframes define information hierarchy, behavior, responsive adaptation, and system states before visual styling. They intentionally avoid final colors, fonts, imagery, glow, decorative geometry, and brand treatments. The approved cyberpunk-inspired visual direction belongs to `UI001` and must be applied only after these flows are approved.

## Wireframe conventions

- `[ Primary action ]` represents the main action on a screen or step.
- `[ Secondary ]` represents a lower-emphasis action.
- `( Status )` represents a visible status indicator with text and an icon in the final UI.
- `! Message` represents an alert, error, warning, or recovery instruction.
- Dashed regions are optional or conditional.
- Sensitive credentials, raw device tokens, agent tokens, and password values are never displayed.
- Command-delivery state and game-session state must always have separate labels.

## Shared responsive behavior

### Desktop

- Access pages use a centered form area with an optional contextual panel beside it.
- Authenticated pages use persistent primary navigation and a flexible content canvas.
- The Dashboard may use multiple columns, but the reading order must remain meaningful when linearized.
- Attention-required content precedes historical content.

### Narrow/mobile

- Content becomes a single column.
- Persistent desktop navigation becomes a compact header and drawer.
- Primary actions remain visible without horizontal scrolling.
- Tables are replaced with record summaries or cards.
- Decorative context panels are removed before functional content is compressed.
- Touch targets must remain at least 44 by 44 CSS pixels in the final visual specification.

---

## First-time setup

### Entry conditions

The setup flow is available only while no administrator exists. On entry, the web application checks setup state and server availability before showing the account form.

```text
Open TAGAME
  -> check server availability
  -> check whether an administrator exists
       -> administrator exists: go to Login
       -> none exists: show setup introduction
       -> check failed: show safe recovery state
```

The check must happen on the server. Hiding the setup route in the browser is not sufficient protection against creating a second initial administrator.

### Desktop — Step 1: welcome and server check

```text
┌──────────────────────────────────────────────────────────────────────┐
│ TAGAME                                             Initial setup 1/3 │
├───────────────────────────────┬──────────────────────────────────────┤
│                               │ Set up your local TAGAME             │
│  What will be configured      │                                      │
│                               │ (✓) Local server available            │
│  • Initial administrator      │ (✓) Storage available                 │
│  • Local system identity      │ (i) No administrator configured       │
│  • Guided next steps          │                                      │
│                               │ Your data stays on this TAGAME server.│
│  No cloud account required.   │                                      │
│                               │                   [ Start setup ]     │
└───────────────────────────────┴──────────────────────────────────────┘
```

If persistence cannot be tested safely before account creation, the storage line is shown as `Ready to verify` and the actual write verification occurs during initialization.

### Desktop — Step 2: administrator account

```text
┌──────────────────────────────────────────────────────────────────────┐
│ TAGAME                                             Initial setup 2/3 │
├───────────────────────────────┬──────────────────────────────────────┤
│  Initial administrator        │ Create administrator account         │
│                               │                                      │
│  This account can manage      │ Name                                 │
│  users, devices, tags, games, │ [________________________________]   │
│  PCs and system settings.     │                                      │
│                               │ Email                                │
│  Store the password safely.   │ [________________________________]   │
│  Recovery is local-first and  │                                      │
│  may require server access.   │ Password                      [Show] │
│                               │ [________________________________]   │
│                               │ • requirements supplied by server    │
│                               │                                      │
│                               │ Confirm password              [Show] │
│                               │ [________________________________]   │
│                               │                                      │
│                               │ [ Back ]       [ Review account ]    │
└───────────────────────────────┴──────────────────────────────────────┘
```

Requirements must be visible before submission. Validation appears next to the relevant field and in a summary at the top when several fields fail. Password values are never repeated on the review screen.

### Desktop — Step 3: review and initialize

```text
┌──────────────────────────────────────────────────────────────────────┐
│ TAGAME                                             Initial setup 3/3 │
├──────────────────────────────────────────────────────────────────────┤
│ Review initial administrator                                         │
│                                                                      │
│ Name       Bryan Example                                  [ Edit ]   │
│ Email      bryan@example.local                            [ Edit ]   │
│ Role       Administrator                                            │
│ Password   Configured                                               │
│                                                                      │
│ This action initializes TAGAME and creates the only initial admin.   │
│ Additional administrators can be created later by an administrator. │
│                                                                      │
│ [ Back ]                              [ Initialize TAGAME ]           │
└──────────────────────────────────────────────────────────────────────┘
```

While initialization is in progress, inputs and navigation are locked and the action reads `Initializing…`. Retrying after a network interruption must begin by checking whether initialization already succeeded, preventing duplicate initial administrators.

### Success

```text
┌──────────────────────────────────────────────────────┐
│ (✓) TAGAME initialized                              │
│                                                      │
│ The administrator account was created successfully.  │
│ Next: sign in and connect your first PC agent.        │
│                                                      │
│                                  [ Continue to login ]│
└──────────────────────────────────────────────────────┘
```

The initial default is to continue to Login instead of assuming that account creation also issued an authenticated browser session. Automatic sign-in remains an owner decision.

### Narrow/mobile

```text
┌──────────────────────────┐
│ TAGAME       Setup 2/3   │
├──────────────────────────┤
│ Create administrator     │
│                          │
│ Short explanation        │
│                          │
│ Name                     │
│ [______________________] │
│ Email                    │
│ [______________________] │
│ Password          [Show] │
│ [______________________] │
│ Password requirements    │
│ Confirm password  [Show] │
│ [______________________] │
│                          │
│ [ Review account       ] │
│ [ Back                 ] │
└──────────────────────────┘
```

The contextual desktop panel becomes short inline explanatory text. The primary action precedes the secondary action in visual prominence, while keyboard and screen-reader order remains logical.

### Required states

| State | Treatment | Recovery |
|---|---|---|
| Checking setup | Page skeleton plus `Checking TAGAME…` | Wait; no duplicate actions |
| Server unavailable | Explain that the local TAGAME server could not be reached | Retry and show local-network guidance |
| Storage check failed | Do not allow initialization | Retry after fixing storage; preserve non-password fields locally only if safe |
| Validation error | Field message plus accessible summary | Correct fields and resubmit |
| Already initialized | Do not show the setup form | Continue to Login |
| Initialization uncertain | Check server state before retrying | Resume at success or safely retry |
| Unexpected error | Safe message and correlation identifier where available | Retry or open diagnostics guidance |

---

## Login

### Desktop

```text
┌──────────────────────────────────────────────────────────────────────┐
│ TAGAME                                                               │
├───────────────────────────────┬──────────────────────────────────────┤
│                               │ Sign in                              │
│  Local NFC game control       │                                      │
│                               │ Email                                │
│  (●) Local server reachable   │ [________________________________]   │
│                               │                                      │
│  Physical tag                 │ Password                      [Show] │
│       ↓                       │ [________________________________]   │
│  Authorized reader            │                                      │
│       ↓                       │ [ Sign in                         ]   │
│  TAGAME → PC → Steam          │                                      │
│                               │ Access recovery is not shown until   │
│                               │ the local-first recovery method is   │
│                               │ approved.                            │
└───────────────────────────────┴──────────────────────────────────────┘
```

The final interface must not expose the internal flow diagram as technical noise if testing shows that it distracts from authentication. It may be replaced with restrained brand context during `UI001`.

### Narrow/mobile

```text
┌──────────────────────────┐
│ TAGAME                   │
│ (●) Server reachable     │
├──────────────────────────┤
│ Sign in                  │
│                          │
│ Email                    │
│ [______________________] │
│ Password          [Show] │
│ [______________________] │
│                          │
│ [ Sign in             ]  │
└──────────────────────────┘
```

### Authentication outcomes

| Outcome | User-facing behavior |
|---|---|
| Invalid credentials | Generic failure that does not confirm whether the email exists |
| Inactive account | Explain that access is unavailable and an administrator is required, without exposing additional account details |
| Rate limited | State that there were too many attempts and when retry becomes available, if the server supplies that safely |
| Server unavailable | Distinguish connectivity failure from credential failure; offer Retry |
| Unexpected error | Safe generic message with correlation information when available |
| Success | Send admin to the admin Dashboard and guest to the restricted Dashboard |

The Sign in button shows a pending state and prevents accidental repeated submission. Focus moves to the error summary after a failed submit, while entered email remains available and the password is cleared.

---

## Dashboard

### Information priority

The Dashboard answers these questions in order:

1. Does anything require immediate attention?
2. Is a game currently starting, running, stopping, or in error?
3. Are the server and target PC agents available?
4. What happened on the latest tag activation?
5. What happened recently?
6. What should the administrator configure next?

Command-delivery progress and actual game-session state are never merged into one ambiguous status. For example, `Command: completed` and `Session: running` may appear together, but `agent_acknowledged` must never be labeled as `running`.

### Administrator desktop — configured system

```text
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ TAGAME        │ Dashboard                         Search   Help   Profile     │
│               ├──────────────────────────────────────────────────────────────┤
│ Dashboard     │ ! 2 items need attention                    [ Review ]       │
│               ├────────────────────────────────────┬─────────────────────────┤
│ Library       │ CURRENT GAME SESSION               │ SYSTEM STATUS           │
│  Games        │                                    │                         │
│  Tags         │ Game: Elden Ring                   │ (●) TAGAME server       │
│               │ PC: Gaming PC                      │ (●) 1 agent online      │
│ Devices       │ Session: Running · 01:42:18        │ (○) 1 PC offline        │
│  Readers      │ Command: Completed                 │ Last check: just now    │
│  PCs          │ Agent heartbeat: 8 seconds ago     │                         │
│               │                                    │ [ View devices ]        │
│ Activity      │ [ View session ]                   │                         │
│  Sessions     ├────────────────────────────────────┴─────────────────────────┤
│  Events       │ LATEST TAG ACTIVATION              QUICK ACTIONS            │
│               │                                    │                         │
│ Administration│ Card — Elden Ring                  │ [ Register game ]       │
│  Users        │ Start requested from Bryan S20 FE  │ [ Register tag ]        │
│  Audit logs   │ Command: Completed                 │ [ Pair reader ]         │
│               │ Session: Running                   │ [ Pair PC agent ]       │
│ Settings      │ 2 minutes ago                      │                         │
│               ├────────────────────────────────────┴─────────────────────────┤
│               │ RECENT EVENTS                                               │
│               │ Time     Action  Game        PC          Command result      │
│               │ 20:30    Start   Elden Ring  Gaming PC   Completed           │
│               │ 18:12    Stop    Hades       Gaming PC   Completed           │
│               │ 17:41    Start   Portal 2    Office PC   Timeout   [Inspect] │
│               │                                               [ View all ]   │
└───────────────┴──────────────────────────────────────────────────────────────┘
```

The attention banner is absent when there is nothing actionable. The current-session panel remains prominent while a session is `starting`, `running`, `stopping`, or `error`. Historical stopped sessions belong in Recent Events or Sessions.

### Administrator desktop — unconfigured system

```text
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Navigation    │ Dashboard                                                    │
│               ├──────────────────────────────────────────────────────────────┤
│               │ GETTING STARTED                              1 of 7 complete │
│               │                                                              │
│               │ (✓) Initial administrator created                            │
│               │ ( ) Pair first PC agent                   [ Pair PC ]         │
│               │ ( ) Pair first reader                     [ Pair reader ]     │
│               │ ( ) Register first Steam game             [ Add game ]        │
│               │ ( ) Register first NTAG213                [ Add tag ]         │
│               │ ( ) Assign tag to game and PC                                │
│               │ ( ) Complete end-to-end test                                 │
│               │                                             [ Dismiss later ] │
│               ├──────────────────────────────────────────────────────────────┤
│               │ No active game session                                       │
│               │ Complete the next setup step to begin using TAGAME.           │
└───────────────┴──────────────────────────────────────────────────────────────┘
```

Empty states direct the administrator to the next valid prerequisite. They do not present actions that cannot yet succeed.

### Guest desktop

```text
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ TAGAME        │ Dashboard                                      Profile       │
│               ├──────────────────────────────────────────────────────────────┤
│ Dashboard     │ YOUR CURRENT ACTIVITY                                        │
│ Activity      │                                                              │
│ Profile       │ Game: Elden Ring                                             │
│               │ PC: Gaming PC                                                │
│               │ Session: Running · 00:18:24                                  │
│               │ Latest request: Completed                                    │
│               │                                             [ View details ] │
│               ├──────────────────────────────────────────────────────────────┤
│               │ YOUR RECENT ACTIVITY                                         │
│               │ 20:30  Start  Elden Ring  Completed                          │
│               │ 18:12  Stop   Hades       Completed                          │
│               │                                                [ View all ]  │
└───────────────┴──────────────────────────────────────────────────────────────┘
```

The guest Dashboard excludes other users' devices and activity, system-wide health, audit data, administrative warnings, credentials, and management actions. Exact guest visibility for reader details remains an open decision.

### Narrow/mobile administrator

```text
┌──────────────────────────────┐
│ ☰ TAGAME       Dashboard  ◉  │
├──────────────────────────────┤
│ ! 2 items need attention     │
│ [ Review                   ] │
├──────────────────────────────┤
│ CURRENT SESSION              │
│ Elden Ring                   │
│ Session: Running · 01:42:18  │
│ Command: Completed           │
│ Gaming PC · heartbeat 8s ago │
│ [ View session            ]  │
├──────────────────────────────┤
│ SYSTEM STATUS                │
│ Server: Online               │
│ Agents: 1 online · 1 offline │
│ [ View devices            ]  │
├──────────────────────────────┤
│ LATEST TAG ACTIVATION        │
│ Card — Elden Ring            │
│ Completed · 2 minutes ago    │
├──────────────────────────────┤
│ QUICK ACTIONS                │
│ [ Game ] [ Tag ]             │
│ [ Reader ] [ PC agent ]      │
├──────────────────────────────┤
│ RECENT EVENTS                │
│ Event summary card           │
│ Event summary card           │
│ [ View all                ]  │
└──────────────────────────────┘
```

On mobile, attention and current-session information appear before system summaries. Recent events are summaries, not a horizontally scrolling desktop table.

### Dashboard states

| State | Dashboard behavior |
|---|---|
| Loading | Preserve card geometry with skeletons; do not show false offline states |
| No configuration | Show Getting Started and the next valid action |
| No active session | Show a calm empty current-session panel and recent history if available |
| Agent offline | Identify the affected PC, last heartbeat, impact, and available recovery action |
| Timeout | Show the relevant command as timed out without claiming the game stopped or started |
| Rejected | Show the safe business reason and link to inspect the event when permitted |
| Partial data failure | Keep healthy panels usable and mark only the failed panel |
| Server unavailable | Replace dashboard data with a page-level reconnect state; do not display stale data as current |
| Permission denied | Remove restricted controls and provide a clear access explanation |
| Unexpected error | Safe explanation, retry, and correlation identifier where available |

## Approved initial decisions

The project owner approved the following decisions:

1. Successful first-time setup continues to the normal Login and does not automatically create a browser session.
2. Initial setup creates only the first administrator; local system naming remains in Settings.
3. Guests may see the target PC name when it belongs to their permitted activity.
4. Guests may open safe details for their own permitted events.
5. The onboarding checklist may be dismissed before completion and reopened later.

---

## Authenticated application shell

### Administrator desktop navigation

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

The sidebar may collapse to icons at intermediate widths only when every icon retains an accessible name and a tooltip. The active destination has a textual and structural indication in addition to any future color treatment.

### Guest navigation

```text
Dashboard
Activity
├── Sessions
└── Events
Profile
```

Guest navigation does not render hidden administrator destinations. Direct access to an unauthorized route shows Access denied instead of exposing page data or returning a misleading Not found state.

### Narrow/mobile navigation

```text
┌──────────────────────────────┐
│ ☰ TAGAME       Page title  ◉ │
└──────────────────────────────┘

Open menu
┌──────────────────────────────┐
│ Profile summary              │
│ Dashboard                    │
│ Library                    › │
│ Devices                    › │
│ Activity                   › │
│ Administration             › │
│ Settings                     │
│ ──────────────────────────── │
│ Sign out                     │
└──────────────────────────────┘
```

The mobile drawer traps focus while open, closes with Escape, and returns focus to the menu trigger.

---

## Library — Games

### Games list — desktop

```text
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Navigation    │ Games                                    [ Register game ]  │
│               ├──────────────────────────────────────────────────────────────┤
│               │ Search [____________]  Status [ All ▾ ]  [ Clear filters ]  │
│               ├──────────────────────────────────────────────────────────────┤
│               │ Game          AppID    Status  Tags  Last execution  Health │
│               │ Elden Ring    1245620  Active  2     Today 20:30     Ready  │
│               │ Hades         1145360  Active  1     Yesterday       Ready  │
│               │ Portal 2      620      Inactive 0    Never           Setup  │
│               │                                                    1–3 of 3 │
└───────────────┴──────────────────────────────────────────────────────────────┘
```

Search supports game name and Steam AppID. Selecting a row opens Game details. Status and health are separate: an active game may still have a configuration warning.

### Register or edit game

```text
Game information

Name                 [________________________________]
Steam AppID          [________________________________]
Executable name      [________________________________]  Optional
Cover source         [ Steam / Manual / None ▾ ]
Manual cover URL     [________________________________]  Conditional
Active               [ switch ]

! The Steam AppID identifies the configured game. It is not supplied by a tag
  activation request and cannot be overridden by a reader.

[ Cancel ]                                      [ Save game ]
```

The form uses a full page on narrow screens. Desktop implementation may use a full page or wide drawer after usability validation. It never accepts an arbitrary executable command or shell arguments.

### Game details

```text
Game / Elden Ring                                      [ Edit ]  [ More ▾ ]

SUMMARY                 ASSOCIATIONS                CURRENT STATE
AppID 1245620           2 active tags              Running on Gaming PC
Executable eldenring…   1 target PC                [ View session ]
Status Active

Associated tags
Tag name                 Target PC       Status        Last used
Card — Elden Ring        Gaming PC       Active        Today 20:30

Recent sessions and safe errors
...

[ Run controlled test ]  Admin only; confirmation required
```

Deleting or disabling a game requires confirmation that explains the effect on new launches and preservation of historical records.

### Empty and mobile states

- With no games, show `Register your first Steam game` and one primary action.
- On mobile, each game becomes a summary card with name, AppID, active state, tag count, last execution, and health.
- Cover loading failure never blocks game management or launch authorization.

---

## Library — Tags

### Tags list

```text
┌───────────────┬──────────────────────────────────────────────────────────────┐
│ Navigation    │ Tags                                       [ Register tag ] │
│               ├──────────────────────────────────────────────────────────────┤
│               │ Search [________] Status [ All ▾ ] Game [ All ▾ ] PC [All▾] │
│               ├──────────────────────────────────────────────────────────────┤
│               │ Tag name         Game        Target PC    Status  Last use  │
│               │ Card — Elden Ring Elden Ring Gaming PC    Active  20:30     │
│               │ Blue token        —           —            Setup   Never     │
└───────────────┴──────────────────────────────────────────────────────────────┘
```

The UUID token may be viewed and copied only by an administrator inside Tag details or the guided registration flow. It is not displayed in the default list, and device or agent credentials never appear near it.

### Guided tag setup

```text
1 Identifier -> 2 Name -> 3 Game -> 4 PC -> 5 Review -> 6 Write -> 7 Test
```

```text
Step 1 — Tag identifier
(•) Generate a new TAGAME UUID
( ) Register an existing valid UUID
[ Continue ]

Step 2 — Friendly name
Name [ Card — Elden Ring________________ ]
[ Back ] [ Continue ]

Step 3 — Select active game
Search [____________]
( ) Elden Ring   AppID 1245620   Active
[ Back ] [ Continue ]

Step 4 — Select active PC
( ) Gaming PC   Agent online   Last heartbeat 8s ago
( ) Office PC   Agent offline  Cannot complete test
[ Back ] [ Continue ]

Step 5 — Review server-side assignment
Tag   Card — Elden Ring
Game  Elden Ring
PC    Gaming PC
[ Back ] [ Create tag and assignment ]

Step 6 — Write physical tag
NDEF payload [ opaque UUID value ] [ Copy ]
The payload identifies this tag only. It contains no command or credential.
[ I wrote the tag ] [ Do this later ]

Step 7 — Test
Tap the physical tag with a paired reader.
Command: waiting / acknowledged / completed / rejected / timeout
Session: starting / running / error
[ Finish ]
```

Reassigning an existing tag starts at Game and PC selection, ends the previous assignment, and does not require rewriting the physical tag.

### Narrow/mobile

Each step occupies one page-height flow with one clear primary action. Selection lists become searchable cards. The UUID can wrap safely and has a dedicated copy action; it is never truncated without a way to inspect the complete value.

---

## Devices — Readers

### Reader list

```text
Readers                                               [ Pair reader ]

Search [________] Platform [ All ▾ ] Status [ All ▾ ]

Name          Platform  Owner   Status   Last seen       Created
Bryan S20 FE  Android   Bryan   Active   2 minutes ago   12 Sep
Home iPhone   iOS       Guest   Revoked  4 days ago      02 Sep
```

Administrators may revoke a reader but cannot retrieve its original credential. A revoked record remains available for historical context.

### Pair reader

```text
1 Reader details -> 2 Pairing code -> 3 Confirmation

Reader name  [____________________________]
Platform     [ Android / iOS / Web ▾ ]
Owner        [ Select active user ▾ ]
[ Continue ]

Pairing code
┌──────────────────────┐
│       731 492        │   Expires in 04:32
│      [ QR code ]     │
└──────────────────────┘
Use this code only in the TAGAME reader setup.
[ Cancel pairing ]

(✓) Reader paired
Bryan S20 FE · Android · Bryan
[ View reader ] [ Done ]
```

The long-lived raw reader credential is issued to the reader integration and is never displayed in the admin interface after pairing. Pairing expiration, cancellation, already-used, and unexpected-error states provide a safe restart action.

---

## Devices — PCs and agents

### PC list

```text
PCs                                                   [ Register PC ]

Name        Hostname       Agent     Last heartbeat  Current session  Health
Gaming PC   GAMING-DESKTOP Online    8 seconds ago   Elden Ring       Ready
Office PC   OFFICE-01      Offline   2 days ago      —                Attention
```

Online, degraded, offline, revoked, and disabled are explicit labels with icons. `PC active` and `Agent online` remain separate facts.

### PC details

```text
PC / Gaming PC                                      [ Edit ] [ More ▾ ]

PC IDENTITY               AGENT CONNECTION          CURRENT SESSION
Hostname GAMING-DESKTOP   Online                    Elden Ring
PC status Active          Version 0.1.0             Running · 01:42:18
                          Heartbeat 8s ago           [ View session ]

Latest command/result
Start Elden Ring · Completed · 20:30

Recent errors
No recent errors

[ Run allowlisted test ] [ Re-pair agent ] [ Open diagnostics ]
```

The controlled test never exposes or accepts shell text. Re-pairing warns that the previous agent credential will be revoked.

### Pair PC agent

```text
1 Register PC -> 2 Generate code -> 3 Enter code in agent -> 4 Confirm

PC name   [ Gaming PC___________________ ]
Hostname  [ GAMING-DESKTOP_____________ ]
[ Continue ]

Agent pairing code
┌──────────────────────┐
│       849 155        │  Expires in 04:32
└──────────────────────┘
Enter this code in the TAGAME Agent window on Gaming PC.

Waiting for agent…
  -> Agent connected
  -> PC identity verified
  -> Credential issued directly to agent

(✓) Gaming PC paired
Agent online · heartbeat received
[ View PC ] [ Done ]
```

---

## Activity — Sessions

### Session list

```text
Sessions

Search [________] Status [ All ▾ ] Game [ All ▾ ] PC [ All ▾ ] Date [____]

Started   Game        PC          Status    Duration   Trigger
20:30     Elden Ring  Gaming PC   Running   01:42:18   Card — Elden Ring
18:12     Hades       Gaming PC   Stopped   00:51:04   Red card
17:41     Portal 2    Office PC   Error     00:00:08   Blue token
```

Guests see only permitted personal sessions. Triggering device or tag details are reduced when ownership rules do not allow disclosure.

### Session details

```text
Session / Elden Ring

SESSION STATE                         RELATED COMMAND
Running · 01:42:18                    Completed
Gaming PC                             Start requested at 20:30:01
Heartbeat 8 seconds ago               [ View event timeline ]

Game         Elden Ring
Started      20:30:02
Triggered by Card — Elden Ring via Bryan S20 FE

Session lifecycle
(✓) Starting 20:30:01
(●) Running  20:30:02
( ) Stopping
( ) Stopped

[ Request stop ]  Admin only and only when eligible
```

An error session explains the safe reason and recovery action without stack traces or secrets.

---

## Activity — Events

### Event list

```text
Events

Search [____] Result [ All ▾ ] Action [ All ▾ ] PC [ All ▾ ] Date [____]

Time      Action  Game        PC          Command status  Result
20:30:01  Start   Elden Ring  Gaming PC   Completed       Success
18:12:44  Stop    Hades       Gaming PC   Completed       Success
17:41:03  Start   Portal 2    Office PC   Timeout         Timeout
```

### Event details and history review

```text
Event / Start Elden Ring

RESULT
Success — requested final state confirmed
Correlation ID  [ safe identifier ] [ Copy ]

COMMAND DELIVERY                    GAME SESSION
(✓) Received             20:30:01   Starting 20:30:01
(✓) Validated            20:30:01   Running  20:30:02
(✓) Dispatched           20:30:01   [ View session ]
(✓) Agent acknowledged   20:30:01
(✓) Launch requested     20:30:01
(✓) Completed            20:30:02

Context
Tag, permitted device, game, PC, action, and safe diagnostic reason
```

For rejected, timeout, or error results, the timeline stops at the real terminal point and explains the eligible recovery action. It never fabricates later stages.

---

## Administration — Users

### User list and details

```text
Users                                                 [ Create user ]

Search [________] Role [ All ▾ ] Status [ All ▾ ]

Name    Email                  Role   Status  Last login  Readers
Bryan   bryan@example.local    Admin  Active  Today       1
Guest   guest@example.local    Guest  Active  Yesterday   1
```

```text
User / Guest                                          [ Edit ]

ACCOUNT                  ACCESS                 ACTIVITY
guest@example.local      Role Guest             Last login yesterday
Active                   1 active reader        Recent permitted events

[ Reset access ] [ Deactivate account ] [ Change role ]
```

Changing a user to Administrator or removing administrator access requires explicit confirmation and creates an audit entry. The interface prevents users from promoting themselves unless an existing authorized administrator performs the change under the business rules.

### Role-change confirmation

```text
Change role to Administrator?

This grants access to users, devices, games, tags, PCs, audit logs,
credentials management, backups, and system settings.

Type the user's email to confirm
[________________________________]

[ Cancel ]                         [ Grant administrator access ]
```

---

## Administration — Audit logs

```text
Audit logs

Actor [ All ▾ ] Action [ All ▾ ] Entity [ All ▾ ] Date [________]

Time      Actor  Action              Entity       Safe details
20:42:10  Bryan  tag_assigned        Blue token   Hades → Gaming PC
19:10:03  Bryan  device_revoked      Home iPhone  Owner Guest
```

Audit details present structured before/after values with secrets redacted. Audit records are read-only in the interface.

---

## Settings and Profile

### Settings

```text
Settings

[ General ] [ Security ] [ Network and agents ] [ Database and backups ]
[ Log retention ] [ Appearance ] [ Diagnostics ] [ About ]

Selected section
┌──────────────────────────────────────────────────────────────────────┐
│ Only settings backed by implemented capabilities are shown.          │
│                                                                      │
│ Field label [ value or control ]                                     │
│ Help text with effect and risk                                       │
│                                                    [ Save changes ]  │
└──────────────────────────────────────────────────────────────────────┘
```

Backup, restore, network, and security changes require contextual confirmation proportional to risk. Placeholders must not imply that an unavailable capability already works.

### Profile

```text
Profile

[ Account ] [ Password ] [ Sessions ] [ My devices ] [ My activity ]

Name   [____________________________]
Email  [____________________________]
Role   Guest                         Read-only

[ Save profile ]
```

Guests cannot change role, reactivate a revoked device, or access another user's information through Profile.

---

## System pages

```text
ACCESS DENIED
You do not have permission to view this page.
[ Return to Dashboard ]

NOT FOUND
The requested page or record does not exist or is no longer available.
[ Return to Dashboard ]

SERVER UNAVAILABLE
TAGAME could not reach the local server.
Check that the CasaOS application is running and that this device is on the local network.
[ Retry ]

UNEXPECTED ERROR
Something went wrong. No sensitive diagnostic data is shown.
Reference: safe correlation identifier
[ Retry ] [ Return to Dashboard ]
```

Not found and Access denied remain distinct internally, but record-level authorization may intentionally use a neutral response when revealing existence would leak information.

---

## Primary end-to-end flows

### Register a game

```text
Dashboard quick action or Games
  -> Register game
  -> validate name and Steam AppID
  -> optionally configure executable detection and cover
  -> save
  -> Game details
```

### Register and assign an NTAG213

```text
Tags -> Register tag
  -> generate or validate opaque UUID
  -> friendly name
  -> select active game
  -> select active PC with eligible agent
  -> review
  -> create tag and active assignment
  -> expose UUID for physical write
  -> optional end-to-end test
```

### Pair a reader

```text
Readers -> Pair reader
  -> name, platform, owner
  -> create short-lived pairing code or QR
  -> reader exchanges code
  -> raw credential issued once to reader
  -> admin sees confirmation, never the raw credential
```

### Pair a PC agent

```text
PCs -> Register PC
  -> name and hostname
  -> create short-lived pairing code
  -> code entered in Windows agent
  -> server validates target PC
  -> credential issued once to agent
  -> first heartbeat confirms online state
```

### Start a game

```text
reader submits authenticated opaque tag token
  -> received
  -> validate device, tag, assignment, game, PC, and agent availability
  -> dispatch constrained start event
  -> agent acknowledges
  -> launch requested through configured Steam AppID
  -> session starting
  -> agent confirms process
  -> command completed + session running
```

### Stop the same game

```text
same tag tapped while its session is starting or running
  -> validate current assignment and session
  -> dispatch constrained stop event
  -> agent acknowledges
  -> session stopping
  -> agent confirms process stopped
  -> command completed + session stopped
```

### Different game already running on target PC

```text
new start request
  -> detect another active game on same PC
  -> reject under current default policy
  -> preserve existing session
  -> record rejected event with safe reason
  -> show user that the current game must be stopped first
```

### Offline-agent handling

```text
request reaches validation with offline or stale target agent
  -> do not claim dispatch or success
  -> reject immediately when offline state is authoritative
     OR time out when a recently online agent fails to respond
  -> record terminal event
  -> show affected PC, last heartbeat, safe reason, and eligible recovery action
```

### Review history

```text
Dashboard recent item or Activity
  -> filtered Events or Sessions list
  -> select record
  -> inspect command timeline and separate session lifecycle
  -> use permitted recovery action when available
```

---

## Approved remaining decisions

1. Initial-administrator recovery uses a recovery code displayed once for secure offline storage, with a local CasaOS recovery procedure as the physical-access fallback. The code is treated as a secret and is never redisplayed.
2. Guests see only a summary of their own reader devices in Profile.
3. Complex creation, assignment, pairing, and recovery flows use full pages. Simple record edits may use desktop drawers; narrow/mobile layouts always use full pages.
4. Android and iOS reader feedback distinguishes immediate acknowledgement from the final result and continues following the request until `running`, `stopped`, `error`, or `timeout`.
5. Database restoration requires administrator reauthentication, a pre-restore backup, an impact summary, and typed confirmation before the destructive step.

No unresolved webapp UX questions remain for `UX001`. Detailed security requirements for recovery-code generation, storage, hashing, rotation, and invalidation belong to the authentication implementation task and must preserve the approved experience.
