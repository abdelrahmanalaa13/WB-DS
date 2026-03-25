# Pre-Build QC: Accordion

**Component category**: Data Display
**Sprint**: D
**Dependencies**: Icon Button (L1), Divider (L1)
**Spec file**: `DS-COMPONENTS/data-display/accordion.md`

---

## 1. State Matrix

### Base States

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default (collapsed) | (none) | Chevron points right/down (LTR/RTL), header visible, content hidden, panel has default bg | `--surface-default`, `--border-subtle`, `--content-primary`, `--space-4` (default density) |
| Hover (collapsed) | `:hover`, `.hover` | Header background lightens via `::before` overlay | `--surface-default`, `--border-subtle`, `currentColor` + opacity via `::before` |
| Focus (collapsed) | `:focus-visible` | 2px primary ring via box-shadow on header | `--primary-filled-bg` |
| Active (collapsed -> expanding) | `:active`, `.active` | Chevron begins rotation, height animates to auto | `--motion-normal`, `--ease-standard` |
| Disabled | `:disabled`, `.disabled` | Header text color switches to `--content-disabled`, interactive states deactivated | `--content-disabled`, `--border-subtle` |
| Default (expanded) | `.expanded` | Chevron points down/up (LTR/RTL), content visible, animation complete | `--surface-default`, `--border-subtle`, `--content-primary`, `--content-secondary`, `--space-4` |
| Hover (expanded) | `:hover.expanded`, `.hover.expanded` | Header background lightens, same overlay pattern | `--surface-default`, `currentColor` + opacity |
| Focus (expanded) | `:focus-visible.expanded` | 2px primary ring via box-shadow | `--primary-filled-bg` |
| Disabled (expanded) | `.disabled.expanded` | Text color stays `--content-disabled`, chevron rotation frozen | `--content-disabled` |

### Compound States (multi-expand mode, next item in accordion)

| State Combo | CSS Selector | Visual Change | Token Consumed |
|------------|-------------|---------------|----------------|
| Adjacent item hover | `.accordion-item:hover + .accordion-item-header` | No visual change to adjacent (only current item responds to hover) | N/A |
| Disabled + Hover | `.accordion-item-header.disabled:hover` | Reverts to disabled default, no overlay | `--content-disabled` |
| Adjacent expanded | `.accordion-item.expanded + .accordion-item` | Divider renders between panels, no overlap | `--border-subtle` |

### Selection States (N/A for Accordion)

Accordion is not a selection control. Expand/collapse is a state toggle, not a selection. Each header is either expanded or collapsed.

---

## 2. Variant Matrix

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| Type | single-expand, multi-expand | Single: only one item expanded at a time; Multi: multiple items can be expanded simultaneously |
| Density | compact, default, comfortable | Header padding and font-size scale with density; affects header height and body padding |
| Content complexity | simple (text only), with-icon (leading icon), with-badge (trailing badge/count) | Leading icon appears before header text; trailing badge/count appears after text; affects header layout and spacing |

**Total combinations**: 2 (type) × 3 (density) × 3 (content) = 18 variants (+ expanded state for each = 36 render cases)

**Total with states**: 36 render cases × 4 states (default, hover, focused, disabled) = 144 cells to verify

---

## 3. Content Resilience

| Slot | Empty | Minimal | Normal | Overflow | Arabic |
|------|-------|---------|--------|----------|--------|
| Header label | Layout collapses to icon only (if icon present) or minimal height if no icon | Single character renders with proper padding | Typical label (2-5 words) | 50+ chars: must truncate with `text-overflow: ellipsis`, `white-space: nowrap`, `overflow: hidden`, `max-width` constraint defined | RTL text aligns right, truncation works same direction |
| Body content | Panel expands to empty space, divider still renders | Single character or short phrase, content area has breathing room with padding | Typical paragraph (100-200 chars), multi-line text | Long text (500+ chars): wraps naturally within panel width constraint, no truncation unless explicitly styled | RTL content preserves natural line direction, margins/padding flip via logical properties |
| Trailing badge/count | Badge hidden or minimal (0 width), header layout adjusts | Single digit (0-9) fits in badge container | Typical count (10-99), badge is appropriately sized | 3+ digits (100+): badge may truncate or grow, verify max-width or font-size adjustment | Badge position flips to inline-end in RTL |
| Leading icon | Icon hidden or minimal, header text starts immediately | Icon renders at full size, spacing adjusted | Standard icon size per density, text follows | N/A (icon size is fixed per density/component-spec) | Icon mirrors if directional; non-directional icons unaffected |

