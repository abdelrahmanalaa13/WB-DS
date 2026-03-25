# Notification Center

Centralized notification inbox panel with categorization and actions.

- Layer: L6 (composed)
- Dependencies: Panel (L2), Button (L1), Icon Button (L1), Avatar (L1), Badge (L1), Chip (L1), Divider (L1), Tabs (L4), List (L5), Loading (L5)
- Status: pending
- Sprint: E

## Anatomy

- Trigger: bell icon button with unread count badge
- Panel: slide-in panel from right (or dropdown on desktop)
- Header: title + "Mark all read" action + settings link
- Filter tabs: All, Unread, Mentions, System
- Notification item: icon/avatar + title + description + timestamp + read/unread dot + action buttons
- Footer: "View all" link to full notification page
- Empty state: per-tab empty message

## Variants

- Display: dropdown panel (desktop), full-screen sheet (mobile)
- Notification types: info, success, warning, error (color-coded left border)
- States: unread (bold title + dot), read (normal weight), actionable (with CTA buttons)
- Grouping: by date (Today, Yesterday, Earlier), by source

## Motion

Panel open: slide from right `--motion-normal` + `--ease-decelerate`. New notification: fade-in at top `--motion-fast`. Mark read: dot fade out `--motion-fast`. Dismiss: slide out left `--motion-fast`.

## Viewport

Desktop: 400px panel dropdown or slide-in from right edge.
Mobile: full-screen bottom sheet with swipe-to-dismiss per notification.

## Valid Tokens

`--surface-raised` (panel bg), `--shadow-lg` (panel shadow), `--surface-sunken` (unread item bg), `--primary-filled-bg` (unread dot), `--danger-outline-border` (error notification border), `--warning-outline-border` (warning border), `--success-outline-border` (success border), `--content-primary` (title), `--content-secondary` (description), `--content-tertiary` (timestamp), `--border-subtle` (item dividers), `--space-3` (item padding), `--radius-lg` (panel)

## Forbidden

- Never auto-dismiss notifications from the center. Only Toasts auto-dismiss.
- Unread count badge must sync with actual unread state in real-time.
- Notification actions must be reachable by keyboard.
- Swipe-to-dismiss on mobile must have undo (Snackbar pattern).
