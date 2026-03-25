# Pre-Build QC: List

**Component category**: Data Display
**Sprint**: D
**Dependencies**: Avatar (L1), Badge (L1), Icon Button (L1), Divider (L1), Chip (L1)
**Spec file**: `DS-COMPONENTS/data-display/list.md`

---

## 1. State Matrix

### Base States (Non-Interactive List Items)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Normal list item appearance, visible text | `--surface-default` (bg), `--content-primary`, `--content-secondary`, `--border-subtle` (divider if present) |
| Disabled | `:disabled`, `.disabled` | Text grayed out, non-interactive appearance | `--content-disabled` (all text elements), `--surface-default` |

### Interactive List Items (Clickable Rows with State Layer)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Base state, no background tint | `--surface-default` (bg), `--content-primary`, `--content-secondary` |
| Hover | `:hover`, `.hover` | Subtle state layer overlay | `--surface-default` (bg), state layer via `::before` with `currentColor` + opacity |
| Focused | `:focus-visible` | 2px primary ring via box-shadow, no layout shift | `--primary-filled-bg` (focus ring) |
| Active/Pressed | `:active`, `.active` | State layer strengthens, item appears pressed | state layer via `::before` with `currentColor` + opacity |
| Disabled | `:disabled`, `.disabled` | Text switches to disabled color, no interactive feedback | `--content-disabled` (all text), `--surface-default` (bg) |

### Compound States for Selectable Items (Single-Select / Multi-Select)

| State Combo | CSS Selector | Visual Change | Tokens Consumed |
|------------|-------------|---------------|-----------------|
| Selected | `.selected` | Item highlighted with primary-tonal background or raised surface | `--primary-tonal-bg`, `--primary-tonal-fg` (or `--surface-raised` for elevated style) |
| Selected + Hover | `.selected:hover`, `.selected.hover` | Hover layer applied over selected background | `--primary-tonal-bg` (base), state layer overlay |
| Selected + Focused | `.selected:focus-visible` | Primary-toned background + 2px primary ring | `--primary-tonal-bg`, `--primary-filled-bg` (focus ring) |
| Selected + Disabled | `.selected.disabled` | Selected background muted, text disabled | `--primary-tonal-bg` (bg with reduced opacity or neutral), `--content-disabled` (text) |
| Disabled + Hover | `.disabled:hover`, `.disabled.hover` | Reverts to disabled default, no hover state | `--content-disabled`, `--surface-default` |

---

## 2. Variant Matrix

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| Type | simple, two-line, three-line, rich | Number of text lines, presence of avatar/icon, trailing action |
| Dividers | full-width, inset, none | Divider rendering style (if present) |
| Selection | none, single-select, multi-select | Control type (radio, checkbox) and selection behavior |
| Density | compact, default, comfortable | Vertical padding and font sizes |
| Interactive | clickable (with state layer), non-interactive | State layer presence and hover behavior |

**Total combinations**: 5 types × 3 divider options × 3 selection modes × 3 densities × 2 interactive modes = 270 potential configurations
**Key combos to test**: simple + no-select + default + clickable, two-line + full-divider + multi-select + comfortable, three-line + inset-divider + single-select + compact, rich + none + no-select + default + non-interactive

---

## 3. Content Resilience

### Text Content Handling

| Slot | Empty | Minimal | Normal | Overflow (50+ chars) | Arabic RTL |
|------|-------|---------|--------|-----------------|----------|
| Primary text (title/main) | Item collapses vertically but maintains minimum height | Single character displays (e.g., "A") with correct font sizing | Standard label display | `text-overflow: ellipsis` with `white-space: nowrap` and `overflow: hidden`; truncates with "…" | Text right-aligns naturally; container width respects RTL layout |
| Secondary text (subtitle) | Slot hidden; item does not reserve space in simple type | Short word (e.g., "Sub") displays | Standard subtitle display | Truncates same as primary; must respect line-clamping if multi-line allowed | Right-aligns in RTL; maintains visual hierarchy |
| Meta text (tertiary info) | Not rendered in simple/two-line; optional in three-line | Short code or number (e.g., "05") | Date, category, or status label | Truncates or wraps depending on density; should not break layout | Right-aligns if positioned trailing; left-aligns if leading |
| Trailing action (button/icon) | Maintains space in rich type; removed if interactive=false | Icon-only button renders at appropriate size | Icon + optional badge | Long action labels truncate; button size respects density | Icon position flips in RTL; scaleX(-1) applied if directional |

### Validation Rules per Overflow

