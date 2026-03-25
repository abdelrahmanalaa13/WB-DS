# Pre-Build QC: Data Grid

**Component category**: Data Display
**Sprint**: D
**Dependencies**: Button (L1), Icon Button (L1), Badge (L1), Chip (L1), Checkbox (L3), Menu (L2), Pagination (L4), Text Field (L3)
**Spec file**: `DS-COMPONENTS/data-display/data-grid.md`

---

## 1. State Matrix

### Base States (Table Container)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Table visible, normal row heights per density | `--surface-default`, `--surface-sunken`, `--border-subtle`, `--content-primary` |
| Loading | `.loading` | Skeleton rows visible, placeholder content, disabled interactions | `--surface-default`, `--skeleton-bg`, `--skeleton-animation` |
| Empty | `.empty` | Empty state message/slot visible, no rows | `--surface-default`, `--content-tertiary` |
| Error | `.error` | Error message visible, table hidden or dimmed | `--surface-default`, `--danger-outline-border`, `--danger-outline-fg` |

### Row States (Interactive Row)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Normal row background, no selection highlight | `--surface-default`, `--border-subtle`, `--content-primary` |
| Hover | `:hover`, `.hover` | Subtle background tint on entire row, row actions (icon buttons) become visible if hidden by default | `--neutral-tonal-bg`, `--content-primary` |
| Selected | `.selected` | Row background filled with primary tonal, checkbox checked, visual emphasis | `--primary-tonal-bg`, `--primary-tonal-fg`, `--border-subtle` |
| Selected + Hover | `.selected:hover` | Darker/enhanced tonal background over selected | `--primary-tonal-bg` (enhanced via opacity or `::before` overlay), `--primary-tonal-fg` |
| Focused (row or cell) | `:focus-visible` | 2px primary ring via box-shadow around row or cell | `--primary-filled-bg` |
| Disabled (row) | `.disabled` | Row dimmed, text uses `--content-disabled`, interactions blocked | `--content-disabled`, `--border-subtle` |
| Expandable + Expanded | `.expanded` | Nested detail panel visible below row | `--surface-sunken` (nested panel background) |

### Column Header States (Interactive Header Cells)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Column title, sort indicator (if sortable) | `--content-primary`, `--border-subtle` |
| Hover | `:hover`, `.hover` | Background tint, enhanced sort indicator visibility | `--neutral-tonal-bg` |
| Sorted Ascending | `.sorted-asc` | Sort arrow points up, strong emphasis | `--primary-filled-fg`, `--primary-filled-bg` |
| Sorted Descending | `.sorted-desc` | Sort arrow points down, strong emphasis | `--primary-filled-fg`, `--primary-filled-bg` |
| Filterable + Active | `.filter-active` | Filter icon highlighted, badge shows active filter count | `--danger-filled-bg` (badge), `--primary-filled-fg` (icon) |
| Focused | `:focus-visible` | 2px primary ring via box-shadow | `--primary-filled-bg` |

### Cell States (Data Cells)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Text aligned per column type (left/center/right), background from row | `--content-primary`, `--content-secondary`, `--content-tertiary` |
| Secondary (muted) | `.secondary` | Lighter text for secondary/metadata columns | `--content-tertiary` |
| Tertiary (de-emphasized) | `.tertiary` | Smallest text for reference columns | `--content-tertiary` |
| Aligned center | `.align-center` | Text-align: center | (same tokens) |
| Aligned right | `.align-end` | Text-align: end (for RTL) | (same tokens) |
| Truncated + Overflow | `.truncated` | `text-overflow: ellipsis`, `white-space: nowrap`, `overflow: hidden`, tooltip on hover | `--content-*` (depends on cell type) |

