# Menu

Anchored dropdown list of actions or selections.

- Layer: L2 (container)
- Dependencies: Icon Button (L1), Divider (L1), Chip (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: B

## Surface

Extends `ds-surface`. Variant: elevated.

## Variants

- Types: simple (text items), with icons, with trailing shortcuts, with selection (checkmark)
- Nested: cascading submenus (desktop only)
- With/without: dividers, section headers
- Trigger: button click, right-click (context menu), long-press (mobile)

## Slots

- `ds-menu-item`: icon + label + shortcut/checkmark
- `ds-menu-divider`: separator
- `ds-menu-header`: section label

## Viewport

Desktop: floating anchored dropdown, keyboard navigable (arrow keys + enter).
Mobile: full-screen selector for long lists, bottom sheet for short lists.

## Valid Tokens

`--surface-raised`, `--shadow-lg`, `--content-primary`, `--content-secondary`, `--neutral-tonal-bg` (hover), `--primary-tonal-bg` (selected), `--radius-md`, `--motion-fast`

## Forbidden

- Never nest more than 2 levels of submenus.
- Keyboard navigation required: arrow keys, enter, escape.
