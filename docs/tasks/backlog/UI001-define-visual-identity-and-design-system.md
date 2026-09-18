# UI001 — Define Visual Identity and Design System

Status: blocked
Area: UI
Owner: —
Dependencies: UX001

## Objective

Define a complete, coherent, accessible, and implementation-ready visual language for TAGAME after the information architecture and low-fidelity wireframes are approved. This task must remove visual ambiguity before frontend implementation by specifying colors, typography, spacing, geometry, responsive behavior, component appearance, interaction states, status communication, and design tokens.

The result must be precise enough for a frontend agent to implement the approved UI without inventing visual rules during development.

## Required inputs

- `PROJECT_VISION.md`
- `ARCHITECTURE.md`
- `RULES.md`
- `CURRENT_STATE.md`
- `UX.md`
- Approved outputs from `UX001`:
  - `docs/wireframes/WEBAPP.md`
  - `docs/wireframes/PC_AGENT.md`

## Product surfaces covered

- CasaOS web application on desktop.
- CasaOS web application on narrow/mobile screens.
- Authentication and onboarding screens.
- Administrator and guest experiences.
- Dashboards, forms, tables, timelines, detail pages, settings, and diagnostics.
- C#/.NET PC-agent setup/status window where custom styling is appropriate.
- Status feedback used by Android and iOS reader integrations.

The Windows tray menu itself may follow native Windows behavior, but its icon, labels, statuses, and related agent window must remain consistent with the TAGAME visual language.

## Scope

### Visual direction

- Define the intended product personality and visual mood.
- Define principles such as clarity, density, calmness, playfulness, technical character, and gaming influence.
- Decide how strongly the Steam/gaming context should influence the interface without copying Steam branding.
- Define visual consistency between the webapp and PC agent.

### Color system

- Decide whether TAGAME initially supports light theme, dark theme, or both.
- Define primary, secondary, neutral, surface, border, and overlay colors.
- Define semantic colors for success, information, warning, error, offline, timeout, rejected, disabled, and active/running states.
- Define foreground/background pairs for every semantic color.
- Define hover, pressed, focus, selected, disabled, and loading variants.
- Ensure that status meaning never depends on color alone.
- Document exact values in implementation-neutral tokens, including HEX and a second useful representation such as RGB or HSL.

### Typography

- Select approved font families and safe fallback stacks.
- Confirm license and web-distribution suitability for any non-system font.
- Define font weights, sizes, line heights, and letter spacing.
- Define a consistent type scale for display, page title, section heading, card title, body, label, caption, table, code, UUID, Steam AppID, and diagnostic content.
- Define truncation, wrapping, and monospace usage rules.

### Spacing and layout

- Define the base spacing unit and complete spacing scale.
- Define page gutters, content widths, grid behavior, section gaps, form gaps, and component padding.
- Define responsive breakpoints without tying them prematurely to a specific CSS framework.
- Define desktop sidebar/header dimensions and narrow-screen navigation behavior.
- Define table-to-card adaptation rules for narrow screens.
- Define density expectations for administrative and diagnostic screens.

### Geometry and depth

- Define border widths and styles.
- Define corner-radius scale.
- Define shadows/elevation levels and when each is permitted.
- Define separators, dividers, overlays, and modal backdrops.
- Avoid decorative depth that reduces readability or status clarity.

### Iconography and imagery

- Define the icon style, stroke/fill approach, standard sizes, and alignment.
- Define when text labels are mandatory alongside icons.
- Define placeholder and empty-state illustration guidance, if illustrations are approved.
- Define application icon requirements for the webapp and Windows tray agent.
- Do not select copyrighted game art as default product imagery.

### Component specifications

Define appearance, anatomy, sizing, variants, states, and usage guidance for at least:

- buttons and icon buttons;
- text inputs, password inputs, text areas, selects, and search;
- checkboxes, radio buttons, switches, and segmented controls;
- form labels, help text, validation messages, and required indicators;
- navigation sidebar, header, breadcrumbs, tabs, and mobile navigation;
- cards, stat cards, status cards, and summary panels;
- tables, responsive record summaries, pagination, sorting, and filters;
- badges and status indicators;
- alerts, banners, toasts, and inline feedback;
- modals, confirmation dialogs, drawers, dropdowns, and context menus;
- skeletons, spinners, progress indicators, and pending states;
- empty, offline, rejected, timeout, permission-denied, and unexpected-error states;
- timelines for command events and game sessions;
- tooltips and copy-to-clipboard feedback;
- destructive-action confirmation;
- onboarding checklist and pairing-code/QR presentation.

Every interactive component must document default, hover, focus-visible, active/pressed, selected, disabled, loading, success, warning, and error states where applicable.

### Status language

Define a consistent visual treatment for:

- `received`;
- `validated`;
- `dispatched`;
- `agent_acknowledged`;
- `launch_requested`;
- `stop_requested`;
- `completed`;
- `rejected`;
- `error`;
- `timeout`;
- `starting`;
- `running`;
- `stopping`;
- `stopped`;
- agent online, degraded, and offline.

