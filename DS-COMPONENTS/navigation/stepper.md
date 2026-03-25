# Stepper

Multi-step progress indicator with navigation.

- Layer: L4 (navigation)
- Dependencies: Button (L1), Badge (L1)
- Status: done
- Sprint: C

## Variants

- Orientation: horizontal (desktop), vertical (mobile)
- Step states: completed (checkmark), active (filled), upcoming (outlined), error (danger)
- With: step labels, optional descriptions, content area below
- Navigation: back/next buttons

## Viewport

Desktop: horizontal stepper with connecting lines between circles.
Mobile: vertical stepper with stacked steps.

## Valid Tokens

`--primary-filled-bg` (active/completed), `--border-default` (upcoming circle), `--danger-filled-bg` (error), `--content-primary`, `--content-tertiary`, `--radius-full` (step circles)

## Forbidden

- Never skip steps. Steps must be completed in order unless explicitly marked as optional.
