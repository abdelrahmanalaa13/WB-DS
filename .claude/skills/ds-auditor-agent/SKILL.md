---
name: ds-auditor-agent
description: "Design system QC auditor that runs before and after building components. Use this skill whenever the user says 'audit', 'QC', 'test cases', 'pre-build check', 'post-build audit', 'compliance check', 'accessibility audit', 'review component', 'check component quality', 'generate test cases for', 'what cases do I need', 'verify the build', 'run the checklist', or any phrase involving checking, validating, or auditing design system components. Also trigger when the user is about to build a new sprint of components and needs the required test cases generated first, or has just finished building and needs everything verified. This skill is the quality gate for the entire ds-library.html component library."
---

# DS Auditor Agent

You are the design system quality control engine. You enforce every rule the system has learned across 6 sprints of building, auditing, and fixing components. You operate in three modes:

1. **Pre-Build Mode** -- Generate all required test cases and acceptance criteria BEFORE a component is built
2. **Build-Time Mode** -- Inline verification during construction. The builder reads `DS_UNIVERSAL_CHECKLIST.md` and verifies each rule per-element as CSS/JS is written. This mode is not run separately; it is embedded in the build process.
3. **Post-Build Mode** -- Audit completed components against every learned rule, find issues, and produce fix instructions

## CRITICAL WORKFLOW CHANGE

The old workflow was: Pre-Build -> Build -> Post-Build. This caused repeated failures because the builder did not consume pre-build output or the learning loop during construction.

The new workflow is:

```
Pre-Build QC -----> Builder reads DS_UNIVERSAL_CHECKLIST.md
                    |
                    v
                    For each CSS rule written:
                    - Interactive element? Verify U1-U6
                    - Text element? Verify U7-U9
                    - Positioned element? Verify U10-U13
                    - Animated element? Verify U14
                    |
                    v
                    Component complete? Verify U15-U19
                    |
                    v
                    Post-Build Audit (catches anything missed)
```

The universal checklist is the bridge between pre-build planning and post-build auditing. It converts learned knowledge into build-time constraints.

## How to Determine Mode

- If the user mentions a component that has status `pending` in the roadmap, or says "build", "create", "implement", or "next sprint" -- use **Pre-Build Mode**, then remind the builder to read `DS_UNIVERSAL_CHECKLIST.md` during construction
- If the user mentions a component that has status `done`, or says "audit", "check", "QC", "verify", "review" -- use **Post-Build Mode**
- If ambiguous, ask which mode

## File Locations

- **Universal checklist (BUILD-TIME GATE)**: `DS_UNIVERSAL_CHECKLIST.md` -- 19 rules, must be read before writing any CSS/JS
- **Token source of truth**: `lovable-kit/globals-v4.css`
- **Component library**: `ds-library.html`
- **Component specs**: `DS-COMPONENTS/<category>/<component>.md`
- **Roadmap**: `DS-COMPONENTS/COMPONENT_ROADMAP.md`
- **Learning loop**: `DS_LEARNING_LOOP.md`
- **Brand compliance**: Read `references/brand-compliance.md` in this skill directory

Before running either mode, always read:
1. `DS_UNIVERSAL_CHECKLIST.md` (the 19 universal rules -- this is the most important file)
2. `DS_LEARNING_LOOP.md` (the full decision ledger)
3. `references/brand-compliance.md` from this skill's directory
4. The component spec file from `DS-COMPONENTS/`

---

## Pre-Build Mode

When a component is about to be built, generate a complete test case document covering everything that must be verified after the build. This document becomes the post-build audit input.

### Step 1: Read Component Spec

Read the spec file from `DS-COMPONENTS/<category>/<component>.md`. Extract:
- All valid tokens
- All variants (emphasis, size, color role, type)
- All states
- Dependencies
- Forbidden patterns

### Step 2: Generate State Matrix

For every component, produce the full state matrix. This is the "kitchen sink" -- every state crossed with every variant that exists for this component.

**Universal states** (apply to all interactive components):
- default
- hover (both `:hover` and `.hover` class)
- focused (`:focus-visible` with focus ring)
- active/pressed
- disabled

**Input-specific states** (apply to components that accept user input):
- error
- error + hover (border must lock, not change to default hover)
- focused + error (focus ring shows danger color, not primary)
- disabled + hover (must revert to default, no visual change)

**Selection states** (apply to components with selected/checked/on behavior):
- selected/checked/on
- selected + hover
- selected + disabled

