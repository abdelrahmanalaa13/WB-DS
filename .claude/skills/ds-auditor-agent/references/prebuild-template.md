# Pre-Build QC Template

Use this template when generating pre-build test cases for a component. Fill in every section. If a section does not apply to the component type, write "N/A -- [reason]".

---

## Pre-Build QC: [Component Name]

**Component category**: [General / Container / Input / Navigation / Data Display / Composed]
**Sprint**: [Sprint letter]
**Dependencies**: [List all components this depends on]
**Spec file**: `DS-COMPONENTS/<category>/<component>.md`

---

### 1. State Matrix

#### Base States

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default | (none) | Base appearance | [list tokens] |
| Hover | `:hover`, `.hover` | [describe] | [list tokens] |
| Focus | `:focus-visible` | 2px primary ring via box-shadow | `--primary-filled-bg` |
| Active/Pressed | `:active`, `.active` | [describe] | [list tokens] |
| Disabled | `:disabled`, `.disabled` | [describe] | `--content-disabled` |

#### Compound States (inputs only)

| State Combo | CSS Selector | Visual Change | Token Consumed |
|------------|-------------|---------------|----------------|
| Error | `.error` | [describe] | `--danger-outline-border` |
| Error + Hover | `.error:hover`, `.error.hover` | Border LOCKED to error | `--danger-outline-border` |
| Focused + Error | `.focused.error` | 2px danger ring | `--danger-outline-border` |
| Disabled + Hover | `.disabled:hover`, `.disabled.hover` | Reverts to disabled default | `--border-subtle` |

#### Selection States (if selectable)

| State | CSS Class | Visual Change | Tokens |
|-------|----------|---------------|--------|
| Selected | `.selected` | [describe] | [list] |
| Selected + Hover | `.selected:hover` | [describe] | [list] |
| Selected + Disabled | `.selected.disabled` | [describe] | [list] |

---

### 2. Variant Matrix

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| [emphasis] | filled, tonal, outlined, ghost | bg/fg/border tokens |
| [size] | sm, md, lg | padding, font-size, height |
| [color/role] | primary, neutral, danger, warning, success, ai | role tokens |
| [type] | [component-specific] | render structure |

**Total combinations**: [emphasis] x [size] x [role] = N variants
**Total with states**: N variants x M states = cells to verify

---

### 3. Content Resilience

| Slot | Empty | Minimal | Normal | Overflow | Arabic |
|------|-------|---------|--------|----------|--------|
| [label] | [behavior] | [behavior] | [behavior] | [truncation rule] | [RTL behavior] |
| [value] | [behavior] | [behavior] | [behavior] | [truncation rule] | [RTL behavior] |
| [helper] | [behavior] | [behavior] | [behavior] | [truncation rule] | [RTL behavior] |

For each overflow case, verify:
- `text-overflow: ellipsis` present
- `white-space: nowrap` present
- `overflow: hidden` present
- `max-width` or container constraint defined

---

### 4. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| [positioned child] | -- | `inset-inline-start`/`end` | No `left`/`right` |
| [directional border-radius] | -- | `border-start-start-radius` etc. | No physical radius |
| [directional icon] | -- | Mirrors via scaleX or CSS | Points correct direction |
| [text alignment] | -- | `text-align: start`/`end` | No `left`/`right` |

---

### 5. Accessibility Checklist

#### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| [main wrapper] | [tag] | [attrs] |
| [interactive child] | `<button>` or `<a>` | [attrs] |
| [label] | `<label>` with `for` | -- |

#### Keyboard Interaction

| Key | Action |
|-----|--------|
| Tab | [what happens] |
| Shift+Tab | [what happens] |
| Enter | [what happens] |
| Space | [what happens] |
| Escape | [what happens] |
| Arrow Up/Down | [what happens or N/A] |
| Arrow Left/Right | [what happens or N/A] |

#### Brand-Specific

| Requirement | Hulul (AA) | WideBot (AAA) |
|-------------|-----------|--------------|
| Text contrast | 4.5:1 | 7:1 |
| Target size | >= 24px | >= 44px |
| Focus ring contrast | Visible | 3:1 against adjacent |
| Color independence | Error text present | Error text + icon required |

---

### 6. Token Validation

#### Required Token References

| Token | Usage | Exists in globals-v4.css? |
|-------|-------|--------------------------|
| [token] | [what it styles] | [YES/NO -- verify] |

#### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter
- [ ] Branch per emphasis: filled/tonal show `*-filled-bg/*-filled-fg` or `*-tonal-bg/*-tonal-fg`; outlined shows `*-outline-fg/*-outline-border`; ghost shows `*-ghost-fg` only
- [ ] Add `--content-disabled` when `config.state === 'disabled'`
- [ ] Add `--danger-outline-border` + `--danger-outline-fg` when `config.state === 'error'`

---

### 7. CSS Architecture

- [ ] Borders via `box-shadow: inset 0 0 0 1px` (exception: dashed)
- [ ] Focus ring via `box-shadow: 0 0 0 2px var(--primary-filled-bg)`
- [ ] Every text element: explicit `font-family`, `font-size`, `color`
- [ ] State classes alongside pseudo-classes: `.hover` + `:hover`
- [ ] Disabled propagation via wrapper class to all child text
- [ ] Hover on inverted: `::before` overlay or `color-mix()`
- [ ] No raw rgba, no raw hex, no raw px for spacing

---

### 8. Motion

| Interaction | Duration Token | Easing Token |
|------------|---------------|-------------|
| Hover in/out | `--motion-fast` | `--ease-standard` |
| Focus ring | `--motion-fast` | `--ease-standard` |
| Enter (overlay) | `--motion-standard` | `--ease-enter` |
| Exit (overlay) | `--motion-fast` | `--ease-exit` |
| Value change | `--motion-standard` | `--ease-standard` |

- [ ] `prefers-reduced-motion` respected

---

### 9. Tester Integration

| Control | Options | Affects |
|---------|---------|--------|
| [control name] | [values] | [what changes in render] |

- [ ] Every control visibly changes preview
- [ ] Default config is representative
- [ ] Token panel updates per config