Command-delivery status and actual game-session status must remain visually distinguishable.

### Accessibility

- Meet WCAG 2.2 AA contrast expectations for text and meaningful UI components.
- Target at least 4.5:1 contrast for normal text and 3:1 for large text and meaningful graphical/UI boundaries where applicable.
- Define visible keyboard focus treatment.
- Do not use color as the only status or validation signal.
- Define minimum practical pointer/touch target sizes.
- Support keyboard navigation, zoom, text scaling, and reduced-motion preferences in the specification.
- Define motion duration and easing conservatively; essential information must not depend on animation.

### Design tokens

Define implementation-neutral token names and values for:

- colors;
- typography;
- spacing;
- sizing;
- borders;
- radii;
- shadows/elevation;
- opacity;
- motion duration and easing;
- responsive breakpoints;
- z-index/layering.

Token names must describe purpose rather than a literal appearance when semantic meaning is intended. For example, prefer `color.status.error.background` over `color.red.500` in component contracts.

## Out of scope

- Production frontend implementation, CSS, C#, or component code.
- Selecting a frontend framework solely because its default theme resembles the specification.
- Changing approved UX flows, database behavior, permissions, or business rules without owner approval.
- Creating final marketing assets, promotional illustrations, or a complete brand campaign.
- Copying Steam, Windows, CasaOS, or another product's proprietary visual identity.
- High-fidelity native Android or iOS application design; only shared status feedback is covered.

## Deliverables

- `docs/UI.md` containing visual direction, accessibility rules, responsive principles, and design-system governance.
- `docs/design-system/COLORS.md` containing approved palettes, semantic mappings, state variants, and contrast results.
- `docs/design-system/TYPOGRAPHY.md` containing font choices, fallbacks, type scale, weights, line heights, and usage rules.
- `docs/design-system/TOKENS.md` containing spacing, sizing, borders, radii, shadows, motion, breakpoints, layering, and token naming.
- `docs/design-system/COMPONENTS.md` containing component anatomy, variants, states, and usage guidance.
- `docs/design-system/EXAMPLES.md` showing the system applied to representative approved wireframes: dashboard, data table, form, event timeline, error/offline state, onboarding/pairing, and PC-agent window.
- An explicit list of unresolved UI questions requiring project-owner approval.

## Allowed files

- `docs/UI.md`
- `docs/design-system/COLORS.md`
- `docs/design-system/TYPOGRAPHY.md`
- `docs/design-system/TOKENS.md`
- `docs/design-system/COMPONENTS.md`
- `docs/design-system/EXAMPLES.md`
- Approved UX wireframe documents only when annotations are necessary and do not alter an approved flow.
- `docs/CURRENT_STATE.md` only for decisions explicitly approved during this task.
- This task file, `docs/TASKS.md`, `.agent/CURRENT_TASK.md`, and the task archive as required by the task workflow.

## Required decision process

1. Review all approved UX outputs before proposing visual choices.
2. Present materially different visual directions to the project owner before finalizing the system.
3. Record the owner's approved direction and rejected alternatives with concise rationale.
4. Build the color, typography, spacing, and component systems from the approved direction.
5. Validate accessibility and responsive behavior.
6. Apply the system to representative wireframes.
7. Obtain final project-owner approval before marking `UI001` complete.

The agent must not silently choose subjective brand-defining decisions when multiple materially different directions are viable.

## Acceptance criteria

- The theme decision is explicit: light, dark, or both, with a documented rationale.
- Every approved color has a token name, exact value, intended usage, and valid foreground/background relationship.
- Contrast validation is recorded for primary text, muted text, links, controls, focus indicators, and semantic statuses.
- Typography includes licensed fonts or approved system fonts, fallbacks, weights, sizes, and line heights.
- A complete spacing scale and responsive layout rules are defined.
- Borders, radii, shadows, layering, and motion are consistently specified.
- Every required component has documented variants and relevant interaction states.
- Command and session statuses are visually distinct and understandable without color alone.
- Desktop and narrow/mobile behavior is defined for navigation, forms, tables, dashboards, and timelines.
- The webapp and PC-agent window feel related while respecting Windows tray conventions.
- Representative examples demonstrate that the system works with the approved wireframes.
- No implementation agent needs to invent a color, font size, spacing value, component state, or breakpoint for the `F001` scope.
- Open questions are resolved or explicitly accepted as blockers by the project owner.

## Verification

- Check every deliverable for internal token and naming consistency.
- Validate documented color pairs with a recognized WCAG contrast checker.
- Trace components and states against `UX.md` and approved wireframes.
- Verify admin, guest, desktop, mobile, offline, timeout, rejected, and error scenarios.
- Verify PC-agent setup, connected/disconnected state, and tray-related guidance.
- Confirm no raw credentials, arbitrary command controls, or client-supplied Steam AppID actions appear in examples.
- Review the complete design system with the project owner before marking the task complete.

