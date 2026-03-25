# Empty States Component - Pre-Build QC Checklist

**Layer:** L6
**Spec Version:** 1.0
**Date:** 2026-03-25

---

## 1. Component Structure & Architecture

- [ ] Component exports named export `EmptyState` as default
- [ ] Anatomy layers correctly nested: IllustrationContainer (centered SVG or large icon) → Title → Description → PrimaryActionCTA → SecondaryActionLink
- [ ] All dependencies imported: Button, Avatar (for user context variants)
- [ ] Props interface defined with all variants: `variant: 'noData' | 'noResults' | 'error' | 'permission'`
- [ ] Tone prop: `tone: 'encouraging' | 'helpful' | 'apologetic'` (maps to messaging strategy)
- [ ] Size prop: `size: 'fullPage' | 'inline' | 'compact'` (layout/spacing scaling)
- [ ] Props for customization: `title?: string`, `description?: string`, `illustrationUrl?: string`, `onPrimaryAction?: () => void`, `primaryActionLabel?: string`, `onSecondaryAction?: () => void`, `secondaryActionLabel?: string`
- [ ] State management: static component (no internal state beyond loading illustration)

---

## 2. Token Integration (Foundations)

- [ ] Surface token: `--surface-default` inherited from parent (no override unless nested context)
- [ ] Title text: `--content-secondary` applied to title element
- [ ] Description text: `--content-tertiary` applied to description paragraph
- [ ] Primary CTA button: `--primary-filled-bg` background (delegated to Button component)
- [ ] Illustration background circle: `--neutral-tonal-bg` (subtle background behind icon/SVG)
- [ ] Spacing gaps: `--space-6` (illustration to title gap), `--space-3` (title to description), `--space-5` (description to action)
- [ ] No hardcoded spacing values (all use tokens)
- [ ] Token panel variables created in design file
- [ ] CSS variable declarations verified in component stylesheet
- [ ] Secondary action text link: inherits --primary-filled-bg or custom --link-primary token

---

## 3. Universal Hover & Focus States (U1-U3)

**Primary Action Button:**
- [ ] Button element: `:hover` only with `.hover` class (U1)
- [ ] Button element: `:focus-visible` with visible focus ring (U2)
- [ ] Button disabled state: `.disabled` class suppresses `.hover` (U3)

**Secondary Action Text Link:**
- [ ] Text link: `:hover` only with `.hover` class
- [ ] Text link: `:focus-visible` with underline or focus ring
- [ ] Text link disabled: no `.hover` state when `.disabled` applied

**Illustration Container:**
- [ ] No hover state (passive element)
- [ ] No focus state required

**Disabled Button Hover Suppression:**
- [ ] Primary action disabled: `.disabled` + `.hover` does not apply visual change
- [ ] Secondary action disabled: hover suppression verified
- [ ] Icon within button: no separate hover behavior

---

## 4. Compound States for Interactive Elements (U5)

- [ ] Primary action button states: default, `.hover`, `:focus-visible`, disabled, loading (if async)
- [ ] Secondary action link states: default, `.hover`, `:focus-visible`, disabled
- [ ] Title state: consistent across all variants (no state variations)
- [ ] Description state: consistent across all variants (no state variations)
- [ ] Illustration state: consistent (no state variations)
- [ ] Size scaling (fullPage/inline/compact): affects layout, not interactive states
- [ ] Variant messaging (noData/noResults/error/permission): affects text content, not interactive states
- [ ] Tone modulation: affects messaging emphasis, not interactive element behavior

---

## 5. Box-Shadow Borders (U6)

- [ ] Primary action button: shadow/border delegated to Button component (no local override)
- [ ] Secondary action text link: no box-shadow required (text-only link)
- [ ] Illustration background circle: optional subtle box-shadow for depth (use --shadow-sm if applied)
- [ ] No `border` property used on main container
- [ ] Container itself: no shadow/border (inherits parent container styling)
- [ ] Shadow values exported as CSS custom properties where applied
- [ ] Tested for contrast in both light and dark modes

---

## 6. Typography & Text Rendering (U7)

- [ ] Title: explicit font-family, font-size (18-24px varies by size), line-height 1.2-1.3, font-weight 600-700
- [ ] Title color: `--content-secondary` token applied
- [ ] Description: explicit font-family, font-size (14-16px), line-height 1.4-1.5, font-weight 400
- [ ] Description color: `--content-tertiary` token applied
- [ ] Primary action button label: typography delegated to Button component (explicit weight/size in Button)
- [ ] Secondary action link label: explicit font-weight (500), font-size matching description or slightly smaller
- [ ] Secondary link color: `--primary-filled-bg` or `--link-primary` token
- [ ] Illustration alt text (SVG): semantic `<title>` or `aria-label` on container
- [ ] No text truncation in title or description (full wrapping expected)

