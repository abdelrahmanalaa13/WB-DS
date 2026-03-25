# Card

Container for grouped content with slots. Extends ds-surface.

- Layer: L2 (container)
- Dependencies: Button (L1), Icon Button (L1), Badge (L1), Avatar (L1)
- Status: done
- Baseline: ds-library.html

## Surface

Extends `ds-surface`. Class pattern: `ds-surface ds-card {variant} {density} {interactive}`.

## Variants

- Surface variant: elevated, outlined, filled
- Density: compact, default, comfortable
- Interactive: yes/no (adds state layer)
- Content types: text, media, metric, profile, list-item, action

## Slots

- `ds-card-media`: flush-edge image/video (optional, top)
- `ds-card-header`: title + subtitle + trailing action
- `ds-card-content`: any child content
- `ds-card-footer`: action buttons (end-aligned)
- `ds-card-metric`: large number + trend indicator
- `ds-card-profile`: avatar + name + role
- `ds-card-list-item`: compact horizontal (icon + text + trailing)

## Valid Tokens

Elevated: `--surface-raised`, `--shadow-sm`, `--shadow-md` (hover)
Outlined: `--surface-default`, `--border-subtle` (inset box-shadow)
Filled: `--surface-sunken`
All: `--radius-md` (compact), `--radius-lg` (default), `--radius-xl` (comfortable)
Content: `--content-primary`, `--content-secondary`, `--content-tertiary`, `--font-size-xs` through `--font-size-lg`
Metric trend: `--success-tonal-bg`, `--success-tonal-fg`, `--danger-tonal-bg`, `--danger-tonal-fg`

## Forbidden

- Never set width/height on the card. Parent layout controls dimensions.
- Never use `size` (sm/md/lg). Use `density` (compact/default/comfortable).
- Never use CSS `border` for outlined. Use `box-shadow: inset 0 0 0 1px`.
- Never use status colors on card surface. Cards are neutral containers. Use Alert/Snackbar for status.
