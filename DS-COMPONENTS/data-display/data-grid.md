# Data Grid

Enterprise data table with sorting, filtering, pagination, and row actions.

- Layer: L5 (data display)
- Dependencies: Button (L1), Icon Button (L1), Badge (L1), Chip (L1), Checkbox (L3), Menu (L2), Pagination (L4), Text Field (L3)
- Status: pending
- Sprint: D

## Variants

- Features: sortable columns, filterable columns, resizable columns, reorderable columns
- Selection: none, single-row, multi-row (checkbox column)
- Row actions: inline icon buttons (visible on hover), overflow menu
- Expandable rows: nested detail panel below row
- Density: compact (32px rows), default (48px rows), comfortable (64px rows)
- States: loading (skeleton rows), empty (empty state slot), error

## Viewport

Desktop: full horizontal scroll with sticky first column and sticky header.
Mobile: card-based layout per row OR horizontal scroll with column priority (hide low-priority columns).

## Valid Tokens

`--surface-default` (table bg), `--surface-sunken` (header bg), `--border-subtle` (cell borders), `--primary-tonal-bg` (selected row), `--content-primary` (cell text), `--content-tertiary` (secondary columns), `--space-2` (compact cell padding), `--space-3` (default), `--space-4` (comfortable), `--shadow-sm` (sticky column shadow)

## Forbidden

- Never load all data at once. Always paginate or virtualize rows beyond 100.
- Column resize must not shrink below minimum content width.
- Sort indicators must be visible and use aria-sort attribute.
