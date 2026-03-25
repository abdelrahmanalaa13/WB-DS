# Command Palette

Global search and action launcher overlay.

- Layer: L6 (composed)
- Dependencies: Text Field (L3), Menu (L2), Icon Button (L1), Badge (L1), Chip (L1), Loading (L5), Dialog (L2)
- Status: pending
- Sprint: E

## Anatomy

- Trigger: Cmd+K / Ctrl+K keyboard shortcut
- Search input: large text field with search icon, auto-focused
- Results list: grouped by category (pages, actions, recent, components)
- Result item: icon + label + description + keyboard shortcut badge
- Footer: navigation hints (arrow keys, Enter, Esc)
- Empty state: no results message with suggestions

## Variants

- Modes: search (find pages/items), command (execute actions), nested (drill into category)
- With: recent searches, pinned/favorite actions, scoped search (filter by category chip)

## Motion

Open: scale from 0.95 + fade `--motion-fast`. Results: instant filter, no animation on list changes. Close: fade out `--motion-fast`.

## Viewport

Desktop: centered overlay, 640px max width, max 60vh height.
Mobile: full-screen overlay with bottom-anchored input.

## Valid Tokens

`--surface-raised` (palette bg), `--shadow-xl` (palette shadow), `--surface-default` (result hover), `--primary-tonal-bg` (active result), `--content-primary` (result label), `--content-secondary` (result description), `--content-tertiary` (shortcut badge), `--border-subtle` (category dividers), `--space-3` (result padding), `--radius-xl` (palette container), `--radius-md` (result items)

## Forbidden

- Never block the shortcut when a text field is focused. Use a modifier key check.
- Results must update within 100ms of keystroke (debounce at 150ms max).
- Must be fully keyboard navigable. Mouse is optional.
- Escape always closes, even from nested views.