For any text content that may overflow (>50 characters):
- [ ] `text-overflow: ellipsis` present on text element
- [ ] `white-space: nowrap` present (single-line truncation)
- [ ] `overflow: hidden` present on container
- [ ] `max-width` constraint defined via parent or inline style
- [ ] Three-line type with multi-line secondary/meta must use line-clamping: `display: -webkit-box`, `-webkit-line-clamp: 2`, `-webkit-box-orient: vertical`, `overflow: hidden`

---

## 4. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| Avatar positioning | `margin-right` (leading element) | `margin-inline-end` | Left margin in LTR becomes right margin in RTL |
| Icon Button (trailing) | `margin-left` (trailing element) | `margin-inline-start` | Right margin in LTR becomes left margin in RTL |
| List item container | `padding-left`, `padding-right` | `padding-inline-start`, `padding-inline-end` | Symmetric padding for inset dividers flips correctly |
| Inset divider offset | `margin-left` (indented) | `margin-inline-start` | Divider indent respects text direction |
| Chevron/directional icon | Static `>` or `→` | Mirror via CSS transform | Icon points correct direction (left in RTL) or uses logical CSS property |
| Text alignment | `text-align: left` | `text-align: start` | Primary/secondary text auto-aligns to text direction |
| Badge position (if on avatar) | `position: absolute; right: -4px` | `inset-inline-end: -4px` | Badge overlay flips to correct corner |
| Transition properties | Hardcoded `left`, `right` | `inset-inline-start`, `inset-inline-end` | No hardcoded directions in animation targets |

---

## 5. Accessibility Checklist

### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| List container | `<ul>` or `<ol>` | `role="list"` (if wrapping list-like structure); `aria-label` if list purpose not clear from context |
| List item wrapper | `<li>` (semantic) OR `<div role="option">` (if using list as menu/select pattern) | `role="option"` (for selectable items); `aria-selected="true/false"` (for selection state) |
| Radio control (single-select) | `<input type="radio">` with `<label>` | Associated via `name` attribute (grouped) and `for` attribute on label |
| Checkbox control (multi-select) | `<input type="checkbox">` with `<label>` | Associated via `for` attribute |
| Primary text/title | `<span>` or `<div>` with appropriate styling | `id` attribute if referenced by `aria-labelledby` |
| Secondary/meta text | `<span>` or `<div>` with class for styling | `id` attribute if needed for `aria-describedby` |
| Trailing action button | `<button>` | `aria-label` if icon-only (e.g., "Delete item"); `aria-pressed` if toggle-like |
| Divider (if rendered) | `<hr>` or `<div role="separator">` | `aria-orientation="horizontal"` (for separator semantics) |

### Keyboard Interaction

| Key | Action |
|-----|--------|
| Tab | Move focus to next list item (if interactive) or to first focusable element within item (button/checkbox). Focus sequence respects document order. |
| Shift+Tab | Move focus to previous list item or focusable element. |
| Enter | Activate selected item (navigate, toggle state, or trigger action depending on context). For items with embedded buttons, Enter focuses the button; Enter again activates. |
| Space | Toggle checkbox or radio (if selection control present). If item is focusable button-like, Space may also activate. |
| Arrow Up | Move focus to previous list item (if list is keyboard-navigable composite widget). In multi-select, maintains selection. |
| Arrow Down | Move focus to next list item (if keyboard-navigable composite widget). Maintains selection in multi-select. |
| Escape | Close any open actions, collapse nested lists (if applicable), or clear focus. |

### Brand-Specific Accessibility Requirements

#### Hulul (WCAG AA)

| Requirement | Detail |
|-------------|--------|
| Text contrast | 4.5:1 for primary/secondary text (`--content-primary`, `--content-secondary`) against `--surface-default` in both light and dark themes |
| Meta text contrast | 3:1 for tertiary text (`--content-tertiary`) against `--surface-default` (acceptable for "large text" per WCAG 2.1 AA 1.4.3) |
| Target size | List items ≥ 24px tall minimum; clickable area (including icon buttons, selection controls) ≥ 24x24px |
| Focus indicator | 2px ring visible (via box-shadow), no color requirements beyond visibility |
| Semantic structure | Use `<li>` for items, `<ul>`/`<ol>` for container; provide `aria-label` for list purpose if needed |
| Selection state indication | Checkbox/radio visual + `aria-selected` attribute |

#### WideBot (WCAG AAA)

