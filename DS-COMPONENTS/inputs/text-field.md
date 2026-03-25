# Text Field

Single-line or multi-line text input.

- Layer: L3 (input)
- Dependencies: Icon Button (L1), Badge (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: B

## Variants

- Emphasis: outlined, filled
- Sizes: sm, md, lg (fixed-height atom)
- States: empty, filled, focused, error, disabled, read-only
- Types: text, password (with visibility toggle), search (with clear button), textarea (multi-line)
- With/without: label, helper text, error message, leading icon, trailing icon, character counter, prefix, suffix

## Valid Tokens

`--surface-default` (outlined bg), `--surface-sunken` (filled bg), `--border-subtle` (default border), `--border-default` (hover border), `--primary-filled-bg` (focus indicator), `--danger-outline-border` (error border), `--danger-outline-fg` (error text), `--content-primary`, `--content-secondary`, `--content-tertiary` (placeholder), `--content-disabled`

## Viewport

No change. Full-width in mobile by default (parent layout).

## Forbidden

- Never use CSS `border` for outlined variant. Use `box-shadow: inset 0 0 0 1px`.
- Label must be above the input, never inline/floating.
- Error message replaces helper text, never shown simultaneously.
