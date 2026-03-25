# Quick Audit Checklist

Use this as a rapid-fire verification list during Post-Build audits. Each item maps to a gate in the SKILL.md.

---

## Gate 1: RTL Safety

- [ ] No `left:` or `right:` in CSS (use `inset-inline-start`/`inset-inline-end`)
- [ ] No `margin-left`/`margin-right` (use `margin-inline-start`/`margin-inline-end`)
- [ ] No `padding-left`/`padding-right` (use `padding-inline-start`/`padding-inline-end`)
- [ ] No `text-align: left`/`right` (use `start`/`end`)
- [ ] No directional `border-radius` (use `border-start-start-radius` etc.)
- [ ] No `float: left`/`right`
- [ ] Transitions reference logical properties, not physical
- [ ] Exception: `translateX()` for self-centering is OK

## Gate 2: Token Compliance

- [ ] No hex colors in CSS
- [ ] No rgb()/rgba() values (use `color-mix()` or `currentColor` + `::before`)
- [ ] No hardcoded font-family strings
- [ ] No raw px for spacing (use `--space-*`)
- [ ] Acceptable raw px: icon dimensions, thumb size, track height, fixed component-specific sizes

## Gate 3: Text Element Coverage

For EVERY element that renders text:
- [ ] Has `font-family: var(--font-sans)`
- [ ] Has `font-size: var(--font-size-*)`
- [ ] Has explicit `color: var(--content-*)` or role token
- [ ] Does NOT rely on CSS inheritance for any of the above

## Gate 4: State Completeness

- [ ] `:hover` AND `.hover` class both defined
- [ ] `:focus-visible` with `box-shadow` focus ring (not outline)
- [ ] `.disabled` or `:disabled` with `cursor: not-allowed`
- [ ] `.error` (for inputs) with danger border
- [ ] `.error:hover` / `.error.hover` -- border LOCKED (does not change)
- [ ] `.focused.error` -- danger focus ring (2px `--danger-outline-border`)
- [ ] `.disabled:hover` / `.disabled.hover` -- reverts to default appearance
- [ ] `.active` or `:active` / `.pressed` where applicable

## Gate 5: Disabled Propagation

- [ ] Wrapper gets `.disabled` or `.label-disabled` class
- [ ] Label text: `color: var(--content-disabled)`
- [ ] Helper text: `color: var(--content-disabled)`
- [ ] Counter text: `color: var(--content-disabled)`
- [ ] Value text: `color: var(--content-disabled)`
- [ ] Opacity alone is NEVER the only disabled treatment for text

## Gate 6: Box-Shadow Borders

- [ ] Outlined borders: `box-shadow: inset 0 0 0 1px var(--border-*)`
- [ ] Focus ring: `box-shadow: 0 0 0 2px var(--primary-filled-bg)`
- [ ] No CSS `border` (exception: dashed borders only)
- [ ] Focus ring on 1px-outlined elements: no layout shift

## Gate 7: Hover on Inverted Surfaces

- [ ] No raw `rgba()` for hover overlays
- [ ] Uses `::before` pseudo-element with `currentColor` + opacity
- [ ] OR uses `color-mix(in srgb, var(--token) 15%, transparent)`
- [ ] `pointer-events: none` on the `::before` overlay

## Gate 8: Control Effectiveness

- [ ] Every tester control visibly changes the preview
- [ ] Default config renders a representative example
- [ ] Switching variants does not break layout
- [ ] State control maps to correct CSS classes

## Gate 9: Token Panel Accuracy

- [ ] Token function takes `config` parameter
- [ ] Branches per emphasis (filled/tonal/outlined/ghost show different tokens)
- [ ] Adds `--content-disabled` when state is disabled
- [ ] Adds `--danger-outline-border` + `--danger-outline-fg` when state is error
- [ ] Uses `outline` not `outlined` in token names
- [ ] Ghost emphasis has NO bg token (only fg)
- [ ] Every token name resolves in `globals-v4.css`

## Gate 10: Render Correctness

- [ ] Disabled state passes class to wrapper AND input AND children
- [ ] Error state applies to both input element AND wrapper (for label propagation)
- [ ] Selected/active/checked state applies correct CSS class
- [ ] Compound render types (range slider, date range) render all sub-elements

---

## Brand Compliance Quick Check

### Hulul (AA)

- [ ] Text on surfaces: 4.5:1 ratio
- [ ] Large text: 3:1 ratio
- [ ] UI components: 3:1 ratio
- [ ] Target size: >= 24px
- [ ] Focus visible
- [ ] Semantic HTML
- [ ] ARIA attributes
- [ ] Keyboard navigable

### WideBot (AAA)

Everything in Hulul PLUS:
- [ ] Text on surfaces: **7:1** ratio
- [ ] Large text: **4.5:1** ratio
- [ ] Target size: >= **44px**
- [ ] Focus indicator: **3:1** contrast against adjacent
- [ ] No information by color alone (icon/text accompanies every color signal)
- [ ] Tooltips/popovers: dismissible + hoverable + persistent

---

## Cross-Sprint Backport Check

After auditing current sprint, verify these patterns in ALL previous sprints:
- [ ] Any new `:focus-visible` rule applies to older interactive elements
- [ ] Any new RTL fix pattern applies to older positioned elements
- [ ] Any new compound state rule applies to older input components
- [ ] Any new disabled propagation pattern applies to older components with labels
