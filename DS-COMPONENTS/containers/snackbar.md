# Snackbar

Brief floating message with optional action. Auto-dismisses.

- Layer: L2 (container)
- Dependencies: Button (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: A

## Surface

Extends `ds-surface` with status surface. Emphasis: filled.

## Variants

- Roles: danger, warning, success, neutral (info)
- With/without: action button, dismiss X
- Auto-dismiss: 5s for info/success, persistent for danger

## Slots

- Leading icon (optional)
- Message text
- Action button (optional, ghost emphasis, inverse color)
- Dismiss X (optional)

## Viewport

Desktop: fixed bottom-start, max-width 400px.
Mobile: full-width bottom, safe-area-inset-bottom.

## Valid Tokens

`--{role}-filled-bg`, `--{role}-filled-fg`, `--motion-fast`, `--ease-spring` (enter), `--shadow-lg`

## Forbidden

- Never use for persistent messages. Use Alert (inline) or Toast (stacking).
- Max 1 snackbar visible at a time. New snackbar replaces current.
- Never use `primary` or `ai` roles. Snackbar is for status feedback.