**Overflow verification for all text slots**:
- [ ] `text-overflow: ellipsis` present
- [ ] `white-space: nowrap` present (for single-line text)
- [ ] `overflow: hidden` present
- [ ] `max-width` or container constraint defined

---

## 4. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| Chevron icon rotation | `rotate(0deg)` (collapsed), `rotate(90deg)` (expanded) | `rotate(0deg)` (collapsed), `rotate(-90deg)` (expanded) | CSS uses logical property or JS applies negative rotation for RTL; icon flips correctly per direction |
| Absolute-positioned chevron | `right: --space-3` (trailing position) | `inset-inline-end: var(--space-3)` | No `left`/`right`, uses `inset-inline-*` |
| Leading icon positioning | `margin-right: --space-2` | `margin-inline-end: var(--space-2)` | No `margin-left`/`margin-right`, uses `margin-inline-*` |
| Trailing badge positioning | `margin-left: --space-2` | `margin-inline-start: var(--space-2)` | No `margin-left`/`margin-right`, uses `margin-inline-*` |
| Body content padding | `padding-left: --space-4` | `padding-inline-start: var(--space-4)` | No physical `padding-left`/`padding-right`, uses `padding-inline-*` |
| Divider direction | Horizontal line (no directional property) | Horizontal line (no directional property) | Divider renders same in both directions |
| Text alignment | `text-align: left` | `text-align: start` | Text always uses `start`/`end`, never `left`/`right` |
| Transition properties | If any transitions on positioning, use `inset-inline-*` | Same | No transitions on `left`/`right` |

---

## 5. Accessibility Checklist

### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| Accordion wrapper | `<div role="region">` or `<section>` | `aria-label="Accordion"` or descriptive label |
| Accordion item | `<div class="accordion-item">` | N/A (wrapper only) |
| Header/trigger | `<button>` with `aria-expanded`, `aria-controls` | `aria-expanded="true"`/`"false"` (dynamic), `aria-controls="panel-id"` (links to content panel) |
| Chevron icon | `<svg>` or `<i>` with `aria-hidden="true"` | `aria-hidden="true"` to prevent screen reader announcement |
| Content panel | `<div id="panel-id">` | `role="region"` or implicit from semantic tag (if `<section id>`) |
| Label (if separate) | `<label>` with `id` or inside button | Associated via `aria-labelledby` if not inside button |

### Keyboard Interaction

| Key | Action |
|-----|--------|
| Tab | Focus moves to next accordion header button |
| Shift+Tab | Focus moves to previous accordion header button |
| Enter | Toggle expand/collapse on focused header |
| Space | Toggle expand/collapse on focused header |
| Arrow Up | Move focus to previous header (optional but recommended per WAI-ARIA APG) |
| Arrow Down | Move focus to next header (optional but recommended per WAI-ARIA APG) |
| Home (if multi-header) | Move focus to first header (optional, WAI-ARIA APG pattern) |
| End (if multi-header) | Move focus to last header (optional, WAI-ARIA APG pattern) |
| Escape | N/A (accordion does not have overlay behavior) |

### Brand-Specific

| Requirement | Hulul (AA) | WideBot (AAA) |
|-------------|-----------|--------------|
| Text contrast (header + body) | 4.5:1 against `--surface-default` | 7:1 against `--surface-default` |
| Focus indicator visibility | Visible 2px ring via `box-shadow` | 2px ring with 3:1 contrast against adjacent header bg |
| Target size (header button) | >= 24px height | >= 44px height |
| Color independence | Header state not conveyed by color alone (chevron rotation + text change) | Same + explicit aria-expanded attribute |
| Disabled indication | Text color `--content-disabled` + pointer-events: none | Same, plus 44px target size still maintained |

