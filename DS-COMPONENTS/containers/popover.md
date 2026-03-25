# Popover

Anchored floating container for rich content.

- Layer: L2 (container)
- Dependencies: Button (L1), Icon Button (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: B

## Surface

Extends `ds-surface`. Variant: elevated. Anchored to a trigger element.

## Variants

- Density: compact, default
- Dismissible: click outside, ESC key, or explicit close button
- Position: auto-positioned relative to anchor (avoids viewport overflow)

## Slots

- `ds-popover-content`: any content (forms, lists, actions)

## Valid Tokens

`--surface-raised`, `--shadow-lg`, `--radius-lg`, `--motion-fast`, `--ease-decelerate`

## Forbidden

- Never use Popover for simple text. Use Tooltip.
- Never use Popover for action lists. Use Menu.
- Must dismiss on outside click.
