# Pre-Build QC: Tree View

**Component category**: Data Display
**Sprint**: D
**Dependencies**: Icon Button (L1), Badge (L1), Checkbox (L3)
**Spec file**: `DS-COMPONENTS/data-display/tree-view.md`

---

## 1. State Matrix

### Base States (All Nodes)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Base node appearance with transparent bg | `--content-primary` (label), `--space-4` (padding), `--space-6` (indent per level) |
| Hover | `:hover`, `.hover` | Background tint via `--primary-tonal-bg` or role-tonal-bg, `--radius-md` border radius | `--{role}-tonal-bg`, `--{role}-tonal-fg` |
| Focus | `:focus-visible` | 2px primary ring via box-shadow | `--primary-filled-bg` |
| Active/Pressed | `:active`, `.active` | Pressed state with darker bg overlay | `--{role}-tonal-bg` (darkened via `::before` overlay) |
| Disabled | `:disabled`, `.disabled` | Text color dims, children grayed, interactive elements non-functional | `--content-disabled` |

### Expanded/Collapsed States

| State | CSS Class | Visual Change | Tokens Consumed |
|-------|----------|---------------|-----------------|
| Expanded | `.expanded` | Children visible, chevron rotated 90°, children slide in with `--motion-fast` | `--motion-fast`, `--ease-standard` |
| Collapsed | `.collapsed` | Children hidden, chevron points right, collapse animation exits | `--motion-fast`, `--ease-exit` |

### Loading State

| State | CSS Class | Visual Change | Tokens Consumed |
|-------|----------|---------------|-----------------|
| Loading | `.loading` | Spinner appears where chevron would be, node not interactive | N/A (loading state, no token change) |

### Selection States (Selection Tree Type Only)

| State | CSS Class | Visual Change | Tokens |
|-------|----------|---------------|--------|
| Selected | `.selected` | Checkbox checked, node bg highlighted with `--primary-tonal-bg` | `--primary-tonal-bg`, `--primary-tonal-fg` |
| Selected + Hover | `.selected:hover`, `.selected.hover` | Selection bg maintained, hover overlay applied | `--primary-tonal-bg` (maintained) |
| Selected + Disabled | `.selected.disabled` | Checkbox disabled, text dimmed to `--content-disabled`, selection visual removed | `--content-disabled` |

### Compound States (Selection Tree Only)

| State Combo | CSS Selector | Visual Change | Token Consumed |
|------------|-------------|---------------|----------------|
| Tri-State Parent | `.tri-state` | Checkbox shows mixed indicator, visual distinction | `--border-subtle` (mixed state border) |
| Tri-State + Hover | `.tri-state:hover` | Mixed indicator maintained, hover overlay applied | `--{role}-tonal-bg` |
| Disabled + Hover | `.disabled:hover`, `.disabled.hover` | Reverts to disabled default, no hover effect | `--border-subtle` |

### Active Navigation State (Navigation Tree Type Only)

| State | CSS Class | Visual Change | Tokens |
|-------|----------|---------------|--------|
| Current/Active | `.aria-current` | Highlighted with `--primary-filled-bg/fg`, bold label | `--primary-filled-bg`, `--primary-filled-fg` |
| Current + Hover | `.aria-current:hover` | Active state maintained, no additional hover effect | `--primary-filled-bg` (maintained) |

---

## 2. Content Resilience

### Node Label

| Scenario | Behavior | Constraint |
|----------|----------|-----------|
| Empty | Node displays placeholder or empty space | Layout remains stable, indent maintained |
| Minimal | Single character or word (e.g., "A", "Q") | Full height preserved, no layout shift |
| Normal | Typical label (e.g., "Folder Name", "Department") | 100% width within parent constraint |
| Overflow (50+ chars) | Text truncates with ellipsis: `text-overflow: ellipsis; white-space: nowrap; overflow: hidden;` | Max-width defined by parent container minus indent/chevron/checkbox space |
| Arabic RTL | Label right-aligned, ellipsis position flips | Same truncation rules apply |

### Trailing Badge/Count (Optional)