### Row Action (Inline Icon Buttons in Row)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Hidden | `.hidden` or display: none | Not visible until row `:hover` or explicit show | N/A |
| Visible + Default | (visible on hover) | Icon button appears, outlined emphasis | `--border-subtle`, `--content-primary` |
| Hover | `:hover`, `.hover` | Background tint, outlined style hover | `--neutral-tonal-bg`, `--content-primary` |
| Focused | `:focus-visible` | 2px focus ring via box-shadow | `--primary-filled-bg` |
| Disabled | `:disabled`, `.disabled` | Dimmed, no interaction | `--content-disabled` |
| Danger Role | `.danger` | Red/danger color for delete/remove actions | `--danger-outline-border`, `--danger-outline-fg` |

### Pagination States (External, but integrated)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Page default | (none) | Button outlined, page number visible | `--border-subtle`, `--content-primary` |
| Page hover | `:hover`, `.hover` | Background tint | `--neutral-tonal-bg` |
| Page active/current | `.active` | Button filled primary, current page highlighted | `--primary-filled-bg`, `--primary-filled-fg` |
| Page disabled | `:disabled` | Prev/next buttons disabled when at boundary | `--content-disabled` |

---

## 2. Variant Matrix

### Table Variants

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| **Density** | compact (32px rows), default (48px rows), comfortable (64px rows) | Row height, cell padding, font-size within cells |
| **Selection** | none, single-row, multi-row (checkbox column) | Presence/absence of selection column, behavior on row click |
| **Row Actions** | inline (visible on hover), overflow menu (three-dot), none | Position/visibility of action buttons |
| **Column Features** | (any combination of: sortable, filterable, resizable, reorderable) | Header interaction affordances (sort arrow, filter icon, resize handle, drag handle) |
| **Expandable** | true/false | Presence of expand/collapse column, nested detail panel |

### Column Variants

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| **Alignment** | start (left in LTR), center, end (right in LTR) | Text alignment and icon position within cell |
| **Type** | text, number, currency, date, status, action | Formatting, alignment defaults, rendering (e.g., currency symbol, badge for status) |
| **Sortable** | true/false | Sort indicator arrow in header |
| **Filterable** | true/false | Filter icon in header, filter UI on click |
| **Resizable** | true/false | Resize handle in column divider |
| **Reorderable** | true/false | Drag handle (six-dot icon) in header |
| **Priority** | high, normal, low | Visibility order on mobile card layout |
| **Truncation** | none, ellipsis, wrap | Overflow behavior (`text-overflow: ellipsis`, `white-space: wrap`, or multiline) |

### Viewport/Feature Variants

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| **Viewport** | desktop, mobile | Layout (table → card-based per row, or horizontal scroll with priority columns hidden) |
| **Sticky** | header (default), header + first column | Position: sticky behavior |
| **Virtualization** | none, rows > 100 | Render only visible rows + 5px buffer, lazy-load outside viewport |
| **Empty state** | default message, custom slot | Fallback content when no rows |
| **Loading** | spinner, skeleton rows, progress | Placeholder during data fetch |

**Total combinations (conservative estimate)**:
- 3 densities × 3 selection modes × 3 row action styles × 5 column feature combos × 2 expandable = **450+ variants**
- **Per-cell state matrix**: 7 row states × 6 column header states × 6 cell states = **252+ cell-level states**
- **Practical focus**: Build representative examples in tester (default density, multi-row selection, inline actions, sortable + filterable columns, expandable rows, pagination)

---

## 3. Content Resilience

### Column Headers (Label/Title Slot)

| Slot | Empty | Minimal | Normal | Overflow | RTL/Arabic |
|------|-------|---------|--------|----------|-----------|
| **Column title** | Should not occur; show placeholder if empty | Single word (OK) | "Customer Name" (typical) | "Very Long Column Header That Exceeds Container Width" → `text-overflow: ellipsis`, `white-space: nowrap`, `overflow: hidden` with `max-width` constraint, hover shows full title in tooltip | Arabic title right-aligned, RTL direction, no width change |

