---
name: ds-frontend-copilot
description: >
  Frontend engineering agent that ensures generated code follows design system rules. Validates
  Angular component implementations against the design system specification, token usage, and
  build-time quality rules (U1-U19). Use this skill when implementing components in Angular,
  validating frontend code against design system rules, checking token compliance in CSS/SCSS,
  reviewing component code for accessibility, or generating Angular component scaffolds from
  design system specs. Trigger when the user says "implement this component", "code review against
  DS", "check my Angular code", "scaffold component", "validate token usage", "CSS audit",
  "frontend implementation", "Angular component from spec", "design system code compliance",
  or any phrase involving frontend code quality validation against the design system.
---

# ds-frontend-copilot: Frontend Engineering and Code Quality Validation

## Role and Scope

This agent ensures all frontend code (Angular 21) follows the design system rules from ds-library.html and the U1-U19 checklist. Validates token usage, accessibility implementation, RTL support, and component structure.

Primary responsibilities:
- Review Angular component implementations against design system specs
- Validate CSS/SCSS token compliance (no raw colors, no hardcoded spacing)
- Audit accessibility attributes (ARIA, semantic HTML, keyboard support)
- Verify RTL implementation (logical properties, icon mirroring)
- Generate component scaffolds from design system specifications
- Enforce U1-U19 build-time rules in code review

## U1-U19 Quick Reference

The 19 build-time rules from DS_UNIVERSAL_CHECKLIST.md. Review every rule during code review:

### State Management (U1-U4)
- **U1**: Every :hover pseudo-class must have .hover class selector companion for testing
- **U2**: Every focusable element must have :focus-visible with box-shadow using --primary-filled-bg token
- **U3**: Disabled elements (.disabled:hover) must remove all hover visual feedback (background: transparent)
- **U4**: Hover on filled surfaces uses ::before with currentColor + opacity, never raw rgba()

### Layout and Spacing (U5-U6)
- **U5**: Input components define compound states (.error:hover, .focused.error, .disabled:hover)
- **U6**: Outlined borders use box-shadow: inset 0 0 0 1px var(--border-*), not CSS border property

### RTL and Logical Properties (U7-U13)
- **U7**: Explicit typography on every text element (font-family, font-size, color). Never inherit.
- **U8**: Disabled parent cascades --content-disabled to ALL child text (label, helper, counter, icon, badge)
- **U9**: Truncated text needs text-overflow: ellipsis + white-space: nowrap + overflow: hidden
- **U10**: Positioning uses inset-inline-start/end, never left/right
- **U11**: Border-radius uses logical properties (border-start-start-radius), never border-radius: x 0 0 x
- **U12**: Spacing uses margin-inline-* and padding-inline-*, never physical directions
- **U13**: Text alignment uses text-align: start/end, never left/right

### Motion and Animation (U14)
- **U14**: Movement animations respect @media (prefers-reduced-motion: reduce). Color/opacity transitions exempt.

### Component Integrity (U15-U19)
- **U15**: No raw values anywhere. All colors from tokens, all spacing from tokens, all sizes from tokens.
- **U16**: Token panel function branches per emphasis and adds state-specific tokens (disabled = --content-disabled, error = --danger-*)
- **U17**: State classes (.hover, .focused, .error, .disabled) defined alongside pseudo-class equivalents
- **U18**: Wrapper state propagation uses .label-disabled and .label-error on wrapper to cascade to child text
- **U19**: Directional icons (chevron, arrow) mirror in RTL via [dir="rtl"] selector with transform: scaleX(-1)

## Angular Component Scaffold

Standard template for new Angular components from design system spec: Use @Input/@Output for properties, apply U1-U19 rules in SCSS, include explicit typography (U7), state classes alongside pseudo-classes (U1, U17), disabled state (U3), RTL logical properties (U10-U13), motion prefers-reduced-motion (U14), and token-only values (U15). Template includes aria-label, disabled attribute, and keyboard event handlers (Enter, Space). SCSS uses token imports, box-shadow for borders (U6), state compounds (.error:hover, .focused.error), and [dir="rtl"] for icon mirroring (U19).

## Token Usage Validation

Every CSS/SCSS file must follow strict token rules. No exceptions.

### Incorrect patterns (VIOLATIONS):
```scss
.button { color: #1a1a1a; }  // Raw hex color (U15)
.button { margin-left: 16px; }  // Raw px value (U15)
.button { border: 1px solid #ccc; }  // Raw border (U6)
.button { border-radius: 4px 0 0 4px; }  // Raw radius (U11)
.button { transition: all 300ms ease; }  // Raw transition (U5)
[dir="rtl"] .button { margin-right: 16px; }  // Physical property in RTL (U12)
```

### Correct patterns:
```scss
.button { color: var(--content-default); }  // Token color (U15)
.button { margin-inline: var(--spacing-medium); }  // Token spacing (U12)
.button { box-shadow: inset 0 0 0 1px var(--border-default); }  // Token border (U6)
.button { border-start-start-radius: var(--border-radius-medium); }  // Logical radius (U11)
.button { transition: background-color var(--motion-standard) ease-out; }  // Token timing (U5)
// RTL automatic via logical properties, no selector needed
```

Validation checklist:
- [ ] No hex colors (use var(--color-*) tokens)
- [ ] No raw px values for spacing (use var(--spacing-*))
- [ ] No border property (use box-shadow with var(--border-*))
- [ ] No transition duration hardcoded (use var(--motion-*) tokens)
- [ ] No physical positioning (left, right, margin-left) - use inset-inline-*, margin-inline-*
- [ ] No physical border-radius - use logical properties (border-start-start-radius)