| Scenario | Behavior | Constraint |
|----------|----------|-----------|
| Empty | Not rendered | Layout collapses, no reserved space |
| Single digit (1-9) | "5" badge or text | Fixed width badge component |
| Double digit (10+) | "25+" or similar | Badge scales or text shows abbreviated count |
| Overflow (3-digit count) | Overflow text truncated or converted to "99+" | Max badge width constraint |
| Arabic RTL | Badge position flips to left side (start position) | Same badge width applies |

### Leading Icon (Optional)

| Scenario | Behavior | Constraint |
|----------|----------|-----------|
| Empty | No icon space reserved, label starts immediately after chevron | Layout preserved |
| Icon 16px | Icon renders at 16px, maintains spacing | Icon color inherits from label color on disable |
| Multiple icons | Only one icon supported per spec, others ignored | Single icon slot |
| Arabic RTL | Icon position flips to right side | Icon mirrors if directional |

### Child Node Overflow (Deep Nesting)

| Scenario | Behavior | Constraint |
|----------|----------|-----------|
| 4+ levels on desktop | All levels visible with indent guides | `--space-6` per level indent, `--border-subtle` indent guides |
| 4+ levels on mobile | Switches to breadcrumb-style drill-down navigation | Non-scrolling, IA changes, not additional levels |
| Scroll parent | Child nodes scroll within container | Tree does not page-break, parent scroll only |

---

## 3. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| Chevron expand/collapse icon | Points right when collapsed, rotates down when expanded | Mirrors: points left when collapsed, rotates down when expanded | `transform: scaleX(-1)` applied in RTL context |
| Node indent | Indents from left edge | Indents from right edge | `margin-inline-start` replaces `margin-left` |
| Checkbox position | Left of label | Right of label | `order: -1` or position via flex direction |
| Badge position | Right of label | Left of label | `margin-inline-start` or flex order |
| Icon position | Left of label (before chevron) | Right of label (before chevron) | Icon order in flex container reverses |
| Indent guide lines | Vertical lines from left | Vertical lines from right | `border-inline-start` replaces `border-left` |
| Text alignment | `text-align: start` (left in LTR) | `text-align: start` (right in RTL) | No hardcoded `left`/`right` in alignment |
| Drag handle (if present) | Position absolute left | Position absolute right | `inset-inline-start`/`inset-inline-end` instead of `left`/`right` |

---

## 4. Accessibility Checklist

### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| Tree wrapper | `<ul role="tree">` or `<div role="tree">` | `aria-label="Tree name"` |
| Tree node | `<li role="treeitem">` or `<div role="treeitem">` | `aria-expanded` (when collapsible), `aria-selected` (selection tree), `aria-disabled` (if disabled), `aria-level` (nesting depth) |
| Expand/collapse button | `<button>` | `aria-expanded="true/false"`, `aria-label="Expand/Collapse"` |
| Checkbox (selection tree) | `<input type="checkbox">` | `aria-label` (if no visible label), `aria-checked="true/false/mixed"` (tri-state) |
| Node label | `<span>` or `<label>` | No ARIA needed if text content is accessible |
| Indent guide (visual only) | `<div>` | `aria-hidden="true"` (purely decorative) |
| Badge | `<span>` | No ARIA needed if numeric count is inline text |
| Navigation tree active node | Current node | `aria-current="page"` (matches W3C nav pattern) |
| Parent with tri-state | Checkbox input | `aria-checked="mixed"` (HTML5, some assistive tech support) |

### Keyboard Interaction

| Key | Action | Implementation |
|-----|--------|----------------|
| Tab | Move focus to next treeitem or control (focus ring visible) | `:focus-visible` with `box-shadow: 0 0 0 2px var(--primary-filled-bg)` on node or button |
| Shift+Tab | Move focus to previous treeitem | Standard reverse Tab navigation |
| Enter | Activate current node (select in selection tree, navigate in nav tree) | On treeitem or button with role treeitem |
| Space | Toggle node selection (selection tree) or expand/collapse (any tree) | Space activates button or checkbox within node |
| Arrow Up | Move focus to previous sibling node (if visible) | JS handles focus management |
| Arrow Down | Move focus to next sibling node (if visible) | JS handles focus management |
| Arrow Right | Expand node if collapsed; move focus to first child if expanded | Opens children list, focus moves |
| Arrow Left | Collapse node if expanded; move focus to parent if collapsed | Closes children list, focus moves up |
| Escape | If in drill-down mode (mobile 4+ levels), go back to parent level | Context-specific, mobile only |
| * (asterisk) | Expand all siblings at current level | Optional, advanced feature |

