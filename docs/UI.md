# TAGAME — Visual Identity and Design System

Status: approved under completed task `UI001`.

## Approved direction

TAGAME uses a dark-only **cyberpunk operational** visual language: a controlled, high-clarity system console for a local NFC game launcher. It draws mood-level inspiration from futuristic game HUDs—dark fields, luminous technical accents, compact system readouts, and precise geometry—without copying Cyberpunk 2077, Steam, CasaOS, Windows, or other proprietary visual identities.

The experience should feel like a private home control station: fast, deliberate, technically capable, and calm under failure. The interface is not a game menu. Information, controls, and recovery must remain clear before decorative effects.

### Alternatives considered

| Direction | Decision | Rationale |
|---|---|---|
| Literal game-HUD reproduction | Rejected | Would risk imitation, distract from administration, and reduce accessibility. |
| Generic neutral SaaS dark theme | Rejected | Clear but insufficiently distinctive for TAGAME's local gaming purpose. |
| Cyberpunk operational | Approved | Delivers a strong identity while protecting hierarchy, readability, and diagnostic clarity. |

## Design principles

1. **State before spectacle.** Current session, command result, and recovery action take priority over ornament.
2. **Operational contrast.** Text, controls, focus, and meaningful borders meet WCAG 2.2 AA targets.
3. **Technical, not noisy.** Fine grid, scan, or data-line decoration is optional and never competes with content.
4. **One primary action.** Each form, dialog, and guided step has a single obvious next action.
5. **Visible distinctions.** Delivery state, game session state, agent health, and destructive intent have different labels, icons, and treatments—not color alone.
6. **Dense by choice.** Administration screens are compact on desktop, but never reduce text below the documented type scale or controls below the documented target size.
7. **Local-first trust.** The UI explains local server failures honestly and never treats a request acknowledgement as a confirmed game launch.

## Theme policy

TAGAME launches with **one dark theme only**. Supporting a light theme before it is designed and contrast-tested would dilute the implementation contract. The neutral and semantic token structure permits a future light theme without component rewrites.

## Visual grammar

- Canvas: near-black blue/green with restrained dark surfaces.
- Accent: cyan is reserved for primary action, interactive focus, and command-delivery context.
- Status: green, yellow, red, orange, and neutral gray carry semantic meaning only alongside icon and text.
- Geometry: compact radii; selected high-value panels may use a single clipped corner through a component utility, never by altering content hit targets.
- Borders: 1px technical separators are preferred over heavy shadows.
- Depth: use elevation sparingly for drawers, menus, modals, and focused content.
- Decoration: optional, static, low-contrast lines or grid marks stay outside readable text and controls. No copyrighted game art is used as default imagery.

## Surface guidance

| Surface | Intended use |
|---|---|
| Canvas | Application background and access-page backdrop. |
| Subtle | Large app shell regions and quiet sections. |
| Surface | Cards, standard panels, form groups, and tables. |
| Raised | Menus, drawers, hoverable cards, and nested focus regions. |
| Overlay | Modal and mobile-drawer scrim only. |

## Responsive principles

- Desktop administration begins at `64rem` and uses a persistent sidebar.
- Below `64rem`, navigation becomes a compact header plus drawer.
- Below `48rem`, dashboards use one column; table records become stacked summaries with the record details on a dedicated screen.
- Page gutters use `1rem` on narrow screens and `2rem` on desktop.
- Do not hide critical state to fit a small screen; move it earlier in the reading order.
- Forms stack labels and fields; paired actions wrap rather than shrink below target sizes.

## Accessibility contract

- Normal text and control text: minimum 4.5:1 contrast.
- Large text and meaningful graphical/UI boundaries: minimum 3:1 contrast.
- Focus is always visible using a cyan outer ring and does not rely on hover.
- Pointer/touch targets: minimum 44 × 44 CSS pixels for standalone controls; dense table-row controls must provide an equivalent reachable target.
- All icon-only controls require an accessible name and tooltip.
- Status uses icon + label + text; color is supplementary.
- Support keyboard navigation, 200% zoom, text resizing, and `prefers-reduced-motion`.
- Motion is non-essential, no longer than 280ms, and disabled by reduced-motion preference.

## Governance

- `docs/design-system/*.md` defines the implementation-neutral contract.
- `tagame-server/frontend/src/styles/tokens.css` mirrors approved tokens as CSS custom properties.
- Components must consume semantic tokens; they must not introduce literal colors, spacing values, or one-off breakpoints.
- A new visual token requires documentation in `TOKENS.md`, a CSS mirror, accessibility validation where color is meaningful, and an explicit usage reason.
- Experimental decorative effects are feature-level enhancements and must have a no-motion/no-decoration fallback.

## Open UI questions

None at this stage. The direction, dark-theme policy, token strategy, and type roles were approved by the project owner before `UI001` began.
