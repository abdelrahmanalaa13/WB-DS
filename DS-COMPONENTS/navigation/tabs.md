# Tabs

Horizontal navigation between content sections.

- Layer: L4 (navigation)
- Dependencies: Badge (L1)
- Status: done
- Baseline: ds-library.html

## Variants

- Sizes: sm, md, lg (fixed-height atom, NOT density)
- With/without: leading icon, badge count
- States: default, hover, active, disabled

## Valid Tokens

`--primary-ghost-fg` (active tab text), `--primary-filled-bg` (active indicator line), `--content-tertiary` (inactive), `--content-disabled`, `--border-subtle` (bottom border), `--font-size-xs` through `--font-size-base`

## Viewport

Desktop: all tabs visible inline. Mobile: scrollable with overflow indicator.

## Forbidden

- Tabs are fixed-height atoms. Use size (sm/md/lg) not density.
- Active indicator is a bottom border (`::after`), not a background fill.
