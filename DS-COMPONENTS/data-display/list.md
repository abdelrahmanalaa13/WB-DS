# List

Structured vertical collection of related items.

- Layer: L5 (data display)
- Dependencies: Avatar (L1), Badge (L1), Icon Button (L1), Divider (L1), Chip (L1)
- Status: pending
- Sprint: D

## Variants

- Types: simple (text only), two-line (primary + secondary text), three-line (primary + secondary + meta), rich (avatar/icon + text + trailing action)
- Dividers: full-width, inset (indented past leading element), none
- Selection: none, single-select (radio behavior), multi-select (checkbox behavior)
- Density: compact, default, comfortable
- Interactive: clickable rows with state layer, non-interactive

## Viewport

Desktop: standard list with optional inline actions on hover.
Mobile: full-width list, swipe actions (archive/delete) replace hover actions.

## Valid Tokens

`--surface-default` (list bg), `--surface-raised` (selected item bg), `--primary-filled-bg` (active indicator), `--content-primary` (primary text), `--content-secondary` (secondary text), `--content-tertiary` (meta text), `--border-subtle` (dividers), `--space-3` (compact padding), `--space-4` (default padding), `--space-5` (comfortable padding), `--radius-md` (item radius on selection)

## Forbidden

- Never nest lists more than one level deep. Use Tree View for hierarchical data.
- List items must have minimum 48px touch target on mobile.
