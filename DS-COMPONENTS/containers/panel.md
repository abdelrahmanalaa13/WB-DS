# Panel

Persistent content region. Side panel on desktop, bottom sheet on mobile.

- Layer: L2 (container)
- Dependencies: Button (L1), Icon Button (L1), Divider (L1)
- Status: done
- Baseline: ds-library.html
- Sprint: A

## Surface

Extends `ds-surface`. Variant: elevated or outlined.

## Variants

- Density: compact, default, comfortable
- Dismissible: yes/no
- Collapsible: yes/no (desktop only)
- Resizable: yes/no (desktop only)

## Slots

- `ds-panel-header`: title + close/collapse button
- `ds-panel-content`: scrollable content area
- `ds-panel-footer`: sticky action buttons (optional)

## Viewport

Desktop: side region, persistent or collapsible, optional resize drag handle.
Mobile: bottom sheet with drag handle, 3 snap points (peek 25%, half 50%, full 90%).

## Valid Tokens

`--surface-raised`, `--surface-default`, `--shadow-lg`, `--border-subtle`, `--motion-normal`, `--ease-decelerate` (open), `--ease-accelerate` (close)

## Forbidden

- Never build a separate Sheet component. Panel IS the sheet on mobile.
- Never set fixed width. Parent layout controls width on desktop.
- Mobile sheet must respect safe-area-inset-bottom.
