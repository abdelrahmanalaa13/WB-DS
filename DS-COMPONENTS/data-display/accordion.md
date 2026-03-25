# Accordion

Expandable/collapsible content sections.

- Layer: L5 (data display)
- Dependencies: Icon Button (L1), Divider (L1)
- Status: pending
- Sprint: D

## Variants

- Types: single-expand (one open at a time), multi-expand (multiple open)
- With: leading icon, trailing badge/count, nested content
- States: collapsed (chevron right/down), expanded (chevron rotated), disabled
- Density: compact, default, comfortable

## Motion

Expand: height auto-animate with `--motion-normal` + `--ease-standard`. Chevron rotates 90deg (LTR) or -90deg (RTL) in sync.

## Viewport

Desktop: standard accordion. Mobile: same behavior, full-width.

## Valid Tokens

`--surface-default` (panel bg), `--border-subtle` (divider between items), `--content-primary` (header text), `--content-secondary` (body text), `--space-4` (default header padding), `--space-3` (compact), `--space-5` (comfortable), `--radius-lg` (outer container), `--motion-normal`, `--ease-standard`

## Forbidden

- Never auto-collapse other panels in multi-expand mode.
- Accordion headers must be keyboard navigable (arrow keys between headers, Enter/Space to toggle).
