# Pre-Build QC: Timeline

**Component category**: Data Display
**Sprint**: D
**Dependencies**: Avatar (L1), Badge (L1), Icon Button (L1)
**Spec file**: `DS-COMPONENTS/data-display/timeline.md`

---

## 1. State Matrix

### Base States

Timeline is a data display component (non-interactive container). Individual nodes and expandable elements have states, but the timeline itself is not an input control. States apply to:
- Node indicators (icon node, avatar node, status dot)
- Expandable detail sections
- Interactive timestamp/description areas

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Base appearance, all nodes in sequence | Node role token (primary/success/danger/warning/neutral) |
| Hover (on expandable) | `:hover`, `.hover` | Expandable section highlights on hover | `--neutral-tonal-bg` or role-specific `*-tonal-bg` |
| Focus (on expandable button) | `:focus-visible` | 2px primary ring via box-shadow | `--primary-filled-bg` |
| Active/Current node | `.active` | Primary fill, larger indicator, position marker | `--primary-filled-bg` |
| Completed node | `.completed` | Success fill (checkmark or full circle) | `--success-filled-bg` |
| Error node | `.error` | Danger fill, error icon | `--danger-filled-bg` |
| Warning node | `.warning` | Warning fill, warning icon | `--warning-filled-bg` |
| Upcoming node | `.upcoming` | Neutral/tonal fill (unfilled or subtle) | `--neutral-tonal-bg` |
| Disabled | `.disabled` | Greyed out, no interaction | `--content-disabled` |

### Compound States (Expandable Details)

| State Combo | CSS Selector | Visual Change | Token Consumed |
|------------|-------------|---------------|----------------|
| Expanded | `.expanded` | Detail section visible, node highlight | Role-specific tokens + `--neutral-tonal-bg` |
| Expanded + Hover | `.expanded:hover`, `.expanded.hover` | Detail section remains visible, highlight strengthens | Role-specific `*-tonal-bg` |
| Focused + Expanded | `.expanded:focus-visible` | 2px primary ring around expandable button/node | `--primary-filled-bg` |
| Disabled + Hover | `.disabled:hover`, `.disabled.hover` | Reverts to disabled default, no highlight | `--border-subtle` |

---

## 2. Variant Matrix

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| Orientation | vertical (default), horizontal | Layout direction, node positioning, line continuity |
| Alignment | left-aligned (icon + line on left), alternating (zigzag left/right on desktop) | Stripe/line position, node position relative to content |
| Node type | icon node, avatar node, status dot | Node visual representation (icon symbol, user avatar, colored circle) |
| Node role/status | primary (active/current), success (completed), danger (error), warning (warning), neutral (upcoming) | Node fill color, connecting line color, badge/icon |
| Has timestamp | with, without | Adds tertiary text line below event title |
| Has description | with, without | Adds secondary text content under title |
| Expandable detail | with, without | Adds expand/collapse button, detail panel below event |

**Total base combinations**: 2 (orientation) × 2 (alignment) × 3 (node type) × 5 (status) × 2 (timestamp) × 2 (description) × 2 (expandable) = 240 possible combinations

**Total with states**: 240 × 8 states (default, hover, focus, active, completed, error, warning, upcoming) = 1,920 cells to verify

**Practical subset for testing**: Test each node status alone (5 variants), then each with timestamp + description, then with expandable details. Test orientation switch (vertical → horizontal).

---

## 3. Content Resilience

| Slot | Empty | Minimal | Normal | Overflow | Arabic |
|------|-------|---------|--------|----------|--------|
| Event title | Layout intact, no label shown | Single word renders, width preserved | Typical 2-3 word title | Long titles (50+ chars) should NOT wrap; must truncate with ellipsis if width constrained or wrap to next line depending on container width | RTL text aligns right, no layout break |
| Timestamp | Omitted entirely, no space reserved | Single char or number | ISO format or locale date/time | Very long locale format (e.g. "Monday, March 25, 2026 at 2:34 PM") respects line wrapping or truncates per locale rules | RTL numerals may display differently; verify readability |
| Description | Omitted entirely, no space reserved | Single sentence | Typical description (20-30 words) | Long descriptions (200+ chars) should wrap naturally within container or be truncatable with ellipsis | RTL text wraps and aligns right |
| Node indicator | Circle placeholder shown | Minimal icon (14px) fits in node circle | Standard icon (24px) | Icon should not overflow node circle; node size increases if needed to fit content | Icons (chevron, arrow) mirror via scaleX(-1) in RTL |

