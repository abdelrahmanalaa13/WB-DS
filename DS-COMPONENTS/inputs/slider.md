# Slider

Continuous or discrete value selection along a track.

- Layer: L3 (input)
- Dependencies: Tooltip (L2)
- Status: done
- Sprint: C

## Variants

- Types: continuous, discrete (step marks), range (two thumbs)
- With/without: value label tooltip above thumb
- States: default, hover, active (dragging), disabled

## Valid Tokens

`--primary-filled-bg` (active track + thumb), `--neutral-tonal-bg` (inactive track), `--surface-default` (thumb), `--shadow-sm` (thumb shadow), `--radius-full` (thumb/track)

## Forbidden

- Touch target for thumb: 44x44px minimum.
- Range slider thumbs must not cross each other.
