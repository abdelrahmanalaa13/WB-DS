# Avatar

Visual representation of a user or entity.

- Layer: L1 (primitive)
- Dependencies: none
- Status: done
- Baseline: ds-library.html

## Variants

- Types: image, initials, icon fallback
- Sizes: xs (24px), sm (32px), md (40px), lg (48px), xl (56px)
- Status dot: online, away, offline, none
- Group: stacked avatars with +N overflow

## Valid Tokens

`--primary-tonal-bg`, `--primary-tonal-fg` (initials background), `--surface-default` (status dot border), `--success-filled-bg` (online), `--warning-filled-bg` (away), `--neutral-filled-bg` (offline), `--radius-full`

## Forbidden

- Status dot must use outer wrapper (not inside overflow:hidden avatar).
- Status dot position uses `inset-inline-end` not `right` (RTL support).
- Never hardcode `white` for status dot border. Use `var(--surface-default)`.