**Ellipsis Rules for Overflow:**
- Event title: if constrained width, use `text-overflow: ellipsis` + `white-space: nowrap` + `overflow: hidden` + `max-width` constraint
- Description: may wrap to multiple lines instead of truncate; if wrapping is undesired, apply ellipsis rules
- Timestamp: typically does not truncate; locale formatting dictates width
- Container must accommodate full content or explicitly truncate; never let content silently overflow

---

## 4. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| Connecting vertical line | `border-inline-start` on left side | `border-inline-end` on right side | Line always visually on the same side as node indicators |
| Horizontal timeline | line flows left-to-right | line flows right-to-left | Direction reversal, not mirror |
| Node indicator circle | `margin-inline-start`, `inset-inline-start` for positioning | Flips automatically with logical properties | No hardcoded `left`/`right` |
| Expandable chevron | Points down (↓) in both LTR/RTL | Points down in both directions | Not mirrored; chevron rotates only for expand/collapse state |
| Event title/description text alignment | `text-align: start` (aligns left in LTR) | `text-align: start` (aligns right in RTL) | No hardcoded `text-align: left`/`right` |
| Alternating zigzag layout | Left stripe node 1, right stripe node 2 | Right stripe node 1, left stripe node 2 | Alternation mirrors in RTL while maintaining visual hierarchy |
| Badge overlay position | `inset-inline-end` on node indicator | Flips automatically | Badge position on node flips with logical property |

**Critical RTL tests:**
- No hardcoded `left:`, `right:`, `margin-left`, `margin-right`, `padding-left`, `padding-right` anywhere
- All positioned elements use `inset-inline-start`/`inset-inline-end`
- Border-radius on alternating nodes uses logical properties (if needed)
- Direction-sensitive icons (rarely used in timeline) mirror correctly

---

## 5. Accessibility Checklist

### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| Timeline container | `<ol>` or `<ul>` or `<div role="region">` | `aria-label="Timeline"` or descriptive aria-label |
| Timeline item | `<li>` or `<article>` or `<div role="article">` | `aria-current="step"` on active/current item |
| Event title | `<h3>`, `<h4>`, or `<div>` | Semantic heading preferred for document outline |
| Expandable trigger | `<button>` | `aria-expanded="true|false"`, `aria-controls="[detail-id]"` |
| Expandable detail panel | `<div>` or `<section>` | `id` matching `aria-controls`, `role="region"` if needed |
| Timestamp text | `<time>` | `datetime` attribute with machine-readable format |
| Node indicator (icon) | Icon within `<div>` or `<span>` | `aria-hidden="true"` if purely decorative, or `aria-label` if conveys status |

### Keyboard Interaction

| Key | Action |
|-----|--------|
| Tab | Focus moves to expandable buttons/triggers in order |
| Shift+Tab | Focus moves backwards through expandable buttons |
| Enter | Expandable button: toggle expand/collapse |
| Space | Expandable button: toggle expand/collapse |
| Escape | If expandable detail opens a popover/modal, Escape dismisses |
| Arrow Up/Down | N/A (timeline is not a composite widget; if vertical list, arrow keys could navigate items as future enhancement) |
| Arrow Left/Right | N/A |

### Brand-Specific Accessibility

