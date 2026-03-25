# Alert

Inline status message within content flow.

- Layer: L2 (container)
- Dependencies: Button (L1), Icon Button (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: A

## Surface

Extends `ds-surface` with status surface. Class: `ds-surface ds-alert status-{emphasis} {role}`.

## Variants

- Emphasis: tonal (default), filled, outlined
- Roles: danger, warning, success, neutral (info), ai
- Dismissible: yes/no
- With/without: icon, title, description, action button

## Slots

- Leading icon (role-specific default icon)
- Title (optional, bold)
- Description (body text)
- Action button (ghost emphasis, same role color)
- Dismiss button (trailing X)

## Viewport

No change. Alert is inline, flows with content on all viewports.

## Valid Tokens

`--{role}-tonal-bg`, `--{role}-tonal-fg`, `--{role}-filled-bg`, `--{role}-filled-fg`, `--{role}-outline-fg`, `--{role}-outline-border`, `--radius-md`

## Forbidden

- Never use Alert for floating/positioned messages. Use Snackbar or Toast.
- Never use `primary` role for alerts. Primary is for brand actions, not status.
- Alert is always inline. Never position: fixed/absolute.
