# Icon Button

Square interactive element with icon only, no label.

- Layer: L1 (primitive)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Emphasis: filled, tonal, outlined, ghost
- Color roles: primary, danger, warning, success, neutral, ai
- Sizes: xs (24px), sm (32px), md (40px), lg (48px), xl (56px)
- States: default, hover, focus, pressed, disabled
- Toggle: selected/unselected

## Valid Tokens

Same as Button. Plus `--{role}-tonal-bg` for selected toggle state.

## Forbidden

- Same as Button (no CSS border, radius scales with size, no hardcoded colors).
- Never use without accessible label (aria-label required).