**Verification for overflow**:
- [ ] `text-overflow: ellipsis` present
- [ ] `white-space: nowrap` present
- [ ] `overflow: hidden` present
- [ ] `max-width` defined (typically 200px for headers)
- [ ] Hover shows `title` attribute or tooltip with full text

### Data Cells (Content Slot)

| Slot | Empty | Minimal | Normal | Overflow | RTL/Arabic |
|------|-------|---------|--------|----------|-----------|
| **Text cell** | Empty cell visible (no content), layout stable, empty state badge/placeholder optional | Single character "A" (OK) | "John Smith", "john@example.com" | "This is a very long text entry that should truncate with ellipsis rather than wrapping" → `text-overflow: ellipsis`, `white-space: nowrap`, `overflow: hidden` | Arabic text right-aligned, base direction RTL, numerals within Arabic text |
| **Number cell** | Empty (shows "—" or placeholder), right-aligned | "1" (OK) | "1,234.56", "€50.00" | "999,999,999.99" → should fit in right-aligned cell with `text-overflow: ellipsis` if needed | Arabic numerals or Western numerals in RTL context, thousands separator direction |
| **Date cell** | Empty (shows "—"), center-aligned | "3/5" | "2026-03-25", "Mar 25, 2026" | "Friday, March 25, 2026, 2:30 PM" → truncate to "Friday, Mar... (click for full)" | RTL date format (right-to-left numbering and month ordering), calendar picker accessible |
| **Status/Badge cell** | No badge shown, layout collapses, no visual noise | Single badge ("Active") | "Active", "Pending", "Error" | Multiple badges ("High Priority + Urgent + Flagged") → stack vertically or wrap with word-break; OR show count badge ("3+") with popover | Badge text in Arabic with right-to-left orientation |
| **Action buttons row** | No buttons visible (removed), cell empty | Single icon button (OK) | 2-3 icon buttons (visible on hover) | 4+ actions → overflow menu with three-dot button | Icon buttons in RTL: chevron/arrow icons mirror via `scaleX(-1)` or CSS logical property |

**Verification for truncation**:
- [ ] Empty cells gracefully collapse (no phantom height)
- [ ] Minimal content (1-2 chars) fits without truncation
- [ ] Normal content (typical length) displays fully
- [ ] Overflow cases (50+ chars for text, 15+ digits for numbers, 40+ chars for dates) apply ellipsis or wrapping
- [ ] Tooltip shows full content on hover (title attr or JS tooltip)
- [ ] RTL text direction reverses visually but not in tab order
- [ ] Arabic numerals and text follow RTL orientation

### Row-Level Content Resilience

| Scenario | Behavior | Tokens |
|----------|----------|--------|
| **All cells empty in a row** | Row shows but visually empty (like a blank form row), minimal height per density | `--surface-default`, `--border-subtle` |
| **Mixed empty + filled cells** | Empty cells show no content, filled cells show content, row height = tallest cell content | `--content-primary` (filled), empty slots invisible |
| **Row with expandable detail panel** | Nested panel renders below row with `--surface-sunken` background, full width indented or in separate column | `--surface-sunken`, `--border-subtle`, `--content-secondary` |
| **Row in loading state** | Skeleton placeholder rows match row height per density, pulsing animation | `--skeleton-bg`, `--motion-standard` easing |

---

