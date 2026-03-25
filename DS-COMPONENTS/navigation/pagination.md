# Pagination

Page navigation for paginated content.

- Layer: L4 (navigation)
- Dependencies: Button (L1), Icon Button (L1), Menu (L2)
- Status: done
- Sprint: C

## Variants

- Types: numbered (page buttons + prev/next), compact (prev/next + "Page X of Y")
- With/without: rows-per-page selector

## Viewport

Desktop: full numbered pages. Mobile: compact prev/next with page indicator.

## Valid Tokens

`--primary-filled-bg` (current page), `--neutral-tonal-bg` (hover), `--content-primary`, `--content-tertiary`, `--radius-sm`

## Forbidden

- Never show all pages when total exceeds 7. Use ellipsis truncation.