---

## 7. Disabled State Propagation (U8)

- [ ] Primary action button: `:disabled` attribute sets `.disabled` class behavior
- [ ] Secondary action link: can be independently disabled (e.g., no support contact available)
- [ ] When disabled, both actions: opacity reduction or --content-disabled color token
- [ ] Disabled primary action: cursor not-allowed
- [ ] Disabled secondary action: cursor not-allowed or default (text link behavior)
- [ ] Disabled state does not affect title, description, or illustration
- [ ] Visual indicator clear that action is unavailable

---

## 8. Truncation & Text Overflow (U9)

- [ ] Title: no truncation required (wrap fully, max-width set by container)
- [ ] Description: no truncation required (wrap fully, allow multi-line paragraphs)
- [ ] Primary action button label: optional truncation if very long (delegated to Button component)
- [ ] Secondary action link label: optional truncation if very long
- [ ] All text elements: `word-wrap: break-word` or `overflow-wrap: break-word` for safety
- [ ] No hardcoded line-clamp values (use natural wrapping)
- [ ] Container max-width set per size variant to prevent excessive line length

---

## 9. Logical Properties & Directional Styling (U10-U13)

**Positioning & Layout:**
- [ ] Main container: `display: flex; flex-direction: column; align-items: center` (centered layout)
- [ ] Illustration: centered with `margin-inline: auto` (not `margin-left/right`)
- [ ] Title/description: `text-align: center` (or `text-align: start` if left-aligned variant desired)
- [ ] Action buttons (if stacked vertically): `gap: var(--space-3)` or `margin-block-start`
- [ ] Flex alignment: `justify-content: center` and `align-items: center`

**Border Radius:**
- [ ] Illustration background circle: `border-radius: 50%` (perfect circle)
- [ ] Button elements: radius delegated to Button component

**Spacing:**
- [ ] Illustration to title gap: `margin-block-end: var(--space-6)` or gap in flex container
- [ ] Title to description: `margin-block-end: var(--space-3)` or gap
- [ ] Description to primary action: `margin-block-start: var(--space-5)` or gap
- [ ] Primary action to secondary action: `margin-block-start: var(--space-2)` (close together)
- [ ] Horizontal padding (fullPage variant): `padding-inline: var(--space-8)` or similar
- [ ] Horizontal padding (inline/compact variants): `padding-inline: var(--space-4)`

**Alignment (Logical):**
- [ ] Text alignment: `text-align: center` (default) or `text-align: start` for left variants
- [ ] Icons/illustrations: center-aligned via flex
- [ ] All properties use logical names (not left/right/top/bottom)

---

## 10. Motion & Accessibility (U14, No Motion, U19)

**Motion Design:**
- [ ] Component is static: NO animations or transitions
- [ ] Illustration appears instantly (no fade-in)
- [ ] Title and description render instantly
- [ ] Action buttons display without animation
- [ ] If illustration is external image: loading state shows placeholder, then swaps instantly
- [ ] Rationale: empty states are not "entry" states; instant feedback is appropriate

**Reduced Motion (U14):**
- [ ] No @media prefers-reduced-motion needed (component has no motion)
- [ ] Component is inherently accessible for motion-sensitive users
- [ ] Illustration background circle (if shadow applied): no animation on it

**No Raw Values (U15):**
- [ ] All spacing use --space-* tokens (not hardcoded px)
- [ ] All colors reference tokens (not #hex, rgb)
- [ ] All typography explicit and token-referenced where possible
- [ ] All shadows use --shadow-* tokens (if applied)
- [ ] All radii use --radius-* tokens

**RTL & Directional Icons (U19):**
- [ ] Text-align: center (already RTL-agnostic)
- [ ] Illustration: centered (no directional flip needed)
- [ ] Icons in buttons (primary/secondary): standard icons, no directional flip
- [ ] If "learn more" or "try again" link has arrow icon: icon should NOT flip in RTL (intent direction stays same)
- [ ] Avatar (if used in variant): no directional bias
- [ ] Logical properties handle RTL margin/padding automatically

**Accessibility (Bonus):**
- [ ] Illustration: `aria-hidden="true"` if purely decorative
- [ ] Title: semantic `<h2>` or appropriate heading level based on page context
- [ ] Description: semantic `<p>` tag
- [ ] Primary action: `<button>` semantic element (delegated to Button component)
- [ ] Secondary action: `<a>` semantic link element
- [ ] No redundant ARIA labels (action buttons already have labels)

---

## Sign-Off

- [ ] All 10 sections reviewed
- [ ] No universal rules (U1-U19) violations found
- [ ] No motion implementations (static component confirmed)
- [ ] Ready for component build phase
- [ ] Outstanding items logged as GitHub issues/dev notes

**Reviewed By:** ________
**Date:** ________

