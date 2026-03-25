# DS Universal Construction Checklist

This is the mandatory build-time gate. Every component must pass every applicable rule in this file DURING construction, not after. This file is extracted from DS_LEARNING_LOOP.md and contains ONLY rules that apply to every component regardless of type.

The builder must verify each rule per-element as CSS/JS is written. The post-build auditor verifies compliance. If a rule is violated, the auditor references the rule number from this file.

---

## How to Use This File

1. Before writing any CSS for a new component, read this entire file.
2. For each interactive element you create, check rules U1-U6.
3. For each text element you create, check rules U7-U9.
4. For each positioned element, check rules U10-U13.
5. For each animated element, check rule U14.
6. For each component overall, check rules U15-U19.
7. After building, run the post-build auditor. It will reference these rule numbers.

---

## Interactive Element Rules (U1-U6)

Every element that responds to user interaction (buttons, links, rows, headers, nodes, checkboxes, sliders, any element with a click/tap handler or tabindex).

**U1: Dual hover selectors**
Every `:hover` pseudo-class MUST have a corresponding `.hover` class selector.
```css
/* WRONG */
.my-element:hover { background: var(--neutral-tonal-bg); }

/* RIGHT */
.my-element:hover,
.my-element.hover { background: var(--neutral-tonal-bg); }
```
Reason: The tester forces states via class, not actual hover. Without `.hover`, the tester cannot show hover state.
Source: Wave 1, Sprint C Audit, Sprint D Audit (missed 3 times).

**U2: Focus-visible ring**
Every element that can receive keyboard focus (buttons, links, inputs, elements with tabindex="0") MUST have:
```css
.my-element:focus-visible {
  outline: none;
  box-shadow: 0 0 0 2px var(--primary-filled-bg);
}
```
For elements inside a bordered container, use `inset 0 0 0 2px` instead.
Source: Sprint C Audit, Sprint D Audit.

**U3: Disabled hover suppression**
Disabled elements MUST NOT show hover visual feedback:
```css
.my-element.disabled:hover,
.my-element.disabled.hover { background: transparent; /* or default bg */ cursor: not-allowed; }
```
Source: Sprint B learnings, Sprint D Audit.

**U4: Hover overlay pattern**
Hover on filled/inverted surfaces MUST use `::before` with `currentColor + opacity` or `color-mix()`. Never raw `rgba()`.
```css
/* WRONG */
.my-element:hover { background: rgba(255,255,255,0.15); }

/* RIGHT - option A */
.my-element::before { content: ''; position: absolute; inset: 0; background: currentColor; opacity: 0; pointer-events: none; transition: opacity var(--motion-fast) var(--ease-standard); }
.my-element:hover::before { opacity: 0.08; }

/* RIGHT - option B */
.my-element:hover { background: color-mix(in srgb, currentColor 8%, transparent); }
```
Source: Wave 1, Full Library Audit.

**U5: Compound states for inputs**
Every input-type component (text field, tag input, date picker, file upload, or any component with error state) MUST define:
- `.error:hover` / `.error.hover` -- border LOCKED (does not change to default hover)
- `.focused.error` -- danger focus ring (2px `--danger-outline-border`)
- `.disabled:hover` / `.disabled.hover` -- reverts to default
Source: Sprint B, Sprint C Audit.

**U6: Box-shadow borders, not CSS border**
Outlined borders use `box-shadow: inset 0 0 0 1px var(--border-*)`, not CSS `border`.
Exception: dashed borders (File Upload) which cannot be produced with box-shadow.
Source: Wave 1.

---

## Text Element Rules (U7-U9)

Every element that renders visible text (labels, values, helpers, counters, descriptions, badges, tags, titles, metadata, trailing text).

**U7: Explicit typography**
Every text element MUST have all three properties explicitly set. Never rely on CSS inheritance.
```css
.my-text {
  font-family: var(--font-sans);
  font-size: var(--font-size-sm); /* appropriate size token */
  color: var(--content-secondary); /* appropriate content token */
}
```
Source: Sprint B learnings, Full Library Audit.

