# Search Component - Pre-Build QC Checklist

**Layer:** L6
**Spec Version:** 1.0
**Date:** 2026-03-25

---

## 1. Component Structure & Architecture

- [ ] Component exports named export `Search` as default
- [ ] Anatomy layers correctly nested: SearchBar → TextInput + SearchIcon + ClearButton; SuggestionsDropdown (Recent + Popular + Autocomplete); FilterBar (ChipRow); ResultsList/Grid; ResultItem (Title/Snippet/Metadata/Thumbnail); EmptyState
- [ ] All dependencies imported: TextField, IconButton, Chip, Menu, Loading, List
- [ ] Props interface defined with all variants: `variant: 'global' | 'scoped' | 'inline'`
- [ ] Props for display modes: `display: 'dropdown' | 'fullPage' | 'inline'`
- [ ] Optional features: `voiceInput?: boolean`, `advancedFilters?: boolean`
- [ ] State management plan documented (pending, searching, results, error, empty)

---

## 2. Token Integration (Foundations)

- [ ] Surface tokens: `--surface-default` (bar background), `--surface-raised` (suggestions dropdown)
- [ ] Shadow tokens: `--shadow-md` applied to dropdown container
- [ ] Primary highlight: `--primary-filled-bg` at 20% opacity (match text highlighting)
- [ ] Content hierarchy: `--content-primary` (result titles), `--content-secondary` (snippets), `--content-tertiary` (metadata)
- [ ] Border tokens: `--border-subtle` applied to search bar border
- [ ] Spacing tokens: `--space-3` (result item padding), `--space-6` (major section gaps)
- [ ] Radius tokens: `--radius-lg` (search bar), `--radius-md` (result items)
- [ ] All hardcoded color/shadow values flagged for token replacement
- [ ] Token panel variables created in design file
- [ ] CSS variable declarations verified in component stylesheet

---

## 3. Universal Hover & Focus States (U1-U3)

**Search Bar Interactive Elements:**
- [ ] Search icon/button: `:hover` only with `.hover` class (U1)
- [ ] Clear button: `:hover` only with `.hover` class, suppressed when disabled (U3)
- [ ] Text input: base state vs `.hover` state documented
- [ ] Result items: `.hover` class applies --surface-raised or subtle elevation
- [ ] Filter chip (in FilterBar): `.hover` state for chip removal/interaction

**Focus States:**
- [ ] Text input: `:focus-visible` with --primary-filled-bg border or outline (U2)
- [ ] Clear button: `:focus-visible` ring visible when keyboard-focused
- [ ] Result items: `:focus-visible` optional (passive list) or full visible ring (interactive list)
- [ ] Scope selector (if present): `:focus-visible` documented
- [ ] Menu items in suggestions: `:focus-visible` visible without mouse

**Disabled Hover Suppression:**
- [ ] Clear button disabled: `.disabled` class prevents `.hover` application
- [ ] Voice input button (if disabled): `.disabled` prevents `.hover`
- [ ] Advanced filter toggles: disabled state rejects hover visuals

---

## 4. Compound States for Interactive Elements (U5)

- [ ] Search bar states: empty, active, filled, searching, error, disabled
- [ ] Clear button states: hidden (empty input), visible (filled), disabled
- [ ] Result item states: default, `.hover`, `:focus-visible`, selected, loading
- [ ] Filter chip states: default, `.hover`, `:focus-visible`, removable, disabled
- [ ] Loading indicator: skeleton or spinner during search
- [ ] Empty/no-results state: distinct visual (see Section 7)
- [ ] Transition classes documented for state changes

---

## 5. Box-Shadow Borders (U6)

- [ ] Search bar: `box-shadow` inset border (--border-subtle token) vs no outline
- [ ] Suggestions dropdown: `box-shadow` applied as --shadow-md (not border)
- [ ] Result items: optional subtle elevation with box-shadow (not border)
- [ ] No `border` property used for visual separation in interactive states
- [ ] Shadow values exported as CSS custom properties
- [ ] Tested for contrast in both light and dark modes

---

## 6. Typography & Text Rendering (U7)

- [ ] Search input: explicit font-family, font-size, line-height, letter-spacing
- [ ] Result title: --content-primary, explicit weight (600-700), size (14-16px)
- [ ] Result snippet: --content-secondary, size (12-14px), line-height 1.4+
- [ ] Result metadata: --content-tertiary, size (12px), optional secondary weight
- [ ] Filter chip text: explicit weight and size
- [ ] "Recent", "Popular" labels: --content-tertiary, explicit style
- [ ] Empty state title (if inline): --content-secondary, explicit typography
- [ ] Match highlight in results: text color inherited, background --primary-filled-bg 20% opacity
- [ ] Text overflow: heading/snippet truncation with ellipsis (U9 - see Section 8)