## 4. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| **Column resize handle** | `left: calc(100% - 1px)` or positioned on right edge | `inset-inline-end: 0` (no `right`) | Physical `right` replaced with `inset-inline-end` |
| **Row action buttons** | Right-aligned, visible on right edge on hover | `inset-inline-end: --space-2` (left in RTL) | No `right:` property, uses `inset-inline-end` |
| **Expand/collapse chevron** | Points right when collapsed, down when expanded | Mirrors via `transform: scaleX(-1)` or CSS logical property | Chevron visual direction flips in RTL |
| **Sticky left column shadow** | `box-shadow: inset -4px 0 8px rgba(0,0,0,0.08)` on right edge | `box-shadow: inset 4px 0 8px` on left edge (RTL) | No physical directional shadow, uses logical box-shadow |
| **Pagination layout** | First/Prev on left, page numbers center, Next/Last on right | Reversed: Last/Next on left, page numbers center, Prev/First on right | No `flex-direction: row-reverse`, instead use `dir=rtl` or logical property handling |
| **Column header drag handle** | Six-dot icon on left edge of header | Positioned on right edge (inline-end) | Uses `inset-inline-start` or `inset-inline-end` |
| **Sort indicator arrow** | Positioned inline-end in header (after text in LTR) | Positioned inline-start in RTL (before text) | No hardcoded `right`/`left`, uses logical properties |
| **Filter icon badge count** | Badge positioned top-right of filter icon | Badge positioned top-left (inline-start) | No `right:` positioning, uses `inset-inline-end` |
| **Cell text alignment** | `text-align: left`, `text-align: right`, `text-align: center` | `text-align: start`, `text-align: end`, `text-align: center` | Physical `left`/`right` replaced with `start`/`end` |
| **Nested detail panel indentation** | `margin-left: --space-4` for nested row | `margin-inline-start: --space-4` flips in RTL | Uses `margin-inline-start` not `margin-left` |

---

## 5. Accessibility Checklist

### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| **Table wrapper** | `<table role="grid"` or `<div role="grid">` for ARIA | `aria-label="Data Grid"`, `aria-rowcount` (total rows), `aria-colcount` (total columns) |
| **Table header row** | `<thead><tr><th>` | Each `<th>` has `aria-sort="none\|ascending\|descending"` if sortable; `scope="col"` |
| **Table body rows** | `<tbody><tr>` or virtual DOM rows | Each row `role="row"`, `aria-rowindex` (1-based), `aria-selected` (if selectable) |
| **Data cells** | `<td>` with proper tag nesting | `role="gridcell"`, `aria-label` (if content is icon-only), `aria-describedby` (if cell has tooltip) |
| **Selection checkbox** | `<input type="checkbox">` or `<div role="checkbox">` | `aria-checked="true\|false"`, `aria-label="Select row N"` |
| **Expand/collapse button** | `<button>` or `<div role="button">` | `aria-expanded="true\|false"`, `aria-label="Expand details for row N"`, `aria-controls="detail-panel-N"` |
| **Row action button** | `<button>` | `aria-label` (e.g., "Edit row 5", "Delete row 5"), not just icon |
| **Sort button (header)** | `<button>` in header cell | `aria-sort="none\|ascending\|descending"` on the button or parent `<th>` |
| **Filter button (header)** | `<button>` in header cell | `aria-label="Filter column Name"`, `aria-expanded` (if dropdown opens) |
| **Pagination controls** | `<button>`, `<a>`, or `<div role="button">` | Prev/Next buttons: `aria-label="Previous page"`, `aria-disabled` if at boundary; Page buttons: `aria-label="Go to page 3"`, `aria-current="page"` on active |
| **Status badge** | `<span>` or semantic tag | `aria-label="Status: Active"` if badge is icon-only |
| **Detail panel (expandable)** | `<div role="region">` | `aria-labelledby="row-N-label"`, `id="detail-panel-N"` (referenced by expand button) |

### Keyboard Navigation

| Key | Action |
|-----|--------|
| **Tab** | Focus moves through: column headers (sortable → button), filter icons, data cells (if focusable), selection checkboxes, row action buttons, pagination buttons. Grid rows themselves not tabbed; focus lands on interactive elements within rows. |
| **Shift+Tab** | Reverse focus direction |
| **Enter** | Activates button (sort, filter, expand, row action, pagination), or toggles checkbox in checkbox cell |
| **Space** | Toggles selection checkbox (if cell is focused), toggles row selection (if grid implements space to select) |
| **Arrow Up/Down** | Move focus to previous/next row (if row-level navigation enabled) or within multi-select (shift+arrow extends selection) |
| **Arrow Left/Right** | Move focus to previous/next cell in the same row (if cell-level navigation enabled) |
| **Ctrl+A** | Select all rows (if multi-select enabled) |
| **Escape** | Close open menus (overflow menu, filter dropdown), closes detail panel if expandable |
| **Home/End** | Jump to first/last row (if row-level navigation) or first/last cell in row (if cell-level navigation) |
| **Page Up/Down** | Scroll up/down one page of rows (if virtualization enabled) |