### Brand-Specific Requirements

| Requirement | Hulul (AA) | WideBot (AAA) |
|-------------|-----------|--------------|
| Text contrast | 4.5:1 minimum (`--content-primary` on `--surface-default`) | 7:1 minimum |
| Target size | >= 24px (node hit area including padding) | >= 44px (node hit area including padding) |
| Focus ring contrast | Visible (2px primary ring) | 3:1 contrast between `--primary-filled-bg` and `--surface-default` |
| Color independence | Node selection identifiable by `aria-selected` + visual marker (checkbox or highlight) | Same + error state identified by text + icon, not color alone |
| Keyboard-only operation | All expand/collapse, selection, and navigation achievable via keyboard | Same accessibility level |

---

## 5. Token Validation

### Required Token References

| Token | Usage | Exists in globals-v4.css? |
|-------|-------|--------------------------|
| `--surface-default` | Tree background | YES (verify) |
| `--content-primary` | Node label text color | YES (verify) |
| `--content-tertiary` | Badge count/trailing text color | YES (verify) |
| `--content-disabled` | Disabled node text and all child text | YES (verify) |
| `--primary-tonal-bg` | Selected node background (selection tree) | YES (verify) |
| `--primary-tonal-fg` | Selected node text color | YES (verify) |
| `--primary-filled-bg` | Focus ring color, active nav node background | YES (verify) |
| `--primary-filled-fg` | Active nav node text color | YES (verify) |
| `--border-subtle` | Indent guide lines, tri-state checkbox border | YES (verify) |
| `--space-4` | Node padding (vertical) | YES (verify) |
| `--space-6` | Indent per nesting level (horizontal) | YES (verify) |
| `--radius-md` | Node hover/selection background border radius | YES (verify) |
| `--motion-fast` | Expand/collapse children slide-in duration | YES (verify) |
| `--ease-standard` | Expand/collapse easing | YES (verify) |
| `--ease-exit` | Collapse animation easing | YES (verify) |
| `--{role}-tonal-bg` | Role-specific hover background (danger, warning, success if multi-role tree) | YES (verify per role) |
| `--{role}-tonal-fg` | Role-specific hover foreground | YES (verify per role) |

### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter with properties: `type` (navigation/selection), `state` (default/expanded/loading/disabled), `role` (primary/danger/etc.), `theme` (light/dark), `selected` (boolean)
- [ ] For navigation tree: show `--content-primary`, `--surface-default`, `--primary-filled-bg`, `--primary-filled-fg` when `config.state === 'default'` or node is `aria-current`
- [ ] For selection tree: show `--primary-tonal-bg`, `--primary-tonal-fg` when `config.selected === true`
- [ ] Add `--content-disabled` to all text tokens when `config.state === 'disabled'`
- [ ] Add hover tokens (`--{role}-tonal-bg`, `--{role}-tonal-fg`) when `config.state === 'hover'`
- [ ] Motion tokens (`--motion-fast`, `--ease-standard`, `--ease-exit`) always visible when tree is interactive
- [ ] Badge tokens (`--content-tertiary` for count color) always visible if badge enabled

---

## 6. CSS Architecture

- [ ] Expand/collapse chevron animation uses `transform: rotate(90deg)` not margin shift, maintains `box-sizing: content-box` or container includes chevron width
- [ ] Borders on indent guides (if present) use `box-shadow: inset` or `border-inline-start` (not `border-left`)
- [ ] Focus ring via `box-shadow: 0 0 0 2px var(--primary-filled-bg)` on node wrapper when `:focus-visible` or `.focused` class
- [ ] Every text element (label, badge, helper text) has explicit `font-family: var(--font-sans)`, `font-size`, `color`
- [ ] Node hover background uses `::before` overlay with `currentColor` + opacity or `color-mix()`, not raw `rgba()`
- [ ] State classes (`.hover`, `.focused`, `.disabled`, `.selected`, `.expanded`, `.loading`) exist alongside pseudo-classes for tester support
- [ ] Text truncation pattern: `text-overflow: ellipsis; white-space: nowrap; overflow: hidden; max-width: calc(100% - [reserved space for chevron/checkbox/icon/badge])`
- [ ] Disabled state: label and all child text switch to `--content-disabled` via wrapper `.disabled` class cascade, not via opacity
- [ ] Indent guides use logical border properties: `border-inline-start` not `border-left`
- [ ] Chevron icon mirrors in RTL via CSS `transform: scaleX(-1)` in `[dir="rtl"]` context