| Requirement | Detail |
|-------------|--------|
| Text contrast | 7:1 for primary text against `--surface-default` in both themes; 7:1 for secondary text; 4.5:1 acceptable for tertiary if large (unlikely in list context) |
| Target size | List items ≥ 44px tall minimum; all interactive elements (icon buttons, selection controls) ≥ 44x44px |
| Focus ring contrast | 3:1 contrast between focus ring (`--primary-filled-bg`) and adjacent colors (`--surface-default` or selected background) in both themes |
| Color independence | Selection state must not rely on color alone; checkbox/radio visual indicator required; optionally add bold weight or checkmark icon to selected primary text |
| Error state (if list has validation) | Error text required alongside visual indicator (color + border); error icon optional but recommended |
| Divider contrast | Dividers (`--border-subtle`) must have 3:1 contrast against list background |

---

## 6. Token Validation

### Required Token References

| Token | Usage | Exists in globals-v4.css? | Verify |
|-------|-------|--------------------------|--------|
| `--surface-default` | List container background | YES | Primary background token |
| `--surface-raised` | Selected item background (alternative emphasis) | YES | Elevated surface for selection feedback |
| `--primary-filled-bg` | Focus ring color (focus-visible box-shadow) | YES | Primary brand color |
| `--primary-tonal-bg` | Selected item background (tonal style) | YES | Soft primary background for selection |
| `--primary-tonal-fg` | Selected item text color | YES | Text on tonal background |
| `--content-primary` | Primary text color (titles/main content) | YES | Standard foreground for body text |
| `--content-secondary` | Secondary text color (subtitles/descriptions) | YES | De-emphasized text |
| `--content-tertiary` | Meta text color (timestamps, counts, categories) | YES | Further de-emphasized text; verify 3:1 contrast for WCAG AA compliance |
| `--content-disabled` | Disabled item text color | YES | Disabled state text; used when `.disabled` class applied |
| `--border-subtle` | Divider color (inset and full-width) | YES | Subtle border for divider lines |
| `--space-3` | Compact density padding (vertical) | YES | Tight spacing for compact items |
| `--space-4` | Default density padding (vertical) | YES | Standard spacing for default items |
| `--space-5` | Comfortable density padding (vertical) | YES | Relaxed spacing for comfortable items |
| `--radius-md` | Border radius for selected item background (if rounded) | YES | Rounded corners on selection background |
| `--motion-fast` | Hover state layer transition duration | YES | Fast motion token for interactive feedback |
| `--ease-standard` | Hover/focus transition easing | YES | Standard easing curve |

### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter with { type, dividers, selection, density, interactive, state, colorRole }
- [ ] Branch per density: compact uses `--space-3`, default uses `--space-4`, comfortable uses `--space-5`
- [ ] Branch per interactive state: interactive lists show state layer tokens (`--motion-fast`, `--ease-standard`); non-interactive lists omit animation tokens
- [ ] Branch per selection mode: no-select lists omit selection tokens; single/multi-select lists show selection tokens (`--primary-tonal-bg`, `--primary-tonal-fg`, `--primary-filled-bg`)
- [ ] Add `--content-disabled` when `config.state === 'disabled'`
- [ ] Add `--border-subtle` when dividers are present (`config.dividers !== 'none'`)
- [ ] Show state layer tokens (`--motion-fast`, `--ease-standard`) only when `config.interactive === true`
- [ ] Token list must update dynamically as config changes

---

## 7. CSS Architecture

### Borders and State Layers

- [ ] List dividers use `box-shadow: inset 0 0 0 1px var(--border-subtle)` on list items or separator divs (NOT CSS `border` property)
- [ ] Focus ring uses `box-shadow: 0 0 0 2px var(--primary-filled-bg)` on interactive items when `:focus-visible` or `.focused` class applied
- [ ] Hover state layer implemented via `::before` overlay with `currentColor` + opacity (NOT raw `rgba()`)
- [ ] State layer must not use hardcoded `rgba(255,255,255,0.08)` or similar; instead use `currentColor` with `--content-primary` inherited color and `opacity: 0.08` (or equivalent token-based value)

### Text Styling

- [ ] Primary text element (title): `font-family: var(--font-sans)`, `font-size: var(--font-size-sm)` or `var(--font-size-md)` (depending on density), `color: var(--content-primary)`, `font-weight: 500` (or standard weight)
- [ ] Secondary text element (subtitle): `font-family: var(--font-sans)`, `font-size: var(--font-size-xs)` or `var(--font-size-sm)`, `color: var(--content-secondary)`, `font-weight: 400`
- [ ] Meta text element (tertiary): `font-family: var(--font-sans)`, `font-size: var(--font-size-xs)`, `color: var(--content-tertiary)`, `font-weight: 400`
- [ ] Ellipsis for truncated text: `text-overflow: ellipsis` ALWAYS paired with `white-space: nowrap` and `overflow: hidden` (three-part requirement)
- [ ] Truncated text must have `max-width` constraint defined on parent container