For each cell in the matrix, write:
- What CSS should apply
- What tokens are consumed
- What the visual result should be

### Step 3: Generate Content Resilience Cases

For every text-rendering slot in the component:
- **Empty**: What happens with no content? Does the layout collapse gracefully?
- **Minimal**: Single character or word. Does it still look right?
- **Normal**: Typical content length
- **Overflow**: Very long text (50+ characters for labels, 200+ for body). Does it truncate with ellipsis? Does it wrap? Does `text-overflow: ellipsis` have `white-space: nowrap` and `overflow: hidden`?
- **Special characters**: Arabic text, numbers, emoji, mixed bidi text

### Step 4: Generate RTL Test Cases

- Every `position: absolute` element uses `inset-inline-start`/`inset-inline-end`, never `left`/`right`
- Every directional `border-radius` uses logical properties (`border-start-start-radius` etc.)
- Every `margin-left`/`margin-right`/`padding-left`/`padding-right` uses `margin-inline-start` etc.
- Every directional icon (chevron, arrow) mirrors via `transform: scaleX(-1)` or CSS logical property
- `text-align: left` becomes `text-align: start`
- Transition properties reference `inset-inline-start`, not `left`

### Step 5: Generate Accessibility Test Cases

Read `references/brand-compliance.md` for brand-specific requirements, then generate:

**For both brands (Hulul AA + WideBot AAA):**
- Semantic HTML elements used (button not div, nav not div, etc.)
- ARIA attributes present where needed (aria-label, aria-current, aria-expanded, etc.)
- Every interactive element has `:focus-visible` with `box-shadow: 0 0 0 2px var(--primary-filled-bg)`
- Keyboard navigation works (Tab, Shift+Tab, Enter, Space, Arrow keys as appropriate)
- Screen reader announces component role and state

**WideBot-specific (WCAG AAA):**
- Contrast ratio 7:1 for normal text, 4.5:1 for large text
- Contrast ratio 3:1 for all UI components and graphical objects
- Focus indicator is at least 2px and has 3:1 contrast against adjacent colors
- No information conveyed by color alone (icons, text, or patterns must accompany color)
- Target size minimum 44x44px for all interactive elements
- Error identification by text, not just color

**Hulul-specific (WCAG AA):**
- Contrast ratio 4.5:1 for normal text, 3:1 for large text
- Contrast ratio 3:1 for UI components
- Focus indicator visible (2px ring)
- Target size minimum 24x24px (WCAG 2.5.8 at AA)

### Step 6: Generate Token Validation Cases

- Every CSS variable referenced in the component exists in `globals-v4.css`
- Token panel function takes `config` parameter and branches per emphasis and per state
- Token names match actual CSS variable pattern: `outline` not `outlined`, ghost has no `bg` token
- Disabled state adds `--content-disabled` to token panel
- Error state adds `--danger-outline-border` and `--danger-outline-fg`
- No raw hex/rgb/rgba values anywhere in CSS (use `color-mix()` or `currentColor` + `::before` opacity)
- No raw `px` values for spacing (use `--space-*` tokens). Acceptable exceptions: component-specific dimensions like thumb size, track height, icon size where no spacing token exists

### Step 7: Generate CSS Architecture Cases

- Borders use `box-shadow: inset 0 0 0 1px` not CSS `border` (exception: dashed borders like File Upload)
- Focus ring uses `box-shadow: 0 0 0 2px var(--primary-filled-bg)` or `inset 0 0 0 2px` for contained elements
- Every text-rendering element has explicit `font-family: var(--font-sans)`, `font-size`, and `color`
- Disabled state: never rely on `opacity` alone. All text elements must switch to `--content-disabled`
- Wrapper class propagates state to children: `.label-disabled`, `.label-error`, `.label-focused`
- Hover on filled/inverted surfaces uses `::before` overlay with `currentColor` + `opacity`, not raw `rgba()`
- State classes (`.hover`, `.focused`, `.error`, `.disabled`) exist alongside pseudo-classes
- `text-overflow: ellipsis` always paired with `white-space: nowrap` and `overflow: hidden`

### Step 8: Generate Motion Cases

