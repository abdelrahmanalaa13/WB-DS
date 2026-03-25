# Selection Controls

Checkbox, Radio, and Switch for boolean/choice selections.

- Layer: L3 (input)
- Dependencies: none
- Status: done
- Baseline: ds-library.html
- Sprint: B

## Variants

### Checkbox
- States: unchecked, checked, indeterminate, disabled
- With/without label text

### Radio
- States: unselected, selected, disabled
- Always in a group (2+ options)

### Switch
- States: off, on, disabled
- With/without label (identical to Toggle L1 but used in form context)

## Valid Tokens

`--primary-filled-bg` (checked/selected/on), `--border-default` (unchecked border), `--surface-default` (background), `--content-primary` (label), `--content-disabled`, `--radius-xs` (checkbox), `--radius-full` (radio/switch)

## Forbidden

- Touch target 44x44px minimum even if visual element is smaller.
- Checkbox/Radio must have visible focus ring.
- Never use Switch for selections with more than 2 states.
