# Badge

Small status indicator, overlaid on other elements.

- Layer: L1 (primitive)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Types: dot (no count), count (number), max (99+)
- Color roles: primary, danger, warning, success, neutral, ai
- Sizes: sm, md, lg

## Valid Tokens

`--{role}-filled-bg`, `--{role}-filled-fg`, `--radius-full`, `--font-size-xs`

## Forbidden

- Never use Badge standalone without a parent element to anchor to.
- Badge always uses `--radius-full` (pill shape). No size-based radius scaling.
