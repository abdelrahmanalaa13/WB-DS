# Breadcrumb

Navigation trail showing page hierarchy with overflow collapse for deep levels.

- Layer: L4 (navigation)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Sizes: sm, md, lg (fixed-height atom)
- Depth: 3, 5, 7, 10 levels (interactive control)
- States: link (clickable), current (non-clickable, medium weight)

## Overflow Behavior

Collapse threshold: 4 visible items max. When items exceed threshold:

- Always show: first item (root) + last 2 items (parent + current)
- Collapse: all middle items into ellipsis button
- Ellipsis button opens a popover listing collapsed items top-to-bottom
- Popover items are clickable links styled like breadcrumb items

Desktop: first + last 2 + ellipsis for middle.
Mobile: back-arrow + current page label only (no trail).

## Label Truncation

Individual labels use `max-width: 160px` with `text-overflow: ellipsis` and `title` attribute for tooltip on hover. Never wrap to second line.

## Valid Tokens

`--content-tertiary` (separator, non-current items), `--content-primary` (current item), `--content-disabled` (separator icon), `--neutral-tonal-bg` (hover background), `--surface-overlay` (popover bg), `--border-subtle` (popover border), `--font-size-xs` through `--font-size-base`

## Accessibility

- Wrap in `<nav>` with `aria-label="Breadcrumb"`
- Use semantic `<ol>` with `<li>` items
- Current page gets `aria-current="page"`
- Ellipsis button gets `aria-label="Show collapsed breadcrumbs"`
- All items keyboard-focusable via Tab

## Viewport

Desktop: full trail with overflow collapse at 4+ visible. Mobile: back-arrow + current only.

## RTL

- Entire breadcrumb reorders right-to-left
- Separator chevron mirrors (points left instead of right)

## Forbidden

- Breadcrumb is fixed-height. Use size not density.
- Never wrap to multiple lines. Use overflow collapse instead.
- Separator chevron must mirror in RTL.
- No custom separator icons. Always use chevron.
