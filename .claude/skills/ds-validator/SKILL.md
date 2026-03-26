---
name: ds-validator
description: Run U1-U19 design system compliance checks against ds-library.html. Use when the user says 'validate', 'audit', 'compliance check', 'run U rules', 'check the DS', or after making changes to ds-library.html. This skill analyzes every component for conformance to all 19 universal construction rules and produces a detailed pass/fail report per rule per component.
---

# DS Validator

This skill runs the complete U1-U19 design system compliance audit against ds-library.html and reports pass/fail status for each rule on each component.

## How to Use

1. Make sure `ds-library.html` exists in the working directory
2. Trigger this skill when you want to validate the design system against the universal checklist
3. Review the report to identify compliance gaps

## Compliance Rules Reference

The skill checks these 19 rules:

**Interactive rules (U1-U6)** - Components with `:hover`, click handlers, or inputs:
- **U1**: Every `:hover` has a matching `.hover` class
- **U2**: Every focusable element has `:focus-visible` with `box-shadow: 0 0 0 2px`
- **U3**: `.disabled:hover` and `.disabled.hover` suppress hover
- **U4**: Hover on filled surfaces uses `::before` or `color-mix()`, not raw `rgba()`
- **U5**: Input components define `.error:hover`, `.focused.error`, `.disabled:hover`
- **U6**: Borders use `box-shadow: inset`, not CSS `border` (exception: dashed borders)

**Text rules (U7-U9)** - Elements rendering text:
- **U7**: Every text element has explicit font-family, font-size, and color (all tokens)
- **U8**: Disabled parent propagates `--content-disabled` to ALL child text elements
- **U9**: Overflowable text has all three: text-overflow, white-space, overflow

**Positioning rules (U10-U13)** - Positioned elements:
- **U10**: No `left`/`right` in positioning, only `inset-inline-start`/`inset-inline-end`
- **U11**: No physical directional border-radius
- **U12**: No `margin-left`/`margin-right`/`padding-left`/`padding-right`, only logical
- **U13**: No `text-align: left`/`right`, only `start`/`end`

**Animation rules (U14)**:
- **U14**: Components with movement animations have `prefers-reduced-motion` rules

**Component-level rules (U15-U19)**:
- **U15**: No raw hex, rgb(), rgba(), or raw px for spacing (exceptions: component-specific dimensions)
- **U16**: Dynamic token panel branches per emphasis and includes state-specific tokens
- **U17**: State classes (.hover, .focused, .error, .disabled) exist alongside pseudo-classes
- **U18**: Wrapper state propagation for label/helper components
- **U19**: Directional icons mirror in RTL via `[dir="rtl"]` with scaleX(-1)

## Report Format

The skill produces a compliance report with this structure:

```
# DS Validator Report
Generated: [date]
Source: ds-library.html

## Summary
Total components: X
Total checks: X
Passed: X
Failed: X
Warnings: X

## Results by Component

### ds-button
- [PASS] U1: Dual hover selectors
- [PASS] U2: Focus-visible ring
- [FAIL] U8: Missing --content-disabled for .ds-button-badge when disabled
- [SKIP] U16: Token panel (not applicable for this component)
...

### ds-text-field
...

## Failed Rules Summary
| Rule | Component | Issue |
|------|-----------|-------|
| U8 | ds-button | Missing disabled propagation for badge child |
| U10 | ds-modal | Uses left/right instead of inset-inline-start/end |
...

## Warnings
- ds-button: No prefers-reduced-motion check despite animation property
...
```

## How It Works

1. **Component Detection**: Scans HTML for elements with CSS class prefix `ds-`
2. **Rule Applicability**: For each component, determines which rules apply based on:
   - Presence of `:hover` pseudo-classes or `.hover` classes → U1-U6
   - Text rendering elements → U7-U9
   - Position properties → U10-U13
   - Animation properties → U14
   - All components → U15-U19
3. **CSS Analysis**: Inspects style rules in both `<style>` tags and external sheets
4. **Pass/Fail Logic**:
   - **PASS**: Rule requirement fully met
   - **FAIL**: Rule requirement violated
   - **SKIP**: Rule not applicable to this component
   - **WARN**: Potential issue that may need manual review
5. **Report Generation**: Aggregates findings into a summary table and detailed per-component breakdown

## Edge Cases & Assumptions

- **Inherited styles**: The checker looks at computed styles where possible. Inline styles and class-based styling are both considered.
- **Dynamic classes**: State classes (`.hover`, `.focused`, etc.) are assumed to be applied via JavaScript or form state; the checker verifies their CSS definitions exist.
- **Token references**: Uses simple text matching to verify token usage (e.g., checking for `var(--` prefix).
- **RTL icons**: Looks for `[dir="rtl"]` selectors and `scaleX(-1)` or `rotate()` transforms.
- **Raw values**: Detects raw hex colors, rgb/rgba, and pixel values; allows exceptions for component-specific dimensions.

## Limitations

- Cannot fully evaluate U16 (token panel) without access to the dynamic token panel function itself
- Cannot verify U18 wrapper propagation if styles are dynamically applied
- RTL checks (U19) only detect explicit `[dir="rtl"]` selectors, not media queries
- Does not evaluate runtime behavior (e.g., actual hover/focus interaction)

## Next Steps

After reviewing the report:
1. Fix any FAIL issues before the next build
2. Resolve WARN items with manual review
3. Re-run validation after changes to confirm fixes

---

**Instructions for Claude**:

Read ds-library.html, extract all components by CSS class prefix `ds-`, and perform static analysis of their CSS rules. For each component, check applicability of U1-U19 and mark each as PASS, FAIL, SKIP, or WARN. Generate the report structure shown above with detailed findings and a summary table of failures.