---

## 6. Token Validation

### Required Token References

| Token | Usage | Exists in globals-v4.css? |
|-------|-------|--------------------------|
| `--surface-default` | Accordion panel background | YES -- verify |
| `--border-subtle` | Divider line between items | YES -- verify |
| `--content-primary` | Header label text color | YES -- verify |
| `--content-secondary` | Body content text color | YES -- verify |
| `--content-disabled` | Header/body text when disabled | YES -- verify |
| `--space-3` | Compact density header padding | YES -- verify |
| `--space-4` | Default density header padding | YES -- verify |
| `--space-5` | Comfortable density header padding | YES -- verify |
| `--space-2` | Icon/badge spacing | YES -- verify |
| `--radius-lg` | Outer container border-radius (if rounded corners) | YES -- verify |
| `--motion-normal` | Chevron rotation + height animation duration | YES -- verify (spec says `--motion-normal`, actual token may be `--motion-standard`) |
| `--ease-standard` | Chevron rotation + height animation easing | YES -- verify |
| `--primary-filled-bg` | Focus ring color | YES -- verify |
| `--font-sans` | Header/body text font-family | YES -- verify |
| `--font-size-body-md` (or `--font-size-md`) | Header text size (default density) | YES -- verify exact token name |

### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter with properties: `type` (single-expand/multi-expand), `density` (compact/default/comfortable), `state` (default/hover/focused/disabled), `hasIcon` (true/false), `hasBadge` (true/false)
- [ ] Show density-aware tokens: `--space-3`/`--space-4`/`--space-5` based on `config.density`
- [ ] Show state-aware tokens: add `--content-disabled` when `config.state === 'disabled'`
- [ ] Show structure tokens: `--surface-default`, `--border-subtle`, `--radius-lg`
- [ ] Show motion tokens: `--motion-normal`, `--ease-standard` with current animation duration value

---

## 7. CSS Architecture

- [ ] Borders use `box-shadow: inset 0 0 0 1px var(--border-subtle)` for dividers between items, NOT CSS `border`
- [ ] Focus ring on header button uses `box-shadow: 0 0 0 2px var(--primary-filled-bg)`
- [ ] Every text element (header label, body content, helper text if present) has explicit:
  - [ ] `font-family: var(--font-sans)`
  - [ ] `font-size: var(--font-size-*)`
  - [ ] `color: var(--content-*)`
- [ ] State classes alongside pseudo-classes:
  - [ ] `.hover` + `:hover` for header button
  - [ ] `.expanded` + `.collapsed` (or no class) for item state
  - [ ] `.disabled` + `:disabled` for disabled items
- [ ] Disabled propagation:
  - [ ] Wrapper `.disabled` class applies `--content-disabled` to header text AND body content
  - [ ] Wrapper `.disabled` class disables interactive states (no hover visual change)
- [ ] Hover on default surface:
  - [ ] Uses `::before` overlay with `currentColor` + opacity (typical: 0.08 or 0.1)
  - [ ] NOT raw `rgba()`
- [ ] No raw hex/rgb/rgba values anywhere in CSS (use token values only)
- [ ] No raw `px` values for spacing (use `--space-*` tokens); exceptions: chevron size, icon size (component-specific dimensions)
- [ ] Chevron rotation transition:
  - [ ] Uses `transition: transform var(--motion-normal) var(--ease-standard)`
  - [ ] NOT hardcoded `200ms` or `ease-in-out`
- [ ] Content height animation (expand/collapse):
  - [ ] Uses `transition: height var(--motion-normal) var(--ease-standard)`
  - [ ] OR `max-height: 0` to `max-height: 100vh` (or suitable max value) with same tokens
  - [ ] `overflow: hidden` during animation
