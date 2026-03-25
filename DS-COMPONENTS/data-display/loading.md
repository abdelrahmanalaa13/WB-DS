# Loading

Progress and loading state indicators.

- Layer: L5 (data display)
- Dependencies: none (standalone)
- Status: pending
- Sprint: D

## Variants

- Types: spinner (circular indeterminate), progress bar (linear determinate/indeterminate), skeleton (content placeholder), dots (typing indicator)
- Spinner sizes: sm (16px), md (24px), lg (40px)
- Progress bar: with/without percentage label
- Skeleton: text lines, avatar circle, card rectangle, table rows
- Overlay: inline (within content), full-screen (modal overlay with spinner)

## Motion

Spinner: continuous rotation `--motion-slow` infinite. Progress bar indeterminate: shimmer sweep left-to-right (RTL: right-to-left). Skeleton: pulse opacity `--motion-slow` infinite. Dots: sequential bounce `--motion-normal`.

## Valid Tokens

`--primary-filled-bg` (spinner/progress fill), `--border-subtle` (spinner/progress track), `--surface-sunken` (skeleton bg), `--neutral-tonal-bg` (skeleton pulse), `--content-tertiary` (percentage label), `--radius-full` (spinner), `--radius-sm` (progress bar)

## Forbidden

- Never show a loading spinner without an accessible label (aria-label or visually hidden text).
- Skeleton must match the actual content layout it replaces.
- Progress bar percentage must never go backwards.
- Reduced motion: replace spin/pulse with static opacity change.
