# Toast

Floating notification with stacking. More persistent than Snackbar.

- Layer: L2 (container)
- Dependencies: Button (L1), Icon Button (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: A

## Surface

Extends `ds-surface`. Variant: elevated. Accent strip on leading edge for role color.

## Variants

- Roles: danger, warning, success, neutral (info), ai
- With/without: icon, title, description, action button, dismiss X
- Auto-dismiss: configurable (default 5s, errors persistent)
- Stacking: max 3 visible, queue rest

## Slots

- Accent strip (4px leading edge, role color)
- Leading icon
- Title + description
- Action button
- Dismiss X

## Viewport

Desktop: fixed bottom-end, max-width 380px, stacks vertically.
Mobile: full-width bottom, stacks vertically, swipe-to-dismiss.

## Valid Tokens

`--surface-raised`, `--shadow-md`, `--{role}-filled-bg` (accent strip only), `--content-primary`, `--content-secondary`, `--motion-fast`, `--ease-spring`

## Forbidden

- Never use role color for entire toast background. Only the accent strip is colored.
- Toast surface is always neutral (elevated). Distinguish from Snackbar which IS role-colored.