- [ ] Logical properties throughout:
  - [ ] `margin-inline-*` not `margin-left`/`margin-right`
  - [ ] `padding-inline-*` not `padding-left`/`padding-right`
  - [ ] `inset-inline-start`/`inset-inline-end` not `left`/`right`
  - [ ] `border-start-start-radius`/`border-start-end-radius` if directional rounding needed

---

## 8. Motion

| Interaction | Duration Token | Easing Token | Spec Requirement |
|------------|---------------|-------------|-----------------|
| Chevron rotation (expand) | `--motion-normal` | `--ease-standard` | Rotates 90deg (LTR) or -90deg (RTL) in sync with content height |
| Content height animation (expand) | `--motion-normal` | `--ease-standard` | `height: 0` to `height: auto` or `max-height: 0` to `max-height: <content-height>` |
| Chevron rotation (collapse) | `--motion-normal` | `--ease-standard` | Rotates back to 0deg or 180deg (depending on open state) |
| Content height animation (collapse) | `--motion-normal` | `--ease-standard` | Reverses to `height: 0` |
| Hover state entry | `--motion-fast` | `--ease-standard` | Overlay opacity transition (if animated) |
| Hover state exit | `--motion-fast` | `--ease-standard` | Overlay opacity transition (if animated) |

- [ ] `prefers-reduced-motion` respected:
  - [ ] When `prefers-reduced-motion: reduce`, chevron rotation duration: 0ms (instant)
  - [ ] When `prefers-reduced-motion: reduce`, height animation duration: 0ms (instant, content appears/disappears)
  - [ ] Hover/focus overlay opacity changes instantly (no transition)

---

## 9. Tester Integration

| Control | Options | Affects |
|---------|---------|--------|
| Type | single-expand, multi-expand | Render shows 2-3 accordion items; single-expand demonstrates one item opens while previous closes; multi-expand shows all can be open |
| Density | compact, default, comfortable | Header and body padding/font-size change; preview updates layout |
| State (header 1) | collapsed, expanded, hover, focused, disabled | First item's chevron, body visibility, focus ring, header styling changes |
| State (header 2) | collapsed, expanded, hover, focused, disabled | Second item's state (independent of first for multi-expand demo) |
| Content type (all items) | simple, with-icon, with-badge | Header layout adjusts: icon appears, badge appears; affects spacing |

**Control effectiveness**:
- [ ] Toggling `type` between single-expand and multi-expand changes behavior (multi-expand allows multiple open, single-expand demonstrates collapse on open)
- [ ] Toggling `density` visibly changes padding/font-size
- [ ] Toggling `state` for each item visibly changes chevron angle, body visibility, header styling
- [ ] Toggling `content-type` adds/removes icon and badge, relayouts header
- [ ] `disabled` state removes pointer-events, grays out text, hides body

**Default config is representative**:
- [ ] Type: multi-expand (more flexible demo)
- [ ] Density: default (standard, matches typical usage)
- [ ] State: first item expanded, second collapsed (shows both states)
- [ ] Content type: with-icon, with-badge (full featured demo)
- [ ] Result: 2-3 items visible, one expanded with icon and badge

**Token panel updates dynamically**:
- [ ] When density changes, panel shows `--space-3`/`--space-4`/`--space-5` appropriately
- [ ] When state changes to `disabled`, panel adds `--content-disabled`
- [ ] When state is `expanded`, panel shows body content tokens (`--content-secondary`)
- [ ] When state is `collapsed`, panel shows header-only tokens
- [ ] Icon/badge presence does not change token list (icons and badges are not text, no color tokens)

---

## Summary

**Total checklist items**: 89
**Critical gates** (must pass before build):
1. All semantic HTML tags verified
2. All token references exist in globals-v4.css
3. Keyboard navigation plan documented
4. RTL strategy for chevron rotation defined
5. Motion tokens selected (--motion-normal, --ease-standard)
6. Disabled state propagation strategy confirmed
7. Hover overlay pattern chosen (::before + currentColor)
8. Density variant spacing defined (--space-3/4/5)

**Ready for build**: Yes, when all 9 steps are verified and QC document is signed off.
