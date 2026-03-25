# Brand Compliance Reference

This document defines the WCAG compliance requirements for each brand in the design system. Every component must pass these checks for BOTH brands since the same component library serves both.

---

## Brand Overview

| Brand | Market | WCAG Level | Primary Use Cases |
|-------|--------|-----------|-------------------|
| Hulul | SMB (small-to-medium business) | WCAG 2.1 AA | CRM, helpdesk, basic ERP |
| WideBot | Enterprise + Government | WCAG 2.1 AAA | Full ERP, CCaaS, AI platform |

Both brands share the same component code. The difference is in token values (colors, spacing) defined in `globals-v4.css` per brand. The auditor must verify that each brand's token values meet its WCAG level.

---

## Hulul -- WCAG 2.1 AA Compliance

### Color Contrast (1.4.3 AA)

| Element Type | Minimum Ratio | How to Verify |
|-------------|---------------|---------------|
| Normal text (<18px regular, <14px bold) | 4.5:1 | Check `--content-primary`, `--content-secondary`, `--content-tertiary` against their background surface tokens |
| Large text (>=18px regular, >=14px bold) | 3:1 | Check heading tokens against surface tokens |
| UI components (borders, icons, controls) | 3:1 | Check `--border-default`, `--border-subtle`, icon color tokens against adjacent surface |
| Placeholder text | 4.5:1 | Check `--content-tertiary` against `--surface-default` and `--surface-sunken` |
| Disabled elements | No requirement | Disabled elements are exempt from contrast requirements per WCAG |

### Focus Indicators (2.4.7 AA)

- Every interactive element must have a visible focus indicator
- Focus ring: `box-shadow: 0 0 0 2px var(--primary-filled-bg)` provides sufficient visibility
- The focus indicator must be visible against both light and dark backgrounds
- Minimum 2px width for focus outline/ring

### Target Size (2.5.8 AA -- Level AA target size)

| Element Type | Minimum Size |
|-------------|-------------|
| Buttons, links, interactive controls | 24x24px minimum touch target |
| Icon-only buttons | 24x24px minimum (the icon can be smaller, but the clickable area must meet this) |
| Form inputs | Height >= 36px recommended, 24px minimum |
| Checkbox/Radio actual hit area | 24x24px (wrapper, not just the visual box) |

### Keyboard Navigation (2.1.1 AA)

- All interactive elements reachable via Tab/Shift+Tab
- Enter activates buttons and links
- Space activates checkboxes, toggles, radio buttons
- Arrow keys navigate within composite widgets (tabs, menus, radio groups, date picker grids)
- Escape closes overlays (dialog, popover, menu, tooltip)
- No keyboard traps

### Error Identification (3.3.1 AA)

- Errors identified by text message, not color alone
- Error text uses `--danger-outline-fg` token
- Error border uses `--danger-outline-border` token
- Error icon accompanies error text where possible
- Error messages are associated with their input via `aria-describedby`

### ARIA Requirements (4.1.2 AA)

- All interactive elements have accessible name (visible label, `aria-label`, or `aria-labelledby`)
- Dynamic content changes announced via `aria-live` regions
- Role attribute correct for non-standard elements
- State attributes updated: `aria-expanded`, `aria-selected`, `aria-checked`, `aria-current`, `aria-disabled`

---

## WideBot -- WCAG 2.1 AAA Compliance

WideBot must meet everything Hulul meets (AA) PLUS the following enhanced requirements.

### Enhanced Color Contrast (1.4.6 AAA)

| Element Type | Minimum Ratio | How to Verify |
|-------------|---------------|---------------|
| Normal text (<18px regular, <14px bold) | **7:1** | Check all `--content-*` tokens against all `--surface-*` tokens in WideBot theme |
| Large text (>=18px regular, >=14px bold) | **4.5:1** | Check heading tokens |
| UI components | 3:1 | Same as AA |
| Links within text | 3:1 contrast against surrounding text OR underlined | Check `--primary-filled-bg` against `--content-primary` |

Token pairs to verify for WideBot (light theme):
- `--content-primary` on `--surface-default` >= 7:1
- `--content-primary` on `--surface-sunken` >= 7:1
- `--content-primary` on `--surface-raised` >= 7:1
- `--content-secondary` on `--surface-default` >= 7:1
- `--content-tertiary` on `--surface-default` >= 4.5:1 (often fails at AAA -- flag if < 7:1)
- `--primary-filled-fg` on `--primary-filled-bg` >= 7:1
- `--danger-filled-fg` on `--danger-filled-bg` >= 7:1
- All role `outline-fg` tokens on `--surface-default` >= 7:1

Token pairs to verify for WideBot (dark theme):
- Same pairs as light, but dark theme values

