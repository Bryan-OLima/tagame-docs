# TAGAME Documentation

TAGAME is a local-first NFC game launcher hosted on CasaOS. This repository is the public documentation and case-study repository for the project. The implementation is maintained in separate `tagame-server` and `tagame-agent` repositories.

## Physical and deployment structure

The initial deployment is designed around three parts:

```text
NFC tag
   ↓
Android or iOS phone
   ↓ local network request
Notebook running CasaOS
   ↓ authenticated launch/stop event
PC running Steam and the TAGAME agent
```

- **Phone:** reads the NFC tag and sends an authenticated request to TAGAME.
- **Notebook with CasaOS:** hosts the TAGAME web application, API, event dispatcher, logs, and database.
- **PC:** runs Steam, receives validated events through the TAGAME agent, and launches or stops the configured game.

The phone is the NFC reader, the notebook is the local server, and the PC is the execution machine. They may be separate physical devices on the same home network.

## Technology decisions

### Database: SQLite

TAGAME will use **SQLite** for the initial deployment.

SQLite is appropriate because the application is local-first, runs on one CasaOS notebook, receives a small number of NFC events, and does not need a separate database server. The database will be accessed by the TAGAME backend on the same machine; phones and PCs will communicate with the API, never directly with the database.

Initial database requirements:

- enable `journal_mode = WAL`;
- enable `foreign_keys = ON`;
- configure a write `busy_timeout`;
- use migrations from the first implementation;
- store internal UUIDs as `TEXT` in canonical 36-character form;
- store `steam_appid` as an integer external identifier;
- keep the database file on a local CasaOS volume, not a network filesystem;
- back up the database using a safe SQLite backup procedure, including any active WAL state.

PostgreSQL remains a possible future migration if TAGAME grows to require multiple application servers, substantially higher write concurrency, or direct database access by multiple machines. The logical relational model in `DB.md` must remain portable enough to support that evolution.

## Documentation map

All normative documents are under `docs/`:

| Document | Purpose |
|---|---|
| [`PROJECT_VISION.md`](docs/PROJECT_VISION.md) | Product purpose, goals, and vocabulary |
| [`ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Components, communication, and trust boundaries |
| [`UX.md`](docs/UX.md) | Information architecture, page inventory, flows, and UI states |
| [`DB.md`](docs/DB.md) | Database schema and relationships |
| [`RULES.md`](docs/RULES.md) | Business, security, and lifecycle rules |
| [`CURRENT_STATE.md`](docs/CURRENT_STATE.md) | Approved decisions and unresolved choices |
| [`ROADMAP.md`](docs/ROADMAP.md) | Planned delivery phases |
| [`TASKS.md`](docs/TASKS.md) | Current task index |
| [`COMMIT_CONVENTIONS.md`](docs/COMMIT_CONVENTIONS.md) | Commit message format |
| [`AGENTS.md`](docs/AGENTS.md) | Instructions for AI agents working on the project |

## Related repositories

- `tagame-server` — CasaOS application containing the web UI, API, database integration, and event dispatcher.
- `tagame-agent` — background execution bridge installed on target PCs.

## Initial technology direction

- **CasaOS server:** full-stack web application with frontend, backend, SQLite, migrations, and event dispatching.
- **PC agent:** C#/.NET Windows application with a simple Windows Forms configuration window and `NotifyIcon` tray interface.
- **Reader devices:** Android and iOS integrations that read NFC tags and submit authenticated requests.

The agent is expected to start with the user's Windows session, provide setup and connection status through the tray, and return to the tray after configuration. A separate Windows Service may be considered later if pre-login operation becomes necessary.

## NFC tag standard

TAGAME will use genuine **NXP NTAG213** tags in card or sticker form.

The NTAG213 is the preferred choice because it is a common NFC Forum Type 2 tag, works with the Android and iOS reader flows planned for TAGAME, is inexpensive, and provides the tag features we need: a stable hardware UID, NDEF storage, write locking, and optional write protection. The official NXP specification lists **144 bytes of user memory**, 10-year data retention, and up to 100,000 write cycles.

The 144-byte capacity is more than sufficient for TAGAME. A UUIDv4 in its canonical textual form uses 36 ASCII characters:

```text
550e8400-e29b-41d4-a716-446655440000
```

Even with the application prefix used by TAGAME, the payload is short:

```text
tagame:550e8400-e29b-41d4-a716-446655440000
```

This is approximately 43 bytes before the small NDEF record overhead, leaving substantial space within the 144-byte user area. The tag stores only this opaque identifier; the game name, Steam AppID, permissions, and execution rules remain in the CasaOS database.

NTAG215 and NTAG216 are not required for the initial project because their larger memory capacities would not provide a meaningful benefit for TAGAME. NTAG424 DNA may be evaluated later if strong anti-cloning authentication becomes a requirement, but it would add unnecessary cryptographic complexity for the initial local deployment.

Reference: [NXP NTAG213/215/216 product specification](https://www.nxp.com/products/NTAG213_215_216).

## Task organization

```text
tagame-docs/
├── README.md
└── docs/
    ├── PROJECT_VISION.md
    ├── AGENTS.md
    ├── TASKS.md
    ├── .agent/
    │   └── CURRENT_TASK.md
    └── tasks/
        ├── backlog/
        ├── active/
        └── archive/
            └── TASK_ARCHIVE.md
```

## Task identifiers

The prefix describes the work area, not whether the change is a feature or a fix:

```text
F001    Frontend
UX001   User experience and information architecture
UI001   Visual identity and design system
B001    Backend
DB001   Database
OPS001  DevOps and infrastructure
M001    Mobile and NFC reader integration
PC001   PC agent
QA001   Quality assurance and tests
DOC001  Documentation
SEC001  Security
```

The numeric part is never reused. A completed or cancelled task remains represented in the archive.

## Working on TAGAME

Agents must read `AGENTS.md`, choose an eligible task from `TASKS.md`, keep `.agent/CURRENT_TASK.md` updated, and follow the task's acceptance criteria. Humans can use `TASKS.md` and the archived history to understand what is planned and what has already been delivered.