### Focus Management

| Interaction | Focus Behavior |
|-------------|----------------|
| **Sort column** | After sorting, focus remains on sort button; announce "Column Name sorted ascending" via aria-live |
| **Filter column** | After filtering, focus remains on filter button; announce "Filter applied: N results" via aria-live |
| **Expand row** | After expanding, focus moves to first interactive element in detail panel (or stays on expand button) |
| **Select row** | After selecting/deselecting, focus stays on checkbox; announce "Row N selected" via aria-live |
| **Pagination** | After changing page, focus returns to table (first cell or sticky header) or pagination controls; announce "Page 3 of 10" via aria-live |
| **Delete row** | After row deletion, focus moves to next visible row at same index, or previous row if at end |

### Brand-Specific Requirements

#### Hulul (WCAG AA)

| Requirement | Check | Target |
|-------------|-------|--------|
| **Text contrast** | `--content-primary` on `--surface-default` / `--surface-sunken` | >= 4.5:1 in light theme, >= 4.5:1 in dark theme |
| **Secondary text contrast** | `--content-secondary` on `--surface-default` | >= 4.5:1 |
| **Tertiary text contrast** | `--content-tertiary` on `--surface-default` | >= 4.5:1 (may fail; needs review) |
| **Target size** | Selection checkbox, expand/collapse button, row action buttons | >= 24x24px (click area, not visual size) |
| **Focus ring** | `:focus-visible` with `box-shadow: 0 0 0 2px var(--primary-filled-bg)` | Visible 2px ring |
| **Sort/filter indicators** | Sort arrow, filter icon, filter badge | Visible and meaningful (not color-alone) |
| **Error state** | Error row (if present) | Error text + icon, not color alone |

#### WideBot (WCAG AAA)

| Requirement | Check | Target |
|-------------|-------|--------|
| **Text contrast** | `--content-primary` on all surfaces | >= 7:1 in both light and dark themes |
| **Secondary text contrast** | `--content-secondary` on `--surface-default` | >= 7:1 |
| **Tertiary text contrast** | `--content-tertiary` on `--surface-default` | >= 7:1 (flag if lower; may need distinct token per brand) |
| **Target size** | Selection checkbox, expand/collapse, row actions, pagination buttons | >= 44x44px (including padding) |
| **Focus ring contrast** | `var(--primary-filled-bg)` on `--surface-default` | >= 3:1 in both themes |
| **Color independence** | Selected row, active page, sorted column, status badge | Visual indicator (checkmark, bold, ring) + NOT color alone |
| **Error indication** | Error row or error badge | Error text + icon, not red border alone |
| **Pagination page numbers** | Current page indicator | `aria-current="page"` + visual difference (filled vs outlined) |

---

## 6. Token Validation

### Required Token References