- All transitions use token durations: `var(--motion-fast)`, `var(--motion-standard)`, `var(--motion-slow)`
- All easings use tokens: `var(--ease-standard)`, `var(--ease-enter)`, `var(--ease-exit)`, `var(--ease-spring)`
- `prefers-reduced-motion` is respected (transitions reduce to 0ms or opacity-only)
- Enter/exit animations exist for overlay components (popover, menu, tooltip, dropdown)

### Step 9: Generate Tester Integration Cases

- Every control in the tester config visibly changes the preview output
- Default config renders a representative example
- Switching between variants does not break layout
- Token panel updates dynamically based on current config (emphasis, state, color)

### Step 10: Universal Checklist Mapping

For each element in the component, map it to the applicable universal rules from `DS_UNIVERSAL_CHECKLIST.md`. This table is the build-time contract.

```
| Element | Interactive? | Text? | Positioned? | Animated? | Applicable Rules |
|---------|-------------|-------|-------------|-----------|-----------------|
| .header-btn | YES | YES | NO | NO | U1,U2,U3,U4,U5,U6,U7,U8,U9,U17 |
| .label | NO | YES | NO | NO | U7,U8,U9 |
| .chevron | YES | NO | YES | YES | U1,U2,U10,U14,U19 |
```

This table tells the builder exactly which rules to check for each CSS class they write. It converts the universal checklist from a document to a per-element verification matrix.

### Output Format

Output a markdown document titled `## Pre-Build QC: [Component Name]` with sections matching Steps 2-10 above. Each section contains a numbered checklist. The final section (Step 10) contains the element-to-rule mapping table. Save to `DS-COMPONENTS/QC/<component>-prebuild.md`.

---

## Post-Build Mode

After a component (or sprint of components) is built, run the full audit.

### Step 1: Read All Inputs

1. Read the pre-build QC document if it exists (`DS-COMPONENTS/QC/<component>-prebuild.md`)
2. Read the component's CSS from `ds-library.html`
3. Read the component's JS render function and config from `ds-library.html`
4. Read `DS_LEARNING_LOOP.md` for the full decision ledger
5. Read `references/brand-compliance.md`

### Step 2: Run CSS Audit

Check every CSS rule for the component against these gates:

**Gate 1: RTL Safety**
Grep for physical properties in the component's CSS block:
- `left:` or `right:` (should be `inset-inline-start`/`inset-inline-end`)
- `margin-left`/`margin-right` (should be `margin-inline-start`/`margin-inline-end`)
- `padding-left`/`padding-right` (should be `padding-inline-start`/`padding-inline-end`)
- `text-align: left`/`right` (should be `start`/`end`)
- `border-radius` with directional values (should use logical properties)
- `float: left`/`right` (should be `float: inline-start`/`inline-end`)
- Transition on `left`/`right` (should be `inset-inline-start`/`inset-inline-end`)

Exceptions: `translateX()` is acceptable for self-centering transforms.

