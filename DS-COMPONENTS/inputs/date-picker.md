# Date Picker

Calendar-based date or date range selection.

- Layer: L3 (input)
- Dependencies: Text Field (L3), Button (L1), Icon Button (L1), Menu (L2)
- Status: done
- Sprint: C

## Variants

- Types: single date, date range (start + end with highlight), relative ("Last 7 days", "Custom range")
- With: month/year navigation, today shortcut
- Input: text field trigger + calendar popover

## Viewport

Desktop: popover calendar grid below text field.
Mobile: bottom sheet calendar or native date input.

## Valid Tokens

`--surface-raised`, `--shadow-lg`, `--primary-filled-bg` (selected date), `--primary-tonal-bg` (range highlight), `--content-primary`, `--content-tertiary` (out-of-month dates), `--border-subtle`

## Forbidden

- Never use inline calendar without a text field trigger.
- Date format must respect locale.
