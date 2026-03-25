# Timeline

Chronological event sequence display.

- Layer: L5 (data display)
- Dependencies: Avatar (L1), Badge (L1), Icon Button (L1)
- Status: pending
- Sprint: D

## Variants

- Orientation: vertical (default), horizontal (desktop only, limited items)
- Alignment: left-aligned (icon + line on left), alternating (zigzag left/right on desktop)
- Node types: icon node, avatar node, status dot (colored by role)
- With: timestamp, description, expandable detail

## Viewport

Desktop: vertical or horizontal. Alternating alignment available.
Mobile: always vertical, always left-aligned. Horizontal collapses to vertical.

## Valid Tokens

`--border-default` (connecting line), `--primary-filled-bg` (active/current node), `--success-filled-bg` (completed node), `--danger-filled-bg` (error node), `--warning-filled-bg` (warning node), `--neutral-tonal-bg` (upcoming node), `--content-primary` (event title), `--content-secondary` (description), `--content-tertiary` (timestamp), `--space-4` (node spacing), `--radius-full` (node circle)

## Forbidden

- Never use horizontal timeline on mobile.
- Connecting line must not break between nodes. Use continuous border.
- Timestamps must respect locale formatting.