**U8: Disabled color propagation**
When a parent component is disabled, ALL child text elements MUST switch to `--content-disabled`. Opacity alone is never sufficient.
```css
.my-component.disabled .my-label { color: var(--content-disabled); }
.my-component.disabled .my-helper { color: var(--content-disabled); }
.my-component.disabled .my-value { color: var(--content-disabled); }
.my-component.disabled .my-trailing { color: var(--content-disabled); }
.my-component.disabled .my-icon { color: var(--content-disabled); }
.my-component.disabled .my-badge { color: var(--content-disabled); }
```
Check: enumerate every CSS class inside the component that renders text or icons. Each one needs a disabled rule.
Source: Sprint B, Sprint C Audit, Sprint D Audit (missed 3 times).

**U9: Truncation trio**
Any text that can overflow MUST have all three properties together:
```css
.my-text { text-overflow: ellipsis; white-space: nowrap; overflow: hidden; }
```
Missing any one of the three makes truncation fail silently.
Source: Full Library Audit.

---

## Positioning Rules (U10-U13)

Every element using `position: absolute/fixed/sticky` or directional CSS properties.

**U10: Logical positioning**
Never use `left`/`right` for positioning. Always use `inset-inline-start`/`inset-inline-end`.
```css
/* WRONG */
.my-overlay { position: absolute; right: 0; }

/* RIGHT */
.my-overlay { position: absolute; inset-inline-end: 0; }
```
Exception: `translateX()` for self-centering is acceptable.
Source: Sprint C Audit, Full Library Audit.

**U11: Logical border-radius**
Never use physical directional border-radius. Always use logical properties.
```css
/* WRONG */
.my-element { border-radius: 8px 0 0 8px; }

/* RIGHT */
.my-element { border-start-start-radius: 8px; border-end-start-radius: 8px; border-start-end-radius: 0; border-end-end-radius: 0; }
```
Source: Sprint C Audit (Date Picker range).

**U12: Logical spacing**
Never use `margin-left`/`margin-right`/`padding-left`/`padding-right`. Always use `margin-inline-start`/`margin-inline-end`/`padding-inline-start`/`padding-inline-end`.
Source: Multiple sprints.

**U13: Logical text alignment**
Never use `text-align: left`/`right`. Always use `text-align: start`/`end`.
Source: Sprint D (Data Grid numeric cells).

---

## Animation Rules (U14)

**U14: Reduced motion**
Every component with CSS animations or transitions that produce movement (not just color/opacity changes) MUST respect `prefers-reduced-motion`:
```css
@media (prefers-reduced-motion: reduce) {
  .my-animated-element { animation: none; transition-duration: 0.01ms; }
}
```
Color and opacity transitions are exempt (they don't cause motion sickness).
Source: Sprint D (Loading component).

---

## Component-Level Rules (U15-U19)

These apply to the component as a whole, not individual elements.

**U15: No raw values**
No hex colors, rgb(), rgba(), or raw px for spacing anywhere in component CSS. Use tokens exclusively.
Acceptable raw px: component-specific dimensions (icon size, thumb size, track height) where no spacing token exists.
Source: Full Library Audit.

**U16: Dynamic token panel**
Token panel function MUST:
- Accept a `config` parameter
- Branch per emphasis (filled/tonal/outlined/ghost show different tokens)
- Add state-specific tokens (disabled adds `--content-disabled`, error adds `--danger-*`)
- Use correct token name pattern (`outline` not `outlined`, ghost has no `bg` token)
Source: Sprint B, Full Library Audit.

**U17: State class pattern**
Components MUST define `.hover`, `.focused`, `.error`, `.disabled` classes alongside their pseudo-class equivalents. These classes enable the tester to force states without actual user interaction.
Source: Wave 1 (this is the foundational rule that keeps being missed).

**U18: Wrapper state propagation**
For components with labels/helpers/counters, use a wrapper class to cascade state:
- `.label-disabled` on wrapper -> label, helper, counter all get `--content-disabled`
- `.label-error` on wrapper -> label gets `--danger-outline-fg`
Source: Sprint B (Text Field), Sprint C (Tag Input).

**U19: RTL directional icons**
Any icon that implies direction (chevron, arrow, caret) MUST mirror in RTL via `[dir="rtl"]` selector with `transform: scaleX(-1)` or rotation adjustment.
Source: Breadcrumb learnings, Sprint D (Accordion chevron).

---

## Verification Method

During construction, for each CSS rule you write, ask:
1. Is this element interactive? Check U1-U6.
2. Does this element render text? Check U7-U9.
3. Does this element use positioning? Check U10-U13.
4. Does this element animate? Check U14.
5. Done with the component? Check U15-U19.

This is not a post-build audit. This is a build-time gate.
