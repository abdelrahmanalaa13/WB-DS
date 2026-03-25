# Kanban Board

Drag-and-drop column-based task/item management.

- Type: Product Feature Pattern
- Status: pending

## Components Used

Card (L2), Button (L1), Icon Button (L1), Badge (L1), Avatar (L1), Chip (L1), Menu (L2), Dialog (L2), Empty States (L6)

## Layout Structure

- Board: horizontal scroll container of columns
- Column: vertical stack with header (title + count badge + menu) + card list + "Add item" button
- Card: ds-card compact with title, assignee avatar, priority chip, due date
- Column footer: ghost button for adding new items

## Interaction Flow

1. Drag card between columns (updates status)
2. Drag to reorder within column (updates priority/order)
3. Click card to open detail dialog
4. Column header menu: rename, add item, archive column, set WIP limit
5. WIP limit exceeded: column header turns warning color

## Viewport

Desktop: horizontal scroll with 280px min column width, all columns visible.
Mobile: single column view with horizontal swipe between columns, column selector tabs at top.

## Layout Tokens

`--kanban-column-width: 280px`, `--kanban-column-gap: var(--space-4)`, `--kanban-card-gap: var(--space-2)`

## Drag Behavior

Uses ds-draggable composable trait. Drop zones highlight with `--primary-tonal-bg`. Dragged card gets `--shadow-lg` + slight scale(1.02). Placeholder shows dashed border at drop position.
