# TAGAME — Design Tokens

Status: approved under completed task `UI001`. CSS mirror: `tagame-server/frontend/src/styles/tokens.css`.

## Naming

Tokens use the `--tg-` prefix. Primitive names describe a palette or scale; semantic names describe intent. Components consume semantic names.

```text
--tg-color-cyan-400                 primitive
--tg-color-action-primary-bg         semantic
--tg-space-4                         scale
--tg-size-control-md                 component sizing contract
```

## Spacing scale

Base unit: `0.25rem` (4px at the default root size).

| Token | Value |
|---|---:|
| `space-1` | 0.25rem |
| `space-2` | 0.5rem |
| `space-3` | 0.75rem |
| `space-4` | 1rem |
| `space-5` | 1.25rem |
| `space-6` | 1.5rem |
| `space-8` | 2rem |
| `space-10` | 2.5rem |
| `space-12` | 3rem |
| `space-16` | 4rem |

Use `space-2` for compact inline gaps, `space-3` for form label-to-control gaps, `space-4` for standard component padding, `space-6` for card and section padding, and `space-8` or higher between major page regions.

## Layout and breakpoints

| Token | Value | Contract |
|---|---:|---|
| `size-sidebar` | 16rem | Persistent desktop navigation width |
| `size-header` | 4rem | App header height |
| `size-content-max` | 90rem | Maximum content canvas width |
| `size-page-gutter-mobile` | 1rem | Narrow-screen page inset |
| `size-page-gutter-desktop` | 2rem | Desktop page inset |
| `breakpoint-sm` | 36rem | Compact layout begins expanding |
| `breakpoint-md` | 48rem | Two-column content may begin |
| `breakpoint-lg` | 64rem | Persistent sidebar becomes available |
| `breakpoint-xl` | 80rem | Wide dashboard/table refinements |

Breakpoints are documented tokens for implementation reference. Use the values directly in media queries; CSS custom properties cannot currently be used as portable media-query parameters.

## Controls and icons

| Token | Value | Use |
|---|---:|---|
| `size-control-sm` | 2.25rem | Compact desktop controls only |
| `size-control-md` | 2.75rem | Default controls and mobile-safe touch target |
| `size-control-lg` | 3.25rem | High-emphasis access/onboarding action |
| `size-icon-sm/md/lg` | 1rem / 1.25rem / 1.5rem | Inline / standard / prominent icons |

## Geometry and elevation

- Borders: `hairline` is 1px; `strong` is 2px for selected or high-attention states.
- Radius: default `radius-md` (0.25rem); `radius-lg` for dialogs and drawers; pills only for compact badges and filters.
- Clipped corners are an optional visual treatment for selected feature panels. They must not alter focus rings, text clipping, or hit targets.
- `shadow-1` is for menus and raised cards; `shadow-2` is for modal/drawer elevation; ordinary cards use borders without shadow.
- `shadow-focus` is the shared focus ring; never remove it without an equally visible replacement.

## Motion and layers

| Category | Token | Value |
|---|---|---:|
| Instant | `duration-instant` | 0ms |
| Fast | `duration-fast` | 120ms |
| Standard | `duration-normal` | 180ms |
| Slow | `duration-slow` | 280ms |
| Base | `z-base` | 0 |
| Sticky | `z-sticky` | 100 |
| Dropdown | `z-dropdown` | 200 |
| Drawer | `z-drawer` | 300 |
| Modal | `z-modal` | 400 |
| Toast | `z-toast` | 500 |
| Tooltip | `z-tooltip` | 600 |

Use motion only to clarify a state change: opening a drawer, expanding a disclosure, confirming a completed action, or presenting a pending action. No looping glitch, flashing text, or essential animated indicator is permitted. `prefers-reduced-motion` changes motion durations to zero in `tokens.css`.
