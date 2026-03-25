# Nav Rail

Collapsible side navigation for desktop.

- Layer: L4 (navigation)
- Dependencies: Badge (L1), Icon Button (L1)
- Status: done
- Baseline: ds-library.html

## Variants

- State: expanded (icon + label), collapsed (icon only)
- Active item highlight
- Badge count on items

## Valid Tokens

`--surface-default` (background), `--border-subtle` (border-inline-end), `--primary-tonal-bg` (active item), `--primary-tonal-fg` (active item text), `--neutral-tonal-bg` (hover), `--content-primary`, `--content-secondary`

## Viewport

Desktop only. On mobile, replaced by Bottom Nav (component swap, not responsive CSS).

## Forbidden

- Does NOT extend ds-surface. Standalone component with specialized collapse/expand behavior.
- Never show Nav Rail on mobile viewports.
