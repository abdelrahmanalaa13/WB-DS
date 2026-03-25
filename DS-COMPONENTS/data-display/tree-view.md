# Tree View

Hierarchical data display with expandable nodes.

- Layer: L5 (data display)
- Dependencies: Icon Button (L1), Badge (L1), Checkbox (L3)
- Status: pending
- Sprint: D

## Variants

- Types: navigation tree (single-select, highlights active), selection tree (multi-select with checkboxes, tri-state parent)
- With: leading icons per node, trailing badges/counts, drag-to-reorder
- States: expanded, collapsed, loading (child fetch), selected, disabled

## Motion

Expand: children slide in with `--motion-fast` + `--ease-standard`. Indent lines draw progressively.

## Viewport

Desktop: standard tree with indent guides.
Mobile: same structure, indent reduced. Deep nesting (4+) uses breadcrumb-style drill-down instead.

## Valid Tokens

`--surface-default` (tree bg), `--primary-tonal-bg` (selected node), `--border-subtle` (indent guides), `--content-primary` (node label), `--content-tertiary` (leaf count), `--space-4` (node padding), `--space-6` (indent per level), `--radius-md` (node hover/selection bg)

## Forbidden

- Never render more than 4 nesting levels on mobile. Switch to drill-down navigation.
- Parent checkbox must reflect tri-state (all/some/none children selected).
- Lazy-load children for nodes with 50+ descendants.
