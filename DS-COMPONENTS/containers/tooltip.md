# Tooltip

Informational popup triggered by hover or focus.

- Layer: L2 (container)
- Dependencies: none
- Status: done
- Baseline: ds-library.html
- Sprint: B

## Surface

Extends `ds-surface`. Uses inverted surface (dark bg on light theme, light bg on dark theme).

## Variants

- Types: plain (text only), rich (title + description + action link)
- Position: top, bottom, start, end (auto-positioned to avoid viewport overflow)
- Arrow: pointing to anchor element

## Viewport

Desktop: hover/focus triggered. Mobile: tap-to-reveal, long-press for rich tooltip.

## Valid Tokens

`--neutral-filled-bg`, `--neutral-filled-fg`, `--radius-sm`, `--shadow-sm`, `--font-size-xs`, `--motion-fast`

## Forbidden

- Never use for critical information. Tooltips are supplementary.
- Touch target of trigger must be 44x44px minimum.
