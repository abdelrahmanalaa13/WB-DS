# Toggle

Binary switch for on/off states.

- Layer: L1 (primitive)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Sizes: sm, md, lg
- States: off, on, disabled
- With/without label

## Valid Tokens

`--primary-filled-bg` (on state track), `--neutral-tonal-bg` (off state track), `--surface-default` (thumb), `--radius-full`, `--motion-fast`, `--ease-spring`

## Forbidden

- Never hardcode `white` for thumb. Use `var(--surface-default)`.
- Toggle always uses `--radius-full`. No size-based radius scaling.
