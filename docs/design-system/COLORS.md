# TAGAME — Colors

Status: approved under completed task `UI001`. CSS mirror: `tagame-server/frontend/src/styles/tokens.css`.

## Palette

| Token family | HEX | RGB | Use |
|---|---:|---:|---|
| `neutral-950` | `#050A0E` | `5, 10, 14` | Canvas |
| `neutral-850` | `#101820` | `16, 24, 32` | Standard surface |
| `neutral-800` | `#151F28` | `21, 31, 40` | Raised surface |
| `neutral-700` | `#25343F` | `37, 52, 63` | Subtle border |
| `neutral-400` | `#91A6AD` | `145, 166, 173` | Muted text |
| `neutral-100` | `#EDF8F8` | `237, 248, 248` | Primary text |
| `cyan-400` | `#35E7F0` | `53, 231, 240` | Primary action |
| `cyan-300` | `#76F5FA` | `118, 245, 250` | Focus and links |
| `green-400` | `#49E797` | `73, 231, 151` | Positive primitive |
| `yellow-400` | `#F4D44D` | `244, 212, 77` | Warning primitive |
| `orange-300` | `#FFBE77` | `255, 190, 119` | Timeout primitive |
| `red-400` | `#FF6B70` | `255, 107, 112` | Error primitive |

Component code must use the semantic roles in `tokens.css`, not primitive color names.

## Semantic mappings

| Meaning | Background | Border | Foreground | Required non-color cue |
|---|---|---|---|---|
| Success | `#0C2B20` | `#1F9B5D` | `#8EF5B9` | Check icon + `Success`/outcome label |
| Information | `#062B33` | `#12BDCB` | `#76F5FA` | Info icon + description |
| Warning | `#30270A` | `#B8921E` | `#FFE27A` | Warning icon + action or cause |
| Error | `#361419` | `#E74850` | `#FF9B9F` | Error icon + safe reason |
| Offline | `#202833` | `#65717E` | `#D1D8E0` | Offline icon + last-known state |
| Timeout | `#34220C` | `#D98432` | `#FFBE77` | Clock icon + retry/inspection guidance |
| Rejected | `#35131E` | `#BF4771` | `#FFB0C8` | Block icon + rule-based reason |
| Disabled | `#1B252C` | `#40525E` | `#BED0D4` | Disabled label or unavailable explanation |

## Interaction colors

| Role | Default | Hover | Pressed | Foreground |
|---|---|---|---|---|
| Primary action | `#35E7F0` | `#76F5FA` | `#12BDCB` | `#00191C` |
| Secondary action | transparent | `#142B33` | `#1B3943` | `#76F5FA` |
| Destructive action | `#E74850` | `#FF6B70` | `#A72E3B` | `#230609` |
| Link | transparent | transparent | transparent | `#76F5FA` → `#C6FBFD` |

Disabled controls use `--tg-opacity-disabled` and retain an accessible explanation when the reason is not obvious.

## Operational state language

### Command delivery

Command status has a `Delivery` prefix and uses cyan structural cues—timeline rail, icon container, and label—rather than being confused with game process state.

| Status | Treatment |
|---|---|
| `received`, `validated`, `dispatched`, `agent_acknowledged`, `launch_requested`, `stop_requested` | Information treatment; show explicit event label and timestamp. |
| `completed` | Success treatment; state the confirmed final outcome. |
| `rejected` | Rejected treatment; show safe rule reason. |
| `error` | Error treatment; show safe diagnostic reason. |
| `timeout` | Timeout treatment; never imply final game state. |

### Game session

Session state has a `Session` prefix and a distinct state icon/shape.

| Status | Foreground | Border | Required label |
|---|---:|---:|---|
| `starting` | `#76F5FA` | `#12BDCB` | `Session: starting` |
| `running` | `#8EF5B9` | `#1F9B5D` | `Session: running` |
| `stopping` | `#FFE27A` | `#B8921E` | `Session: stopping` |
| `stopped` | `#BED0D4` | `#40525E` | `Session: stopped` |
| `error` | `#FF9B9F` | `#E74850` | `Session: error` |

Agent health uses the same semantic family but always states `Agent: online`, `Agent: degraded`, or `Agent: offline`.

## Contrast validation

Ratios were calculated with the WCAG relative-luminance formula. All text pairs below meet or exceed 4.5:1; focus cyan exceeds 3:1 against the canvas.

| Pair | Ratio |
|---|---:|
| Primary text `#EDF8F8` on canvas `#050A0E` | 18.35:1 |
| Secondary text `#BED0D4` on surface `#101820` | 11.22:1 |
| Muted text `#91A6AD` on subtle `#0B1117` | 7.46:1 |
| Link/focus `#76F5FA` on canvas `#050A0E` | 15.35:1 |
| Primary-action foreground `#00191C` on `#35E7F0` | 11.97:1 |
| Destructive-action foreground `#230609` on `#E74850` | 4.94:1 |
| Success foreground on success background | 11.51:1 |
| Information foreground on information background | 11.57:1 |
| Warning foreground on warning background | 11.56:1 |
| Error foreground on error background | 8.22:1 |
| Offline foreground on offline background | 10.34:1 |
| Timeout foreground on timeout background | 9.34:1 |
| Rejected foreground on rejected background | 9.73:1 |

## Prohibited uses

- Do not use cyan purely decoratively near a primary action if it can be mistaken for a control.
- Do not use red as the selected-navigation color; selection is a structural state, not an error.
- Do not communicate `running`, `completed`, `rejected`, or `offline` by color alone.
- Do not put cyan, yellow, green, or red body text directly on transparent photographic or game-art backgrounds.