| Requirement | Hulul (AA) | WideBot (AAA) |
|-------------|-----------|--------------|
| Text contrast | 4.5:1 for title, description; 3:1 for timestamp | 7:1 for title, description; 4.5:1 for timestamp |
| Node indicator size | ≥ 24px touch target (wrapper) | ≥ 44px touch target if interactive (expandable) |
| Focus ring on expandable | Visible 2px ring | 3:1 contrast against adjacent colors |
| Color independence | If node color conveys status (danger=red), must include icon/text label | Same + enhanced visual distinction |
| Timestamp locale | Renders in user locale, RTL-safe | Same, plus verify all locale formats meet contrast |

### Screen Reader Verification

- Timeline announced as ordered/unordered list or region depending on tag
- Current item announced with `aria-current="step"`
- Expandable items announce as buttons with `aria-expanded` state
- Status (completed, error, warning) conveyed via text label or icon alt text, not color alone
- Timestamps announced in human-readable locale format

---

## 6. Token Validation

### Required Token References

| Token | Usage | Exists in globals-v4.css? |
|-------|-------|--------------------------|
| `--border-default` | Connecting line between nodes | [VERIFY] |
| `--primary-filled-bg` | Active/current node background | [VERIFY] |
| `--success-filled-bg` | Completed node background | [VERIFY] |
| `--danger-filled-bg` | Error node background | [VERIFY] |
| `--warning-filled-bg` | Warning node background | [VERIFY] |
| `--neutral-tonal-bg` | Upcoming node background (subtle fill) | [VERIFY] |
| `--content-primary` | Event title text color | [VERIFY] |
| `--content-secondary` | Description text color | [VERIFY] |
| `--content-tertiary` | Timestamp text color | [VERIFY] |
| `--space-4` | Node spacing / margin between items | [VERIFY] |
| `--radius-full` | Node circle border-radius (100%) | [VERIFY] |
| `--content-disabled` | Disabled node/text color | [VERIFY] |
| `--primary-filled-fg` | Text color inside primary-filled node (if icon background) | [VERIFY] |
| `--success-filled-fg` | Text color inside success-filled node | [VERIFY] |
| `--danger-filled-fg` | Text color inside danger-filled node | [VERIFY] |
| `--warning-filled-fg` | Text color inside warning-filled node | [VERIFY] |
| `--motion-standard` | Expandable detail enter/exit animation | [VERIFY] |
| `--motion-fast` | Hover state transitions | [VERIFY] |
| `--ease-standard`, `--ease-enter`, `--ease-exit` | Animation easing | [VERIFY] |

### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter with `{ orientation, alignment, nodeStatus, hasTimestamp, hasDescription, expandable, state }`
- [ ] Show connecting line token: `--border-default`
- [ ] Show node background token based on `nodeStatus` (primary/success/danger/warning/neutral)
- [ ] Show node foreground token corresponding to node background role
- [ ] Show text tokens: `--content-primary` (title), `--content-secondary` (description), `--content-tertiary` (timestamp)
- [ ] When `state === 'disabled'`, add `--content-disabled` for all text
- [ ] Highlight which tokens are currently consumed in the preview

---

## 7. CSS Architecture

**Checkbox requirements:**

- [ ] Connecting line: `box-shadow: inset` on vertical line element (alternative: `border-left` or `border-inline-start` with logical properties) OR `border: 1px solid var(--border-default)` on connecting line
- [ ] Focus ring on expandable button: `box-shadow: 0 0 0 2px var(--primary-filled-bg)` with `:focus-visible`
- [ ] Every text element (title, description, timestamp): explicit `font-family: var(--font-sans)`, `font-size: var(--font-size-*)`, `color: var(--content-*)`
- [ ] State classes alongside pseudo-classes: `.hover` + `:hover`, `.focused` + `:focus-visible`, `.expanded` + corresponding visual state
- [ ] Disabled propagation: wrapper `.disabled` class cascades `--content-disabled` to title, description, timestamp text
- [ ] Node circle: `border-radius: var(--radius-full)` (100% for perfect circle), `width: var(--space-8)` or appropriate size, `height: var(--space-8)`
- [ ] No raw rgba values for hover overlays; use `::before` with `currentColor` + `opacity` or `color-mix()` if needed
- [ ] No hardcoded hex/rgb/rgba color values; all colors via tokens
- [ ] No hardcoded px values for spacing; use `--space-*` tokens or semantic names