---

## 7. Motion

| Interaction | Duration Token | Easing Token | Notes |
|------------|---------------|-------------|-------|
| Expand (children slide in) | `--motion-fast` | `--ease-standard` | Children slide down into view, indent guides draw simultaneously |
| Collapse (children slide out) | `--motion-fast` | `--ease-exit` | Children slide up out of view |
| Chevron rotate | `--motion-fast` | `--ease-standard` | 90° rotation synchronized with child animation |
| Hover tint | `--motion-fast` | `--ease-standard` | Background tint fades in/out |
| Focus ring | `--motion-fast` | `--ease-standard` | Ring appears on focus |
| Selection toggle (checkbox) | `--motion-fast` | `--ease-standard` | Checkbox checkmark animates |

- [ ] All duration and easing values use tokens, no hardcoded `0.3s`, `ease-in-out`, etc.
- [ ] `prefers-reduced-motion` respected: all transitions reduce to 0ms or opacity-only (no transform/translate)
- [ ] Expand animation does NOT affect parent node height (children are overflow: hidden within a flex container)

---

## 8. Tester Integration

| Control | Options | Affects |
|---------|---------|--------|
| `type` | "navigation", "selection" | Renders navigation tree (single-select, aria-current) vs selection tree (checkboxes, tri-state parent) |
| `state` | "default", "expanded", "collapsed", "loading", "disabled" | Chevron rotation, children visibility, spinner, disabled state class |
| `role` | "primary", "danger", "warning", "success", "neutral", "ai" | Tonal hover bg/fg tokens, selected node colors (selection tree) |
| `selected` | true, false | Checkbox checked state, node bg highlight (selection tree only) |
| `hasIcon` | true, false | Leading icon renders before label |
| `hasBadge` | true, false | Trailing badge/count renders after label |
| `nestingLevel` | 0, 1, 2, 3, 4 | Indent spacing (multiple of `--space-6`), indent guides visible |
| `withDragHandle` | true, false | Drag handle icon renders before chevron |
| `mobileBreakpoint` | false, true | Desktop tree layout vs mobile drill-down navigation (at 4+ nesting levels) |

- [ ] Switching `type` from "navigation" to "selection" visibly changes: checkbox appears, tri-state logic available, selection colors apply
- [ ] Switching `state` from "default" to "expanded" shows children and rotates chevron 90°
- [ ] Switching `state` to "loading" shows spinner, chevron hidden, node non-interactive
- [ ] Switching `state` to "disabled" dims all text and children, disables interaction
- [ ] Increasing `nestingLevel` increases indent and shows nested child structure
- [ ] Toggling `hasIcon` adds/removes leading icon space and shifts label
- [ ] Toggling `hasBadge` adds/removes trailing badge space
- [ ] Toggling `selected` (selection tree) checks/unchecks checkbox and highlights node
- [ ] Default config renders a 3-level balanced tree with mix of: expanded/collapsed nodes, icons, badges, one selected node (selection tree), one disabled node

---

## 9. Test Cases for Post-Build Verification

### Functional Test Cases

1. **Expand/Collapse** -- Click chevron on expandable node: children appear/disappear with slide animation
2. **Navigation Tree Selection** -- Click on node: `aria-current="page"` moves to that node, visual highlight updates
3. **Selection Tree Checkboxes** -- Click checkbox: node toggles selected state, parent tri-state updates if any children selected
4. **Parent Tri-State** -- Select/deselect child nodes: parent checkbox shows mixed indicator when some (not all) children selected; clicking parent checkbox selects/deselects all children
5. **Lazy Loading** -- Expand node with `loading` state: spinner appears, children not yet rendered; once loaded, children appear and state transitions to `expanded`
6. **Disabled Propagation** -- Set parent node to disabled: node and all descendants become non-interactive, text dims to `--content-disabled`
7. **Keyboard Navigation** -- Tab through tree: focus ring visible on each node; Arrow keys navigate up/down/left/right as expected
8. **Mobile Drill-Down** -- At 4+ nesting levels on mobile: breadcrumb shows current path, clicking back navigates to parent level
9. **Drag Handle** -- If `withDragHandle` enabled: drag node to reorder, drop zones visible
10. **Badge Count Updates** -- If badge count dynamic: count updates, badge resizes (no layout shift)

