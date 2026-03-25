# Bottom Nav

Mobile navigation bar with icon + label.

- Layer: L4 (navigation)
- Dependencies: Badge (L1)
- Status: done
- Baseline: ds-library.html

## Variants

- Active item: 4-5 items max
- With/without badge count
- States: default, active

## Valid Tokens

`--surface-default` (background), `--border-subtle` (border-top), `--primary-filled-bg` (active indicator), `--content-tertiary` (inactive), `--content-primary` (active)

## Viewport

Mobile only. On desktop, replaced by Nav Rail (component swap).

## Forbidden

- Does NOT extend ds-surface. Standalone fixed-bottom bar.
- Never show Bottom Nav on desktop viewports.
- Max 5 items. More items require a "more" menu.