**Specific patterns:**

- Connecting line must be continuous (never breaks between nodes); use a single tall element or CSS linear pattern
- Node indicators (icon, avatar, dot) centered within circle using flexbox or absolute positioning with logical properties
- Expandable detail panel uses `.expanded` state to show/hide; not `.active` (which is for node status)
- Text truncation (if needed): `text-overflow: ellipsis` + `white-space: nowrap` + `overflow: hidden` + container `max-width`

---

## 8. Motion

| Interaction | Duration Token | Easing Token |
|------------|---------------|-------------|
| Expandable section enter | `--motion-standard` | `--ease-enter` |
| Expandable section exit | `--motion-fast` | `--ease-exit` |
| Hover state (background tint) | `--motion-fast` | `--ease-standard` |
| Focus ring appearance | `--motion-fast` | `--ease-standard` |

**Motion requirements:**

- [ ] All transitions use token durations: `transition: all var(--motion-standard) var(--ease-standard)`
- [ ] No hardcoded `0.3s`, `300ms`, etc.; always use token
- [ ] `prefers-reduced-motion` is respected: when active, disable or reduce transitions to opacity-only
- [ ] Expandable detail slides/fades in (animation must be smooth and not jarring)
- [ ] No jumping or layout shift when expanding detail sections

---

## 9. Tester Integration

| Control | Options | Affects |
|---------|---------|--------|
| Orientation | vertical (default), horizontal | Layout switches, node positioning, line direction |
| Alignment | left-aligned (default), alternating | Visual left/right stripe pattern |
| Node type | icon node, avatar node, status dot | Node visual representation in preview |
| Node status | primary, success, danger, warning, upcoming | Node fill color, icon/badge, connecting line color |
| Has timestamp | on/off | Adds/removes timestamp text line |
| Has description | on/off | Adds/removes description text line |
| Expandable detail | on/off | Shows expand/collapse button and detail panel |
| Expanded state | collapsed/expanded | Controls visibility of detail content |
| Disabled state | enabled/disabled | Greyed-out appearance, no interactions |
| Locale/Theme | light/dark, LTR/RTL | Verifies token contrast and RTL layout |

**Tester requirements:**

- [ ] Every control visibly changes the preview output
- [ ] Default config renders a representative vertical timeline with 3-4 events: primary (active), success (completed), danger (error), neutral (upcoming)
- [ ] Switching between orientation and alignment does not break layout
- [ ] Expanding/collapsing detail section animates smoothly
- [ ] Disabling the timeline dims all content appropriately
- [ ] Token panel updates dynamically based on current node status (primary/success/danger/warning/neutral)
- [ ] RTL toggle mirrors the entire layout correctly (line moves to right side, text aligns right, chevrons stay down)
- [ ] Light/dark theme toggle maintains contrast and readability

---

## Verification Checklist

### Pre-Build Gate Passes

- [ ] Step 1: Component spec read; all variants, states, dependencies extracted
- [ ] Step 2: State matrix complete with 8+ states for interactive elements
- [ ] Step 3: Content resilience matrix covers empty, minimal, normal, overflow, Arabic for all text slots
- [ ] Step 4: RTL verification points document all logical properties needed
- [ ] Step 5: Accessibility checklist includes semantic HTML, keyboard navigation, brand-specific requirements
- [ ] Step 6: All tokens validated against spec and globals-v4.css
- [ ] Step 7: CSS architecture rules clear (borders, focus rings, text styling, state classes, disabled propagation, no raw values)
- [ ] Step 8: Motion tokens and prefers-reduced-motion documented
- [ ] Step 9: Tester controls cover all variants and states; token panel dynamically updates

### Ready for Build

Once all 9 steps are verified and all checkbox items above are checked, the Timeline component is ready to be built in Sprint D. Builder should reference this document during implementation to ensure every requirement is met.
