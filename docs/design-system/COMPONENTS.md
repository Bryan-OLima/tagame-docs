# TAGAME — Components

Status: approved under completed task `UI001`.

Every interactive component supports keyboard operation, visible focus, disabled semantics, and a reduced-motion equivalent. Component styles use `tokens.css` semantic properties only.

## Buttons and icon buttons

| Variant | Anatomy | Use | States |
|---|---|---|---|
| Primary | Label; optional leading icon | One main action per region | default, hover, pressed, focus-visible, disabled, loading |
| Secondary | Label; optional leading icon; outlined or quiet treatment | Supporting action | default, hover, pressed, focus-visible, disabled, loading |
| Danger | Label; optional destructive icon | Confirmed destructive action only | default, hover, pressed, focus-visible, disabled, loading |
| Icon button | Accessible name + tooltip + icon | Compact familiar action only | same as secondary; never the sole destructive confirmation |

Default height is `size-control-md`. Loading retains the button label and prevents repeated activation. Do not use a spinner without a text label for primary actions.

## Form controls

| Component | Required anatomy | Guidance |
|---|---|---|
| Text/password input | Label, control, optional help, inline error | Password show/hide is an accessible button; errors describe correction. |
| Text area | Same as input; optional count | Use only for safe user-authored text, never shell commands. |
| Select/combobox | Label, selected value, trigger, listbox | Search when choice sets are large; keyboard selection required. |
| Search | Label or accessible name, search icon, clear action | Clearing restores the unfiltered state. |
| Checkbox | Target, visible label, optional help | Use for independent choices. |
| Radio | Group label, options, help | Use for one-of-many static choices. |
| Switch | Label with current state text | Use for immediate on/off settings, not destructive confirmation. |
| Segmented control | Group label, options | Use for 2–4 mutually exclusive view modes. |

Controls have default, hover, focus-visible, filled, validation-error, disabled, and loading/read-only states where applicable. Error is indicated by border, icon, message, and programmatic association—not border color alone.

## Navigation

- **Desktop sidebar:** 16rem; grouped labels; active item has text weight, left rail, and `aria-current` or equivalent.
- **Header:** 4rem; page title, contextual action, profile/menu controls.
- **Breadcrumbs:** show hierarchy on detail pages; the current page is non-link text.
- **Tabs:** use for peer content within a page, not primary navigation.
- **Mobile drawer:** focus-trapped while open; Escape closes it; focus returns to trigger.
- **Pagination:** state current page and total; never use icon-only pagination without labels.

## Panels, cards, and tables

| Component | Anatomy | Rules |
|---|---|---|
| Standard card | Optional eyebrow, title, content, footer action | Surface + subtle border; no shadow by default. |
| Stat/status card | Label, value/state, context, action | State includes icon and text. |
| Summary panel | Title, key facts, actions | Supports clipped-corner visual accent only when content remains rectangular. |
| Data table | Caption, filters, headers, rows, pagination | Sort has text/aria state; technical data uses mono. |
| Mobile record summary | Title, 2–4 key facts, state, detail link | Replaces wide table; do not force horizontal scrolling for primary fields. |

Rows that navigate are entire accessible links or explicit buttons; do not combine ambiguous row clicks with unrelated inline controls.

## Status, feedback, and loading

| Component | Required content | Behavior |
|---|---|---|
| Badge | Icon, short label, semantic treatment | Supplemental summary, never the sole explanation. |
| Inline feedback | Icon, message, correction/recovery | Lives near the affected control or operation. |
| Alert/banner | Title, safe explanation, optional action | Page-level failure, warning, or attention state. |
| Toast | Result + optional undo/view action | Does not carry critical information alone. |
| Skeleton | Structural placeholder | Used while data is genuinely loading; no fake status. |
| Spinner/progress | Label + determinate value when known | Used for actions or stages, not indefinite ambiguity. |
| Empty state | Meaningful absence, next valid action | Does not offer an action whose prerequisite is missing. |

Offline, timeout, rejected, permission-denied, and unexpected-error states follow the semantic table in `COLORS.md` and include a recovery action whenever one exists.

## Overlays and menus

- **Modal:** title, concise effect, primary/secondary actions, Escape behavior, focus trap, and return focus.
- **Confirmation dialog:** names the affected record and explains impact on future actions and historical records.
- **Drawer:** supporting desktop edit or contextual inspection; narrow/mobile uses a full page for complex flows.
- **Dropdown/context menu:** keyboard navigation, Escape close, and no critical action without an explicit confirmation.
- **Tooltip:** short supplemental help only; content is not required to understand a control.

## Timelines

### Command-delivery timeline

Use `Delivery` as the heading. Each event includes status label, timestamp, safe reason where terminal, and a cyan structural rail. `completed` adds outcome text such as `Running confirmed`; it does not replace the session panel.

### Game-session timeline

Use `Session` as the heading. States are `starting`, `running`, `stopping`, `stopped`, and `error`, with their own icon geometry and semantic tokens. A session can remain unknown after a command timeout; never infer `stopped` or `running` without agent confirmation.

## Copy-to-clipboard

Copyable UUIDs, AppIDs, and safe correlation IDs use mono text plus a labelled copy button. On success, show `Copied` in the control and a short live-region message. Never make a raw device or agent credential copyable in the administration UI.

## Pairing and onboarding

- **Onboarding checklist:** item state, prerequisite explanation, primary action, completion count, dismiss/reopen control.
- **Pairing code:** large mono digits, expiry, progress, cancel/restart behavior, and no long-lived credential display.
- **QR presentation:** text code alternative, expiry, and manual entry path.
- **Destructive re-pairing:** clearly states that the old credential is replaced or revoked.

## Component state matrix

| State | Minimum treatment |
|---|---|
| Default | Semantic color, label, and expected geometry |
| Hover | Background/border shift only; no layout movement |
| Focus-visible | `shadow-focus` plus focus border |
| Pressed | Short contrast shift; no essential animation |
| Selected | Structural marker, state text where relevant, and selected surface |
| Disabled | Native disabled semantics or `aria-disabled`; lower opacity plus explanation if non-obvious |
| Loading | Prevent duplicate action; retain label and expose busy state |
| Success/warning/error | Semantic treatment plus icon and explanatory text |