---

## 7. Disabled State Propagation (U8)

- [ ] Search bar disabled: input + all buttons (.disabled state)
- [ ] Clear button: auto-disabled when input empty, `:disabled` suppresses `.hover`
- [ ] Voice input button: can be independently disabled if feature flag off
- [ ] Filter bar: individual chips can be disabled
- [ ] Results list: non-interactive in display mode 'fullPage' when searching
- [ ] Advanced filter toggle: disabled state reflects feature availability
- [ ] Opacity reduction or text color shift for disabled text (use --content-disabled token if available)

---

## 8. Truncation & Text Overflow (U9)

- [ ] Result title: `overflow: hidden; text-overflow: ellipsis; white-space: nowrap` OR `display: -webkit-box; -webkit-line-clamp: 2`
- [ ] Result snippet: clamp to 2-3 lines with line-clamp
- [ ] Result metadata: single-line truncation
- [ ] Filter chip labels: truncate if exceeds max-width (typically 120px)
- [ ] Search input placeholder: truncate gracefully on narrow viewports
- [ ] Voice input text preview: truncate if exceeds visible width
- [ ] All truncated text has `title` attribute or tooltip for full content access

---

## 9. Logical Properties & Directional Styling (U10-U13)

**Positioning & Layout:**
- [ ] Suggestions dropdown: `inset-inline-start: 0` or logical equivalent (not `left`)
- [ ] Result item padding: `padding-inline: --space-3` (not `padding-left/right`)
- [ ] Filter bar chips: `gap: var(--space-2)` using logical gap
- [ ] Search icon position: `inset-inline-start` inside search bar
- [ ] Clear button position: `inset-inline-end` inside search bar

**Border Radius:**
- [ ] Search bar: `border-radius: var(--radius-lg)` applied to all corners
- [ ] Result items: `border-radius: var(--radius-md)` if contained/grouped
- [ ] Suggestions dropdown: `border-radius: var(--radius-lg)` or inherited from bar

**Spacing:**
- [ ] Between result items: `gap: var(--space-3)` or margin-block
- [ ] Result item internal spacing: `padding: var(--space-3)`
- [ ] Section gaps (Recent to Popular): `gap: var(--space-5)` or larger
- [ ] Filter bar to results: `gap: var(--space-4)`

**Alignment (Logical):**
- [ ] Title/snippet start alignment: `text-align: start` (not `left`)
- [ ] Icons center-aligned within buttons
- [ ] Result item flex layout: `flex-direction: row` with logical alignment

---

## 10. Motion & Accessibility (U14, U19, Motion)

**Motion Design:**
- [ ] Suggestions fade-in: `animation: fadeIn var(--motion-fast)` (~200ms)
- [ ] Filter chips width animation: `transition: width var(--motion-fast)` when adding/removing
- [ ] Results staggered fade-in: sequential animation via `animation-delay` for each result item
- [ ] Match text highlighting: no animation (instant on result render)
- [ ] All animations use CSS transitions/keyframes (not JavaScript animations)

**Reduced Motion (U14):**
- [ ] `@media (prefers-reduced-motion: reduce)` wraps all animations
- [ ] Duration set to 0 or instant within prefers-reduced-motion block
- [ ] Suggestions display instantly (no fade)
- [ ] Filter chips width change instant
- [ ] Results appear instantly (no stagger)
- [ ] Fallback behavior verified: animations disabled = features still work

**No Raw Values (U15):**
- [ ] All timing values use --motion-fast CSS var (not hardcoded 200ms)
- [ ] All colors reference tokens (not #hex, rgb)
- [ ] All spacing use --space-* tokens
- [ ] All shadows use --shadow-* tokens
- [ ] All radii use --radius-* tokens

**RTL & Directional Icons (U19):**
- [ ] Search icon: standard direction (not flipped in RTL)
- [ ] Clear button icon: "X" or close icon (not directional)
- [ ] Dropdown arrow (if present): `transform: scaleX(-1)` in RTL via `[dir="rtl"]` selector
- [ ] Left-arrow in breadcrumb or back link: flipped in RTL context
- [ ] Dropdown position: logical `inset-inline-start` handles RTL automatically

---

## Sign-Off

- [ ] All 10 sections reviewed
- [ ] No universal rules (U1-U19) violations found
- [ ] Ready for component build phase
- [ ] Outstanding items logged as GitHub issues/dev notes

**Reviewed By:** ________
**Date:** ________

