# TAGAME — Representative Applications

Status: approved under completed task `UI001`. These are visual contracts applied to approved UX wireframes; they do not change navigation or business behavior.

## Dashboard

```text
Canvas
├── Sidebar: subtle surface, active item with cyan rail + text weight
├── Attention banner: semantic warning/error with icon and Review action
├── Current session panel: raised surface; display heading; session badge
│   ├── Session: Running  [green icon + label]
│   └── Delivery: Completed [cyan rail + label]
├── Agent health panel: online/degraded/offline status cards
└── Recent events table: technical columns in mono; compact row summaries on mobile
```

The active session uses a clipped-corner accent only at its outer panel. It never turns a `Delivery: completed` label into a `Session: running` label.

## Data table

```text
Page title + primary action
Filters: search, status, date or entity selectors
Table: caption, sortable headers, status badge, row action
Mobile: record card with title, state, primary facts, View details
```

Tables use `body-small` text at minimum. Dense metadata uses mono only for technical values. Filtering and sorting are accessible controls, never placeholder-only inputs.

## Form

```text
Section title
Label (required indicator when applicable)
Control
Help text
Inline validation error

[Secondary]                         [Primary action]
```

First-time setup uses a quiet access-page surface; the final action uses the cyan primary button. Password requirements are text requirements, not only a meter. A server error is a page-level alert separate from field validation.

## Event timeline

```text
Delivery
● Received             20:30:01
● Validated            20:30:01
● Agent acknowledged   20:30:02
✓ Completed            20:30:03 — Running confirmed

Session
◌ Starting             20:30:02
● Running              20:30:03
```

The two headings, icon shapes, and semantic token families maintain the distinction even for users who cannot perceive color.

## Offline / error state

```text
[offline icon] TAGAME server unavailable
The local server cannot be reached. Dashboard data may be outdated.
[Retry] [Open diagnostics]
```

Do not show stale data as current. Partial panel failure keeps healthy dashboard panels visible while marking the failed panel with contextual retry.

## Onboarding and pairing

```text
Getting started — 2 of 7 complete
✓ Administrator created
→ Pair PC agent                         [Pair PC]

Pair reader — code expires in 04:32
731 492
[Copy code] [Show QR]
```

Numbers use JetBrains Mono. The code is temporary, paired with expiry and manual-entry support. Long-lived credentials are never displayed.

## PC-agent window

```text
TAGAME Agent — Gaming PC
[online icon] Connected
Current game: Elden Ring
Session: Running
Delivery: Completed
Last heartbeat: 8 seconds ago

[Open TAGAME web app] [Diagnostics]
```

Use the same typography roles and semantic status colors as the webapp, but retain native Windows controls, menu behavior, and accessibility conventions. The tray icon is simple, readable at small sizes, and adds a restrained activity indicator only when a game is running.
