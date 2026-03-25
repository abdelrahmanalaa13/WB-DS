# Search

Contextual search with filters, suggestions, and results display.

- Layer: L6 (composed)
- Dependencies: Text Field (L3), Icon Button (L1), Chip (L1), Menu (L2), Loading (L5), List (L5)
- Status: pending
- Sprint: E

## Anatomy

- Search bar: text field with search icon, clear button, optional scope selector
- Suggestions dropdown: recent searches, popular queries, autocomplete matches
- Filter bar: chip row for active filters (date range, category, status)
- Results: list or grid of result cards with highlighted match text
- Result item: title (match highlighted), snippet, metadata, thumbnail (optional)
- Empty/no results: message with spelling suggestions or related queries

## Variants

- Modes: global (searches everything), scoped (within a module/section), inline (filters a visible list)
- Display: dropdown suggestions only, full results page, inline filtered list
- With: voice input button, advanced filters expandable panel

## Motion

Suggestions: fade-in `--motion-fast` as user types. Filter chips: animate width on add/remove `--motion-fast`. Results: fade-in staggered `--motion-fast`.

## Viewport

Desktop: search bar in header/toolbar, suggestions dropdown below, results in main content.
Mobile: expandable search bar (icon tap expands to full-width), full-screen results.

## Valid Tokens

`--surface-default` (search bar bg), `--surface-raised` (suggestions dropdown), `--shadow-md` (dropdown shadow), `--primary-filled-bg` (match highlight bg with 20% opacity), `--content-primary` (result title), `--content-secondary` (snippet), `--content-tertiary` (metadata), `--border-subtle` (search bar border), `--border-focus` (focus ring), `--space-3` (result item padding), `--radius-lg` (search bar), `--radius-md` (suggestion items)

## Forbidden

- Never submit search on every keystroke. Debounce at 300ms minimum.
- Search must support Escape to clear and close suggestions.
- Highlighted match text must use mark element, not just bold.
- Empty results must always suggest corrective action.