### State Propagation (Wrapper Class Pattern)

- [ ] List item wrapper uses `.ds-list-item` base class
- [ ] Disabled state: wrapper gets `.disabled` or `.list-item-disabled` class; all text children (title, subtitle, meta) receive `color: var(--content-disabled)`
- [ ] Selected state: wrapper gets `.selected` or `.list-item-selected` class; background changes to `--primary-tonal-bg` (or `--surface-raised` if elevated style)
- [ ] Error state (if applicable): wrapper gets `.error` or `.list-item-error` class; divider and text switch to danger tokens
- [ ] Hover state (interactive only): wrapper gets `:hover` or `.hover` class; `::before` state layer applied
- [ ] Focus state: wrapper gets `:focus-visible` or `.focused` class; 2px focus ring applied
- [ ] Compound state `.selected.disabled`: background uses disabled + selected color mix; text uses `--content-disabled`

### Hover on Selected/Tonal Background

- [ ] Hover overlay on selected items uses `::before` with `currentColor` + opacity, NOT raw rgba
- [ ] Example: `.list-item-selected::before { content: ""; position: absolute; inset: 0; background: currentColor; opacity: 0.08; pointer-events: none; }`
- [ ] This pattern works in both light and dark themes without additional theme overrides

### Spacing and Layout

- [ ] Padding follows density tokens: `.list-item-compact` uses `padding: var(--space-3)`, `.list-item-default` uses `var(--space-4)`, `.list-item-comfortable` uses `var(--space-5)`
- [ ] Logical properties for directional spacing: `padding-inline-start`, `padding-inline-end` (not `padding-left`/`padding-right`)
- [ ] Avatar leading margin: `margin-inline-end` (not `margin-right`)
- [ ] Trailing action margin: `margin-inline-start` (not `margin-left`)
- [ ] List container uses `padding: 0` or density-based padding (no hardcoded px values)
- [ ] No width or height on list items; dimensions driven by parent layout and content

### Divider Styling

- [ ] Full-width divider: `box-shadow: inset 0 0 0 1px var(--border-subtle)` on bottom of item (or top of next item)
- [ ] Inset divider: Same box-shadow, BUT with `margin-inline-start: var(--space-4)` or equivalent (or `padding-inline-start` on pseudo-element) to indent past leading avatar/icon
- [ ] Divider must use logical properties for margin/padding offset

### No Raw Values

- [ ] No raw hex colors (e.g., `#ffffff`, `#000000`) anywhere in list CSS
- [ ] No raw rgb/rgba values (e.g., `rgba(255,255,255,0.1)`) — use `currentColor` + opacity or `color-mix()` instead
- [ ] No raw px for spacing (except component-specific inherent sizes like avatar dimension 40px, icon size 24px). Use `--space-*` tokens.
- [ ] No raw font families. Use `var(--font-sans)` (or `--font-mono` if rare).

---

## 8. Motion

### Transition Usage

| Interaction | Duration Token | Easing Token | Applies To |
|------------|---------------|-------------|-----------|
| State layer appear (hover in) | `--motion-fast` | `--ease-standard` | List item background overlay when hovering interactive item |
| State layer disappear (hover out) | `--motion-fast` | `--ease-standard` | Fade out of state layer |
| Focus ring appear | `--motion-fast` | `--ease-standard` | Box-shadow focus ring on keyboard focus |
| Selection toggle | `--motion-fast` | `--ease-standard` | Checkbox/radio visual change, background color change to selected |
| List item insert/remove (if animating) | `--motion-standard` | `--ease-enter` (insert) / `--ease-exit` (remove) | Fade + slide in/out of new items |

### Motion Requirements

- [ ] All transitions reference motion tokens: `transition: background-color var(--motion-fast) var(--ease-standard)`
- [ ] All easings reference easing tokens: `--ease-standard`, `--ease-enter`, `--ease-exit`
- [ ] No hardcoded duration values (e.g., `0.2s`, `200ms`)
- [ ] Focus ring transitions use `--motion-fast` (rapid appear)
- [ ] `prefers-reduced-motion: reduce` media query respected: transitions reduce to 0ms or opacity-only (no movement)
- [ ] Example reduced-motion override: `@media (prefers-reduced-motion: reduce) { * { transition-duration: 0ms !important; } }`

