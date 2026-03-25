# Chip

Compact element for filters, tags, or selections.

- Layer: L1 (primitive)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Emphasis: tonal, outlined
- Color roles: primary, danger, warning, success, neutral, ai
- Sizes: sm (24px), md (32px), lg (40px)
- States: default, hover, focus, pressed, disabled, selected
- With/without: leading icon, remove button (trailing X)

## Valid Tokens

`--{role}-tonal-bg`, `--{role}-tonal-fg`, `--{role}-outline-fg`, `--{role}-outline-border`, `--radius-xs` through `--radius-lg`, `--state-hover`, `--state-focus`, `--state-pressed`, `--focus-ring`

## Forbidden

- Same inset box-shadow rule for outlined. No CSS border.
- Radius scales with size.
- Must have .hover and .disabled class selectors alongside pseudo-classes.