**Gate 2: Token Compliance**
Grep for raw values in the component's CSS:
- Hex colors (#fff, #000, etc.)
- rgb()/rgba() values
- Hard-coded font families
- px values not in: width/height of icons, thumb dimensions, track heights, or other inherently fixed component-specific sizes

**Gate 3: Text Element Coverage**
For every element that renders text (labels, values, helpers, counters, descriptions, badges, tags):
- Has `font-family: var(--font-sans)` (or `--font-mono` for code)
- Has `font-size: var(--font-size-*)`
- Has `color: var(--content-*)` or role-specific color token
- Verify by checking every CSS class that could contain text

**Gate 4: State Completeness**
For every interactive component, verify CSS rules exist for:
- `:hover` and `.hover`
- `:focus-visible` with box-shadow focus ring
- `:disabled` or `.disabled`
- `.error` (for inputs)
- `.error:hover` / `.error.hover` (border locked)
- `.focused.error` (danger focus ring)
- `.disabled:hover` / `.disabled.hover` (reverts to default)

**Gate 5: Disabled Propagation**
For components with labels/helpers/values:
- Wrapper has `.disabled` or `.label-disabled` class
- Label color switches to `--content-disabled`
- Helper text switches to `--content-disabled`
- Counter text switches to `--content-disabled`
- Value text switches to `--content-disabled`
- Error text switches to `--content-disabled` when error+disabled

**Gate 6: Box-Shadow Borders**
- Outlined borders use `box-shadow: inset 0 0 0 1px var(--border-*)`, not CSS `border`
- Focus ring uses `box-shadow: 0 0 0 2px var(--primary-filled-bg)`
- Exception: dashed borders (cannot be done with box-shadow)

**Gate 7: Hover on Inverted Surfaces**
For components rendered on filled/tonal backgrounds:
- No raw `rgba()` for hover overlay
- Uses `::before` with `currentColor` + opacity, or `color-mix()`

### Step 3: Run JS/Render Audit

**Gate 8: Control Effectiveness**
For each control in the component config:
- Toggle every option and verify the render output changes visibly
- Check that the `stateClass` logic correctly maps control values to CSS classes

**Gate 9: Token Panel Accuracy**
- Token function takes `config` parameter (not static)
- Branches per emphasis (filled, tonal, outlined, ghost each show different tokens)
- Adds state-specific tokens (disabled: `--content-disabled`, error: `--danger-*`)
- Token names match actual CSS variables in `globals-v4.css`

**Gate 10: Render Correctness**
- Disabled state passes correct classes to wrapper AND children
- Error state adds both error class on input AND error label/helper propagation
- Selected/active state applies correct CSS class
- Range/compound types render all sub-elements

### Step 4: Run Brand Compliance Audit

For each brand, verify the component meets its WCAG level. Read `references/brand-compliance.md` for the full checklist. Key checks:

**Hulul (WCAG AA):**
- All text color tokens resolve to 4.5:1+ contrast against their background in both light and dark themes
- Interactive elements meet 24x24px minimum target size
- Focus indicators are visible

**WideBot (WCAG AAA):**
- All text color tokens resolve to 7:1+ contrast against their background in both light and dark themes
- Interactive elements meet 44x44px minimum target size
- Focus indicators have 3:1 contrast against adjacent colors
- No information conveyed by color alone

### Step 5: Cross-Reference Universal Checklist

Read `DS_UNIVERSAL_CHECKLIST.md` and verify every applicable rule (U1-U19) against the component. This replaces the old "read the learning loop" step. The universal checklist is a distilled, actionable version of the learning loop.

For each failure found, reference the rule number:
- "Violates U1 (dual hover selectors)" not "missing .hover class"
- "Violates U8 (disabled color propagation)" not "disabled text not grayed out"
- "Violates U10 (logical positioning)" not "uses left instead of inset-inline-start"

This creates a traceable link between the failure and the rule, making it clear that the issue was a known, documented constraint that was not applied during construction.

Additionally, cross-reference `DS_LEARNING_LOOP.md` for any component-specific decisions that go beyond the universal rules (e.g., specific token choices, component composition patterns, density behavior).

### Step 6: Produce Audit Report

Output a structured report with:

```
## Post-Build Audit: [Component Name]

### Summary
- Total checks: N
- Passed: N
- Failed: N
- Warnings: N

### Failures (must fix)
1. [Gate X] [Rule UNN] Description of issue
   - Rule violated: U1/U2/.../U19 from DS_UNIVERSAL_CHECKLIST.md
   - File: ds-library.html, line ~NNNN
   - Current: `what exists`
   - Required: `what should exist`
   - Fix: exact CSS/JS to add or change

### Warnings (should fix)
1. Description
   - Recommendation

### Brand Compliance
| Check | Hulul (AA) | WideBot (AAA) |
|-------|-----------|--------------|
| Text contrast | PASS/FAIL | PASS/FAIL |
| Target size | PASS/FAIL | PASS/FAIL |
| Focus indicator | PASS/FAIL | PASS/FAIL |
| Color independence | N/A | PASS/FAIL |

### New Learnings
Any patterns discovered during this audit that should be added to DS_LEARNING_LOOP.md.
```

### Step 7: Apply Fixes

After producing the report, if failures exist:
1. Apply each fix to `ds-library.html`
2. Re-run the failed gates to verify
3. Update `DS_LEARNING_LOOP.md` with any new learnings

---

## Sprint-Level Audit

When auditing an entire sprint (multiple components at once), run Post-Build Mode for each component, then add a cross-component check:

1. **Shared pattern consistency**: Do all components in the sprint use the same patterns for the same problems? (e.g., all inputs use the same disabled propagation, all overlays use the same enter animation)
2. **Token coverage overlap**: Are the same tokens used consistently across similar components?
3. **Backport check**: Do any rules discovered during this sprint's audit apply to components from previous sprints? If yes, list the affected components and required fixes.
4. **Learning loop update**: Compile all new learnings from the sprint audit into a single new section in `DS_LEARNING_LOOP.md`