---

## 9. Tester Integration

### List Component Controls

| Control | Type | Options | Affects Render |
|---------|------|---------|-----------------|
| Type | Dropdown | simple, two-line, three-line, rich | Changes number of text lines, avatar/icon presence, trailing action visibility |
| Dividers | Dropdown | none, full-width, inset | Adds/removes divider lines; inset dividers indent past avatar |
| Selection | Dropdown | none, single-select, multi-select | Adds radio/checkbox controls; enables `.selected` state styling |
| Density | Dropdown | compact, default, comfortable | Changes vertical padding and font sizes |
| Interactive | Toggle | true/false | Enables/disables state layer, hover feedback, focus ring |
| Item State | Dropdown | default, hover, focused, active, disabled, selected | Forces specific CSS class/state for testing (overrides actual browser state) |
| Divider Type | Toggle | (if dividers enabled) | Switches between full-width and inset styles |

### Tester Render Requirements

- [ ] Default config renders a realistic three-item list with two-line type, full dividers, no selection, default density, interactive=true
- [ ] Switching type option visibly changes the layout (simple = single line, two-line = title + subtitle, etc.)
- [ ] Switching dividers option shows/hides dividers and adjusts spacing
- [ ] Switching selection option reveals/hides checkbox/radio controls and changes item heights
- [ ] Switching density visibly changes padding and font sizes
- [ ] Toggling interactive changes hover/focus feedback visibility
- [ ] Forcing item state (hover, focused, disabled, selected) visibly updates the preview without needing actual hover/focus
- [ ] Token panel updates dynamically as config changes
- [ ] Token panel shows density-specific tokens when density changes
- [ ] Token panel adds/removes selection tokens when selection mode changes
- [ ] Token panel shows state-specific tokens (e.g., `--content-disabled` when state=disabled) when state changes
- [ ] Rich type with avatar shows Avatar component; with icon shows Icon Button; both are tabbable/interactive
- [ ] Selection control changes (radio vs checkbox) visibly update control type
- [ ] Long text (>50 chars in title or subtitle) truncates with ellipsis in simple/two-line; three-line secondary can wrap if line-clamping applied
- [ ] Error state (if implemented in this sprint) shows danger border and disabled text color
- [ ] Multi-select allows multiple items to be selected simultaneously; single-select allows only one (radio behavior)

### Token Panel Accuracy

The token panel must:
- [ ] Display all tokens used in the current config state
- [ ] Show `--surface-default` for non-selected items
- [ ] Show `--primary-tonal-bg` + `--primary-tonal-fg` for selected items
- [ ] Show `--content-primary`, `--content-secondary`, `--content-tertiary` for text
- [ ] Show `--border-subtle` when dividers are present
- [ ] Show `--space-3`, `--space-4`, or `--space-5` based on density
- [ ] Show `--motion-fast`, `--ease-standard` when interactive=true
- [ ] Show `--primary-filled-bg` when state=focused
- [ ] Show `--content-disabled` when state=disabled or when item wrapper has `.disabled` class
- [ ] Update token list immediately when config changes (no manual refresh needed)
- [ ] Verify all displayed tokens actually exist in `globals-v4.css`

---

## Pre-Build Sign-Off Checklist

Before implementation begins:

- [ ] **Step 1 (Spec Read)**: All variants, states, tokens, dependencies, and forbidden patterns extracted ✓
- [ ] **Step 2 (State Matrix)**: Complete matrix generated for base, compound, and selection states ✓
- [ ] **Step 3 (Content Resilience)**: All text slots tested for empty, minimal, normal, overflow, and RTL behavior ✓
- [ ] **Step 4 (RTL)**: All physical properties mapped to logical equivalents; chevron mirroring planned ✓
- [ ] **Step 5 (Accessibility)**: WCAG AA and AAA requirements specified per brand; semantic HTML and ARIA attributes listed ✓
- [ ] **Step 6 (Token Validation)**: All tokens verified against globals-v4.css; token panel requirements defined ✓
- [ ] **Step 7 (CSS Architecture)**: Borders, text styling, state propagation, hover patterns, and spacing rules documented ✓
- [ ] **Step 8 (Motion)**: All transitions use token durations and easings; prefers-reduced-motion handled ✓
- [ ] **Step 9 (Tester)**: All controls defined; render requirements and token panel accuracy specified ✓

**Status**: Ready for implementation in Sprint D.
**Next Step**: Implement List component in `ds-library.html` following this Pre-Build QC document.