## Accessibility Implementation

Every Angular component must support accessibility. Use aria-label or linked labels for all interactive elements. Form inputs need @aria-label or linked @aria-labelledby. Custom selects require role="listbox" with role="option" for items and @aria-expanded for state. Error messages use aria-describedby with role="alert". Keyboard support: Tab/Shift+Tab navigate, Enter/Space trigger buttons, Arrow keys navigate lists, Escape closes modals (no keyboard traps). Auto-focus first element when dialogs open via ngAfterViewInit and @ViewChild. Test with NVDA/VoiceOver/JAWS across Chrome/Firefox/Safari to verify announcements.

## RTL Implementation

All components must work in right-to-left (RTL) interfaces. Use CSS logical properties: margin-inline-* (not margin-left/right), padding-inline-* (not padding-left/right), inset-inline-* (not left/right), border-inline-* (not border-left/right), border-start-start-radius (not border-radius: x 0 0 x), text-align: start/end (not left/right). Directional icons mirror via [dir="rtl"] .icon-class { transform: scaleX(-1); } (U19). Test layout with bidirectional text, form labels right-aligned in RTL mode, and all spacing uses logical properties.

## State Management Patterns

State classes must accompany pseudo-classes for testing compatibility (U1, U17). Define both :hover and .hover selectors with identical styling. Define :focus-visible and .focused, :disabled and .disabled, :active and .active. Compound states handle error + hover, error + focused, focused + error combinations (U5). Every state change uses token values from --color-* system. Disabled parent cascades --content-disabled to child text elements (U8).

## Code Review Checklist

Use this checklist when reviewing any Angular component against design system:

- [ ] U1-U19: All build-time rules pass (systematically check each)
- [ ] Accessibility: ARIA labels, keyboard support, focus management
- [ ] RTL: Logical properties used, icons mirror, no physical directions
- [ ] States: All documented states (hover, focus, active, disabled, error) implemented
- [ ] Motion: Animations respect prefers-reduced-motion
- [ ] Responsive: Component scales correctly (touch targets still 44px+)
- [ ] Tokens: No raw colors, no raw spacing, no raw timing values
- [ ] Template: Semantic HTML, proper heading hierarchy, form elements properly labeled
- [ ] Styling: SCSS organized, variables prefixed, no dead CSS
- [ ] Testing: Unit tests cover all states, accessibility tests pass

## Common Violations

Top 10 code violations with fixes:

### Violation 1: Using CSS border instead of box-shadow
```scss
// WRONG (U6)
.button { border: 1px solid var(--border-default); }

// CORRECT
.button { box-shadow: inset 0 0 0 1px var(--border-default); }
```

### Violation 2: Missing .hover class companion
```scss
// WRONG (U1)
.button:hover { background: var(--surface-hover); }

// CORRECT
.button:hover { background: var(--surface-hover); }
.button.hover { background: var(--surface-hover); }
```

### Violation 3: Raw color values
```scss
// WRONG (U15)
.button { color: #1a1a1a; }

// CORRECT
.button { color: var(--content-default); }
```

### Violation 4: Missing disabled hover state removal
```scss
// WRONG (U3)
.button:disabled:hover { background: var(--surface-disabled); }

// CORRECT
.button:disabled:hover { background: var(--surface-disabled); cursor: not-allowed; }
```

### Violation 5: Missing --content-disabled cascade
```scss
// WRONG (U8)
.button.disabled { opacity: 0.5; }

// CORRECT
.button.disabled {
  color: var(--content-disabled);
  background-color: var(--surface-disabled);
}
.button.disabled label,
.button.disabled .label { color: var(--content-disabled); }
```

### Violation 6: Physical properties in RTL
```scss
// WRONG (U12)
.button { margin-left: var(--spacing-medium); }

// CORRECT
.button { margin-inline-start: var(--spacing-medium); }
```

### Violation 7: Raw border-radius values
```scss
// WRONG (U11)
.button { border-radius: 4px 0 0 4px; }

// CORRECT
.button {
  border-start-start-radius: var(--border-radius-medium);
  border-end-start-radius: var(--border-radius-medium);
}
```

### Violation 8: Missing icon mirroring in RTL
```scss
// WRONG (U19)
.icon-chevron-right { /* no RTL rule */ }

// CORRECT
.icon-chevron-right { /* default LTR */ }
[dir="rtl"] .icon-chevron-right { transform: scaleX(-1); }
```

### Violation 9: Hardcoded motion duration
```scss
// WRONG (U5)
.button { transition: all 300ms ease-out; }

// CORRECT
.button { transition: background-color var(--motion-standard) ease-out; }
```

### Violation 10: Missing explicit typography
```scss
// WRONG (U7)
.button { /* inherits from parent */ }

// CORRECT
.button {
  font-family: var(--typography-family-default);
  font-size: var(--typography-size-body-medium);
  color: var(--content-default);
}
```

## Integration with Component Registry

Reference ai-component-registry.json to auto-generate component interfaces:

```typescript
// Read registry for button properties
const buttonSpec = registry.components.find(c => c.name === 'button');

// Auto-generate Input types
export class ButtonComponent {
  @Input() emphasis: buttonSpec.properties.emphasis.values[number];
  @Input() size: buttonSpec.properties.size.values[number];
  @Input() disabled: boolean;
  // ... generate from spec
}

// Validate all documented states implemented
buttonSpec.states.forEach(state => {
  // Ensure .{state} class and :{state} pseudo-class defined
});
```

This ensures components match the authoritative registry specification.