| Token | Usage | Exists in globals-v4.css? |
|-------|-------|--------------------------|
| `--surface-default` | Table background | YES — verify in globals-v4.css |
| `--surface-sunken` | Header background, nested detail panel background | YES |
| `--surface-raised` | Optional for alternate row striping | YES |
| `--border-subtle` | Cell borders, row dividers | YES |
| `--primary-filled-bg` | Focus ring color, selected row (filled), sorted column indicator | YES |
| `--primary-filled-fg` | Text/icon color on filled primary | YES |
| `--primary-tonal-bg` | Selected row (tonal variant), hovered row subtle tint | YES |
| `--primary-tonal-fg` | Text color on tonal primary | YES |
| `--neutral-tonal-bg` | Row hover background (non-selected) | YES |
| `--content-primary` | Primary cell text color | YES |
| `--content-secondary` | Secondary cell text (metadata, subtext) | YES |
| `--content-tertiary` | Tertiary cell text (de-emphasized columns) | YES |
| `--content-disabled` | Disabled row text | YES |
| `--danger-outline-border` | Error row border (if error state) | YES |
| `--danger-outline-fg` | Error text color | YES |
| `--danger-filled-bg` | Delete action button background, error badge | YES |
| `--space-2` | Compact density cell padding | YES |
| `--space-3` | Default density cell padding | YES |
| `--space-4` | Comfortable density cell padding | YES |
| `--shadow-sm` | Sticky column shadow (inset) | YES |
| `--font-sans` | All text elements | YES |
| `--font-size-sm` | Compact density cell font | YES |
| `--font-size-base` | Default density cell font | YES |
| `--motion-fast` | Hover/focus transitions | YES |
| `--motion-standard` | Row expand animation, loading skeleton | YES |
| `--ease-standard` | Standard easing for transitions | YES |

### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter with properties: `density` (compact|default|comfortable), `selection` (none|single|multi), `rowState` (default|hover|selected|disabled|error), `columnFeature` (sortable|filterable|resizable|reorderable)
- [ ] Branch per density:
  - **Compact**: `--space-2`, `--font-size-sm`, row height 32px
  - **Default**: `--space-3`, `--font-size-base`, row height 48px
  - **Comfortable**: `--space-4`, `--font-size-base` or larger, row height 64px
- [ ] Branch per row state:
  - **default**: primary content tokens (`--content-primary`, `--surface-default`)
  - **hover**: add `--neutral-tonal-bg` for overlay effect
  - **selected**: use `--primary-tonal-bg`, `--primary-tonal-fg` (or filled variant)
  - **disabled**: replace content tokens with `--content-disabled`
  - **error**: add `--danger-outline-border`, `--danger-outline-fg`
- [ ] Include column-specific tokens:
  - **sortable column**: show `--primary-filled-bg` (for sort arrow)
  - **filterable column**: show filter icon tokens, if active show `--danger-filled-bg` (badge)
  - **header state**: show `--content-primary`, `--surface-sunken`