### Enhanced Focus Indicators (2.4.13 AAA)

- Focus indicator must have **3:1 contrast** against adjacent colors (not just be visible)
- Focus indicator must enclose the entire component (not just underline)
- Minimum area of focus indicator: at least as large as a 2px solid border around the component
- Focus indicator must not be obscured by other content

Verification method:
- `var(--primary-filled-bg)` must have 3:1 contrast against `--surface-default` (light theme)
- `var(--primary-filled-bg)` must have 3:1 contrast against `--surface-default` (dark theme)
- Focus ring must not be hidden by adjacent elements (z-index check)

### Enhanced Target Size (2.5.5 AAA)

| Element Type | Minimum Size |
|-------------|-------------|
| All interactive elements | **44x44px** minimum touch/click target |
| Icon-only buttons | 44x44px (padding increases hit area even if icon is 20px) |
| Form inputs | Height >= 44px |
| Checkbox/Radio wrapper | 44x44px (can be achieved with padding on wrapper) |
| Close/dismiss buttons | 44x44px |
| Pagination buttons | 44x44px |
| Calendar day cells | 44x44px |
| Stepper step circles | Already 32px visual + should have 44px click area |

Components where this matters most:
- Icon Button `xs` size: visual 28px, needs 44px click area via padding
- Tag remove button: visual 14px, needs 44px click area
- Toast dismiss: visual 20px, needs 44px click area
- File upload item remove: visual 20px, needs 44px click area
- Pagination buttons: currently 36px, must be 44px for WideBot

### Color Independence (1.4.1 AA -- but critical for AAA)

No information may be conveyed by color alone. Every color signal must have a secondary indicator:

| Color Signal | Required Secondary Indicator |
|-------------|------------------------------|
| Error state (red border) | Error text message + error icon |
| Success state (green) | Success text + checkmark icon |
| Warning state (amber) | Warning text + warning icon |
| Selected state (primary color) | Checkmark, bold weight, or aria-selected |
| Active step in stepper | Step number + bold label + ring |
| Today in date picker | Border ring (not just color change) |
| In-range days in date picker | Background tint + position context |
| Progress bar | Percentage text alongside the bar |
| File upload error | Error text below file name |

### Content on Hover or Focus (1.4.13 AA -- required for AAA path)

For tooltips, popovers, and any content that appears on hover/focus:
- Content must be dismissible (Escape key)
- Content must be hoverable (user can move mouse to the popup without it disappearing)
- Content must be persistent (stays until user dismisses, moves focus, or the trigger condition is removed)

### Error Prevention (3.3.4 AA -- critical for enterprise)

For forms that submit data:
- Reversible: user can undo
- Checked: input validated before submission
- Confirmed: user can review before final submit

This applies to the component design patterns, not individual atoms. But the auditor should flag if a form-related component (Text Field, Selection Controls, Date Picker, Tag Input) lacks the hooks for validation feedback.

---

## Theme-Specific Verification

Both brands support Light and Dark themes. The auditor must verify compliance in BOTH themes because:

- Dark theme often has lower contrast ratios for `--content-tertiary`
- Dark theme `--primary-filled-bg` may have different contrast against `--surface-default`
- Hover overlays behave differently (light overlay on dark vs dark overlay on light)
- `::before` opacity patterns must work in both directions

### How to Verify Token Contrast Programmatically

For each token pair, resolve the CSS variable to its computed hex value, then calculate the WCAG contrast ratio:

```
contrast_ratio = (L1 + 0.05) / (L2 + 0.05)
where L1 = relative luminance of lighter color
      L2 = relative luminance of darker color
```

Flag any pair below the threshold for the brand's WCAG level.

---

## Direction-Specific Verification

Both brands support LTR (English) and RTL (Arabic).

### Arabic RTL Requirements

- All layout properties use CSS logical properties
- Chevrons and directional icons mirror
- Date picker range-start/range-end border-radius flips
- Badge overlay position flips (inset-inline-end, not right)
- Text alignment uses `start`/`end`, not `left`/`right`
- Breadcrumb separator points left in RTL
- Navigation order reverses visually but not in DOM (Tab order stays logical)
- Arabic text in RTL variants must use real Arabic words, not lorem ipsum

---

## Audit Severity Levels

| Severity | Description | Action |
|----------|-------------|--------|
| CRITICAL | Fails WCAG at the brand's level. Blocks release. | Must fix immediately |
| HIGH | Violates a learned rule from DS_LEARNING_LOOP.md. Regression. | Must fix before next sprint |
| MEDIUM | Missing enhancement (e.g., no focus-visible on a secondary element) | Fix in current round |
| LOW | Cosmetic or best-practice improvement | Track for future |
| INFO | Observation, no action needed | Record in learning loop |
