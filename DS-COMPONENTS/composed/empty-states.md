# Empty States

Placeholder displays for views with no data or content.

- Layer: L6 (composed)
- Dependencies: Button (L1), Avatar (L1)
- Status: pending
- Sprint: E

## Anatomy

- Illustration/icon: centered visual (SVG illustration or large icon)
- Title: short, clear headline explaining the state
- Description: supporting text with guidance on what to do
- Action: primary CTA button to resolve the state
- Secondary action: text link for alternative path

## Variants

- Types: no data (first use, nothing created yet), no results (search/filter returned nothing), error (failed to load), permission (no access)
- Tone: encouraging (first use), helpful (no results with suggestions), apologetic (error)
- Size: full-page (main content area), inline (within a card/panel), compact (within a table)

## Viewport

Desktop: centered in available space, max 400px content width, illustration at comfortable scale.
Mobile: same structure, illustration scaled down or hidden if space is tight.

## Valid Tokens

`--surface-default` (bg inherits from parent), `--content-secondary` (title), `--content-tertiary` (description), `--primary-filled-bg` (CTA button), `--neutral-tonal-bg` (illustration bg circle), `--space-6` (gap between illustration and text), `--space-3` (gap between title and description), `--space-5` (gap between description and action)

## Forbidden

- Never show a blank white space. Always provide an empty state with guidance.
- Empty state must always include at least a title and one action.
- Error empty states must include a retry action.
- Illustration must not be purely decorative. Use aria-hidden if so.