- [ ] Dynamic token visibility: Add/remove tokens based on current config (don't show disabled tokens if density is compact)

---

## 7. CSS Architecture

- [ ] **Borders via inset box-shadow**: Cell borders and row dividers use `box-shadow: inset 0 0 0 1px var(--border-subtle)`, NOT CSS `border` property (exception: none for data grid)
- [ ] **Focus ring via box-shadow**: `box-shadow: 0 0 0 2px var(--primary-filled-bg)` for focused row/cell, or `inset 0 0 0 2px` for contained cells
- [ ] **Every text element explicit**: All cells, headers, labels, badges, action labels have explicit `font-family: var(--font-sans)`, `font-size: var(--font-size-*)`, `color: var(--content-*)`
- [ ] **State classes alongside pseudo-classes**: `.hover` class alongside `:hover`, `.selected` alongside visual state, `.disabled` alongside `:disabled`
- [ ] **Disabled propagation**: Wrapper class `.disabled` on row cascades `--content-disabled` to all child text elements (cells, labels, action descriptions)
- [ ] **Hover on selection row**: Use `::before` overlay with `currentColor` + opacity, not raw `rgba()`. Example: `.selected::before { background: currentColor; opacity: 0.1; }`
- [ ] **Truncation pattern**: Every truncated cell uses trio: `text-overflow: ellipsis`, `white-space: nowrap`, `overflow: hidden` with `max-width` constraint
- [ ] **Logical properties for RTL**: All positioning uses `inset-inline-start`/`inset-inline-end`, no `left`/`right`; borders use logical notation; directional icons use `transform: scaleX(-1)` or CSS logical property
- [ ] **Density classes**: `.density-compact`, `.density-default`, `.density-comfortable` with distinct padding and font-size per class
- [ ] **No hardcoded dimensions**: Row heights, cell padding, font sizes all via tokens; only exception: icon size, resize handle thickness, expand/collapse chevron size
- [ ] **Sticky column technique**: First column uses `position: sticky`, `inset-inline-start: 0`, with `box-shadow: inset -4px 0 8px` (or logical equivalent) for shadow edge

---

## 8. Motion

| Interaction | Duration Token | Easing Token | Trigger |
|------------|---------------|-------------|---------|
| **Row hover in/out** | `--motion-fast` | `--ease-standard` | `:hover` pseudo-class or `.hover` class |
| **Cell hover in/out** | `--motion-fast` | `--ease-standard` | `:hover` on cell focus |
| **Row selection checkbox** | `--motion-fast` | `--ease-standard` | Checkbox input state change |
| **Row expand/collapse** | `--motion-standard` | `--ease-enter` (open), `--ease-exit` (close) | Detail panel slide down/up |
| **Sort column highlight** | `--motion-standard` | `--ease-standard` | Column header sort button activation |
| **Filter active state** | `--motion-fast` | `--ease-standard` | Filter button becomes active/inactive |
| **Skeleton loading animation** | Infinite `--motion-standard` | `--ease-standard` (pulse or shimmer) | Loading state active |
| **Pagination fade** | `--motion-fast` | `--ease-standard` | Page transition (optional; may be instant) |
| **Focus ring** | `--motion-fast` | `--ease-standard` | Element receives focus |

- [ ] **prefers-reduced-motion respected**: All transitions reduce to 0ms or opacity-only when `@media (prefers-reduced-motion: reduce)` is active
- [ ] **No hardcoded durations**: Every `transition:` or `animation:` uses token variables, never hardcoded milliseconds
- [ ] **Stagger optional**: If multiple rows animate on load (skeleton → data), consider stagger with data attribute `[data-row-index]` (advanced; may be out of scope)

---

## 9. Tester Integration

### Config Structure

```javascript
{
  density: 'default',        // compact | default | comfortable
  selection: 'multi',        // none | single | multi
  rowActions: 'inline',      // none | inline | overflow-menu
  expandable: true,          // true | false
  features: ['sortable', 'filterable'],  // any combo of sortable, filterable, resizable, reorderable
  rowCount: 10,              // number of data rows to render
  rowStates: {               // demo state overrides for selected rows
    3: 'selected',           // row index 3 is selected
    5: 'hover',              // row index 5 shows hover state
    7: 'disabled'            // row index 7 is disabled
  },
  columnTypes: {             // column definitions
    0: { label: 'ID', type: 'text', sortable: true },
    1: { label: 'Name', type: 'text', sortable: true, filterable: true },
    2: { label: 'Status', type: 'status', sortable: false },
    3: { label: 'Date', type: 'date', sortable: true },
    4: { label: 'Actions', type: 'action', align: 'end' }
  },
  showPagination: true,      // true | false
  paginationPage: 1,         // current page
  paginationPageSize: 10,    // rows per page
  emptyState: 'no-results',  // no-results | loading | error | custom-content
}
```

### Control Effectiveness

| Control | Options | Expected Visual Change |
|---------|---------|----------------------|
| **density** | compact (32px) → default (48px) → comfortable (64px) | Row height changes, cell padding increases, font size may increase |
| **selection** | none → single → multi | Selection column appears/disappears, checkbox behavior changes, selected rows highlight differently |
| **rowActions** | none → inline → overflow-menu | Action buttons hidden/visible, position changes (inline vs three-dot dropdown) |
| **expandable** | false → true | Expand/collapse column appears, clicking expand shows nested detail panel below row |
| **features** (sortable) | off → on | Sort arrow appears in column header, clicking changes arrow direction, `aria-sort` updates |
| **features** (filterable) | off → on | Filter icon appears in column header, clicking opens filter dropdown/popover |
| **rowCount** | 5 → 10 → 50 | Number of visible rows increases; if virtualization, only visible + buffer rows render |
| **rowStates[N]** | default → selected → hover → disabled | Individual row visuals change; hover overlay applies, selected tint appears, disabled text dims |
| **showPagination** | false → true | Pagination controls appear below table |
| **paginationPage** | 1 → 2 → 3 | Table data updates to show next page rows, page button styles change (current page highlighted) |
| **emptyState** | no-results → loading → error | Table body content changes to empty state / skeleton / error message |

### Token Panel Accuracy

The token panel must:
- [ ] **Update dynamically**: When config changes (density, rowState), token list reflects new tokens (e.g., switching from compact to comfortable adds `--space-4`, removes `--space-2`)
- [ ] **Show emphasis branches**: If applicable (not all tables have emphasis variant), show filled/tonal/outlined/ghost tokens per branch
- [ ] **Include state-specific tokens**: When rowState is 'selected', show `--primary-tonal-bg`; when 'disabled', show `--content-disabled`; when 'error', show `--danger-outline-border` and `--danger-outline-fg`
- [ ] **Display column header tokens**: Always include `--surface-sunken`, `--content-primary`, `--border-subtle` (header base tokens)
- [ ] **Highlight sorted column tokens**: When a column is sorted in the preview, show `--primary-filled-bg` and `--primary-filled-fg` in the token panel

### Render Correctness

- [ ] **Default config is representative**: Default setup (density: default, selection: multi, rowActions: inline, expandable: true, features: [sortable, filterable], 10 rows, pagination enabled) shows a realistic data grid
- [ ] **Switching density does not break layout**: Compact/default/comfortable all render correctly with proper spacing; no text overflow, no layout shift
- [ ] **Selection state propagates correctly**: Selecting a row via checkbox sets class `.selected`, applies tonal background, updates `aria-selected`
- [ ] **Row actions visibility**: In 'inline' mode, action buttons visible on row hover; in 'overflow-menu' mode, three-dot button always visible, dropdown opens on click
- [ ] **Expandable rows**: Clicking expand button shows nested detail panel below the row with proper indentation/styling; clicking again collapses it
- [ ] **Sort/filter columns**: Clicking sort button changes arrow direction and `aria-sort`; clicking filter button opens popover with filter controls
- [ ] **Pagination**: Clicking "Next" changes page number, updates table rows, disables "Previous" if on page 1
- [ ] **Empty state**: When rowCount = 0 or emptyState = 'no-results', table body shows empty state slot (message, icon, optional action button)
- [ ] **Loading state**: When emptyState = 'loading', skeleton rows visible with pulsing animation; rowCount ignored (skeleton shows placeholder rows)
- [ ] **Error state**: When emptyState = 'error', error message + icon visible, table body hidden or dimmed

---

## Acceptance Criteria (Signed Off by Auditor)

Before marking Pre-Build QC complete, verify:

- [ ] All 9 steps in this document are filled out with specific acceptance criteria
- [ ] State matrix covers base states, compound states, and selection states
- [ ] Variant matrix identifies all axis (density, selection, row actions, features)
- [ ] Content resilience includes empty/minimal/normal/overflow/RTL for all text slots
- [ ] RTL verification points use logical properties (no `left`/`right`/`margin-left`/`margin-right`)
- [ ] Accessibility checklist includes semantic HTML, keyboard navigation, and brand-specific requirements
- [ ] Token validation includes all tokens used in the component + token panel requirements
- [ ] CSS architecture covers borders, focus rings, text properties, state propagation, and motion
- [ ] Motion section includes token references for all transitions
- [ ] Tester integration defines config structure, control effectiveness, and render correctness
- [ ] This document is referenced in the build plan as the QC gate
- [ ] Developer signs QC document before starting implementation
- [ ] Post-build audit will verify every item in this document
