# Tag Input

Multi-value input with tag chips and autocomplete.

- Layer: L3 (input)
- Dependencies: Chip (L1), Text Field (L3), Menu (L2)
- Status: done
- Sprint: C

## Variants

- With/without: autocomplete suggestions dropdown
- Remove: X button or backspace key
- Max tags: configurable, with "+N more" overflow

## Valid Tokens

Same as Text Field for the input container. Plus Chip tokens for tags.

## Forbidden

- Never allow duplicate tags.
- Backspace on empty input removes last tag.
