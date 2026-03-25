# Dialog

Modal overlay requiring user action before continuing.

- Layer: L2 (container)
- Dependencies: Button (L1), Icon Button (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: A

## Surface

Extends `ds-surface`. Variant: elevated. Always has backdrop (`--surface-overlay`).

## Variants

- Density: compact, default, comfortable
- Dismissible: yes (close X) / no (must choose action)
- Scrollable: yes/no (for long content)
- Types: alert (title + message + actions), confirmation (destructive, danger role), form (inputs inside)

## Slots

- `ds-dialog-header`: title + optional close button
- `ds-dialog-content`: scrollable content area
- `ds-dialog-footer`: action buttons (end-aligned, primary rightmost)

## Viewport

Desktop: centered modal with backdrop, max-width 560px.
Mobile (compact): centered modal. Mobile (large/form): bottom sheet or full-screen.

## Valid Tokens

`--surface-raised`, `--shadow-xl`, `--surface-overlay` (backdrop), `--motion-normal`, `--ease-decelerate` (enter), `--motion-fast`, `--ease-accelerate` (exit)

## Forbidden

- Never allow interaction with content behind dialog while open.
- Focus must be trapped inside dialog.
- ESC key must close dismissible dialogs.