### Visual Test Cases

11. **Hover State** -- Hover over node: background tint appears per role, label stays readable, badge and icon maintain color
12. **Focus Ring** -- Tab to node: 2px ring appears around entire node, ring color has sufficient contrast
13. **Error Role in Selection Tree** -- Set role to "danger" and select node: danger tonal colors apply, focus ring is danger color if selected+focused
14. **Truncation** -- Insert 50+ character label: ellipsis appears, title tooltip on hover shows full text
15. **RTL Layout** -- Toggle dir="rtl": chevron mirrors, indent shifts to right edge, checkbox/badge positions flip, text aligns right
16. **Loading Spinner** -- Set state to "loading": spinner animates at node, label visible, node clickable but chevron hidden

### State Matrix Verification

17. **Disabled + Hover** -- Hover over disabled node: no visual change from disabled default, no hover tint
18. **Selected + Disabled** -- Select node, then disable: checkbox shows checked but disabled state, text dims
19. **Expanded + Disabled** -- Expand parent, then disable: all children inherit disabled state visually
20. **Focus + Error** -- Set role to danger, tab to node: focus ring is danger color (if supported), not primary

### Token Verification

21. **Token Panel Updates** -- Change `role` control: token panel updates to show new role tokens (hover bg/fg, selected bg/fg)
22. **Disabled Tokens** -- Set `state` to "disabled": token panel adds `--content-disabled` for all text tokens, removes role-specific bg tokens
23. **Motion Tokens** -- Expand/collapse node: motion tokens (`--motion-fast`, `--ease-standard`) used in animation, visible in token panel when expand control is active

### Accessibility Verification

24. **Screen Reader Announces Role** -- Focus on treeitem: SR announces "tree item" or "collapsed tree item" + label
25. **Screen Reader Announces Expanded State** -- Navigate to expanded/collapsed node: SR announces `aria-expanded` state
26. **Screen Reader Announces Selection** -- Click checkbox in selection tree: SR announces "checked" or "unchecked"
27. **Screen Reader Announces Tri-State** -- Parent with mixed children: SR announces "mixed" or "partially checked"
28. **Screen Reader Announces Current (Nav Tree)** -- Active node: SR announces `aria-current="page"`
29. **Keyboard-Only Navigation** -- Use keyboard only (no mouse): all expand/collapse, selection, and focus navigation works
30. **Escape Key (Mobile)** -- In drill-down mode, press Escape: returns to parent level

---

## Summary

**Total state combinations**:
- Base states: 5 (default, hover, focused, active, disabled)
- Expand/collapse: 3 (expanded, collapsed, loading)
- Selection states: 4 (not selected, selected, tri-state parent, mixed)
- Navigation states: 2 (current, not current)
- **Total unique state paths**: 5 × 3 × 3 × 2 = 90 potential cells (many not applicable per tree type)

**Roles to test**: primary (default), danger, warning, success, neutral (if multi-role support)

**Mobile breakpoint**: Test at 4+ nesting levels with drill-down behavior

**Accessibility gates**:
- Semantic HTML (`<ul role="tree">`, `<li role="treeitem">`)
- ARIA attributes (`aria-expanded`, `aria-selected`, `aria-current`, `aria-disabled`, `aria-level`)
- Keyboard navigation (Tab, Arrow keys, Enter, Space, Escape)
- Focus ring on all interactive elements
- Screen reader announcements for state/role

**Key dependencies**:
- Icon Button: chevron icon, expand/collapse trigger
- Checkbox: selection tree checkboxes (with tri-state support)
- Badge: trailing count/badges
- Motion tokens: expand/collapse animations
- Token panel must reflect role-specific and state-specific tokens
