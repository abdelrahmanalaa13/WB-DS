# Button

Interactive element that triggers actions.

- Layer: L1 (primitive)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Emphasis: filled, tonal, outlined, ghost
- Color roles: primary, danger, warning, success, neutral, ai
- Sizes: xs (24px), sm (32px), md (40px), lg (48px), xl (56px)
- States: default, hover, focus, pressed, disabled, loading
- With/without: leading icon, trailing icon

## Valid Tokens

`--{role}-filled-bg`, `--{role}-filled-fg`, `--{role}-tonal-bg`, `--{role}-tonal-fg`, `--{role}-outline-fg`, `--{role}-outline-border`, `--{role}-ghost-fg`, `--radius-xs` through `--radius-xl`, `--state-hover` (0.08), `--state-focus` (0.12), `--state-pressed` (0.12), `--state-disabled-content` (0.38), `--focus-ring`, `--motion-fast`, `--ease-standard`

## Forbidden

- Never use CSS `border` for outlined. Use `box-shadow: inset 0 0 0 1px`.
- Never use flat radius. Radius scales with size (xs=radius-xs through xl=radius-xl).
- Never hardcode colors. All colors via `var(--token)`.
- Never create separate hover color tokens. Use ::before state layer.
