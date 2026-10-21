# TAGAME — Typography

Status: approved under completed task `UI001`. CSS mirror: `tagame-server/frontend/src/styles/tokens.css`.

## Approved families

| Role | Family | Fallback | License/distribution |
|---|---|---|---|
| Display | Oxanium | Rajdhani, Arial Narrow, sans-serif | SIL Open Font License 1.1; self-host WOFF2 files and retain `OFL.txt`. |
| Functional UI | Inter | Segoe UI, Arial, sans-serif | SIL Open Font License 1.1; self-host WOFF2 files and retain `OFL.txt`. |
| Technical data | JetBrains Mono | Cascadia Mono, Consolas, monospace | SIL Open Font License 1.1; self-host WOFF2 files and retain `OFL.txt`. |

Only these three type roles are loaded. Oxanium is a display face, not a form or table face.

## Type scale

| Role | Family | Size / line-height | Weight | Letter spacing | Use |
|---|---|---|---:|---:|---|
| Display | Display | `2.25rem / 1.15` | 700 | `0.04em` | Login title and rare high-level hero text |
| Page title | Display | `1.875rem / 1.15` | 600 | `0.04em` | Page heading |
| Section title | Display | `1.5rem / 1.3` | 600 | `0.04em` | Major dashboard section |
| Card title | Sans | `1.125rem / 1.3` | 600 | normal | Card and dialog title |
| Body | Sans | `1rem / 1.5` | 400 | normal | Default reading text |
| Body small | Sans | `0.875rem / 1.5` | 400 | normal | Table cells and help text |
| Label | Sans | `0.75rem / 1.3` | 600 | `0.08em` | Form labels and micro-headings; uppercase only when brief |
| Caption | Sans | `0.6875rem / 1.3` | 500 | `0.08em` | Secondary metadata only |
| Technical | Mono | `0.875rem / 1.5` | 400 | normal | UUID, AppID, event IDs, timestamps |

Use sentence case for body text, button labels, form labels, and status explanations. Uppercase is reserved for short labels and compact technical headings. Never render paragraphs or error explanations in uppercase.

## Technical data rules

- UUIDs, AppIDs, correlation IDs, timestamps, hostnames, and diagnostic values use the mono family.
- UUIDs may wrap at hyphen boundaries on narrow screens; provide a dedicated copy action for values that must be copied.
- Never truncate a security-relevant or copyable identifier without an inspect/copy path.
- Long game titles and device names truncate with an ellipsis in dense lists, while the complete value is available in the detail view or tooltip.
- Numeric columns use tabular numerals where the chosen font provides them.

## Loading and fallbacks

- Self-host font files inside the frontend asset tree; do not require a CDN or external network at runtime.
- Use `font-display: swap` when the frontend registers the fonts.
- The system fallback stacks preserve a functional interface before custom fonts load.
- Browser zoom and user font-size preferences must not be blocked by fixed pixel sizing.
