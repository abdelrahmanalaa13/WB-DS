# Pre-Build QC: Loading

**Component category**: Data Display
**Sprint**: D
**Dependencies**: none (standalone)
**Spec file**: `DS-COMPONENTS/data-display/loading.md`

---

## 1. State Matrix

### Spinner States

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default (rotating) | (none) | Continuous clockwise rotation | `--primary-filled-bg`, `--border-subtle`, `--radius-full` |
| Reduced motion | `@media (prefers-reduced-motion)` | Static, no rotation; opacity pulse instead | `--primary-filled-bg`, `--border-subtle`, `--motion-fast` |

### Progress Bar States (Determinate)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default (fill progresses 0-100%) | (none) | Fill bar grows left-to-right with percentage label | `--primary-filled-bg`, `--border-subtle`, `--content-tertiary` |
| At 0% | (none) | Empty track visible, label shows "0%" | `--border-subtle`, `--content-tertiary` |
| At 100% | (none) | Full bar, label shows "100%" | `--primary-filled-bg`, `--content-tertiary` |
| RTL (right-to-left) | `[dir="rtl"]` | Fill grows right-to-left instead | Same tokens, mirror direction |

### Progress Bar States (Indeterminate)

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default (shimmer) | (none) | Shimmer animation sweeps continuously left-to-right | `--primary-filled-bg`, `--border-subtle`, `--motion-slow` |
| RTL (right-to-left) | `[dir="rtl"]` | Shimmer sweeps right-to-left | Same tokens, mirror direction |
| Reduced motion | `@media (prefers-reduced-motion)` | Static progress bar, optional pulsing opacity | `--primary-filled-bg`, `--border-subtle` |

### Skeleton States

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default (pulsing) | (none) | Placeholder pulse oscillates opacity continuously | `--surface-sunken`, `--neutral-tonal-bg`, `--motion-slow` |
| Text line | `.skeleton-line` | Rectangular shape matching text height, pulsing | `--surface-sunken`, `--neutral-tonal-bg` |
| Circle (avatar) | `.skeleton-circle` | Circular shape with `--radius-full`, pulsing | `--surface-sunken`, `--neutral-tonal-bg` |
| Rectangle (card/image) | `.skeleton-rect` | Rectangular block, pulsing | `--surface-sunken`, `--neutral-tonal-bg` |
| Reduced motion | `@media (prefers-reduced-motion)` | Static opacity; no pulse animation | `--surface-sunken`, `--neutral-tonal-bg` |

### Typing Indicator Dots States

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Default (bouncing) | (none) | Three dots animate vertically in sequence | `--primary-filled-bg`, `--motion-normal` |
| Reduced motion | `@media (prefers-reduced-motion)` | Static dots, no animation; optional opacity fade in/out | `--primary-filled-bg` |

### Overlay States

| State | CSS Class/Pseudo | Visual Change | Tokens Consumed |
|-------|-----------------|---------------|-----------------|
| Inline (within content) | `.loading-inline` | Spinner/skeleton appears alongside content, no backdrop | N/A (uses inner component tokens) |
| Full-screen modal | `.loading-modal` | Spinner centered on semi-transparent backdrop, dims content | `--primary-filled-bg`, `--border-subtle`, `--radius-full` |
| Full-screen backdrop | `.loading-backdrop` | Semi-transparent dark overlay (token-based) | `--surface-overlay` or `rgba(0,0,0,0.3)` |

---

## 2. Variant Matrix

| Variant Axis | Values | What Changes |
|-------------|--------|-------------|
| Type | spinner, progress-bar, skeleton, dots | render structure, animation behavior, layout |
| Spinner size | sm (16px), md (24px), lg (40px) | stroke-width, radius scaling |
| Progress bar type | determinate, indeterminate | fill behavior, animation pattern |
| Progress label | with, without | percentage text visible or hidden |
| Skeleton shape | line, circle, rectangle, table-row | width/height constraints |
| Overlay | inline, full-screen | positioned absolutely or fixed, backdrop present or absent |

**Total combinations**:
- Spinner: 1 type × 3 sizes × 1 overlay mode = 3 variants
- Progress bar: 1 type × 2 variations (determinate/indeterminate) × 2 label options × 1 overlay = 4 variants
- Skeleton: 1 type × 4 shapes × 1 overlay = 4 variants
- Dots: 1 type × 1 size (fixed) × 1 overlay = 1 variant
- **Total base**: 12 variants
- **With reduced motion**: +12 motion alternatives
- **Total cells to verify**: ~24 + overlay combinations

---

## 3. Content Resilience

### Spinner

| Slot | Empty | Minimal | Normal | Overflow | Arabic (RTL) |
|------|-------|---------|--------|----------|--------|
| Aria label | Must have `aria-label="Loading"` or visually hidden text | Single word: "Loading" | "Loading [entity name]" | "Loading very long entity name that might wrap" | RTL text label direction flips, label still present |

**Behavior**: Spinner is purely visual. The accessible label is the only text content. Never show spinner without a label.

### Progress Bar with Percentage Label

| Slot | Empty | Minimal | Normal | Overflow | Arabic (RTL) |
|------|-------|---------|--------|----------|--------|
| Percentage text | "0%" | "50%" | "75%" | "100%" (max value, no overflow possible) | "٠٪" (Arabic numeral 0 and percent sign) |

**Behavior**: Label is numeric only. No text overflow; always fits within bar space or adjacent.

### Skeleton Shapes

| Slot | Empty | Minimal | Normal | Overflow | Arabic (RTL) |
|------|-------|---------|--------|----------|--------|
| Text line | Single line, height constrained | "W" (1 char width) | "Lorem ipsum dolor sit amet" | "Lorem ipsum dolor sit amet consectetur adipiscing elit sed do eiusmod tempor..." (long text truncated by line width) | Arabic text flows RTL, line width same, height same |
| Avatar circle | No content slot (shape only) | N/A | N/A | N/A | N/A |
| Card rectangle | No content slot (shape only) | N/A | N/A | N/A | N/A |
| Table row | Multiple line placeholders stacked | 2 lines | 3-4 lines | 5+ lines (stacks vertically, no horizontal overflow) | RTL stacking same |

---

## 4. RTL Verification Points

| Element | LTR Property | RTL Equivalent | Verify |
|---------|-------------|---------------|--------|
| Progress bar fill direction | left-to-right sweep in shimmer | right-to-left sweep | No hardcoded `left`/`right`; use `direction: rtl` on element or logical transform |
| Typing indicator dots baseline | Centered baseline | Centered baseline (no horizontal flip needed) | Animation order same, visual centering same |
| Full-screen overlay positioning | Spinner centered via `left: 50%` + `transform: translateX(-50%)` | Centered via `inset-inline: 0; margin: auto` or `inset-inline-start/end` | No physical `left`/`right`; use logical properties or `position: absolute; inset: 0; margin: auto` |
| Skeleton in header/sidebar | Element order LTR | Element order RTL (flex-direction reversal happens at container level) | No skew or directional rotation on skeleton shapes themselves |

---

## 5. Accessibility Checklist

### Semantic HTML

| Element | Required Tag | ARIA Attributes |
|---------|-------------|-----------------|
| Spinner wrapper | `<div>` or `<span>` | `role="status"`, `aria-label="Loading"` or `aria-live="polite"` + visually hidden text |
| Progress bar wrapper | `<div>` | `role="progressbar"`, `aria-valuenow="50"`, `aria-valuemin="0"`, `aria-valuemax="100"`, `aria-label="Loading [name]"` |
| Skeleton wrapper | `<div>` | `role="status"`, `aria-busy="true"`, optional `aria-label` if in a specific context |
| Typing indicator | `<div>` | `role="status"`, `aria-label="Typing indicator"` or `aria-live="polite"` |

### Keyboard Interaction

| Key | Action |
|-----|--------|
| Tab | N/A -- Loading components are non-interactive status indicators, not focusable |
| Escape | N/A |
| Enter | N/A |
| Space | N/A |

### Screen Reader Announcements

- Spinner: Screen reader announces "Loading" when `aria-label` is present; if using `aria-live="polite"`, announces when inserted into DOM
- Progress bar: Announces role, current value, and range ("Progress bar, 50 percent")
- Skeleton: Announces "Loading..." via `aria-busy="true"` or `aria-live="polite"` + label
- Typing indicator: Announces "Typing..." when inserted

### Brand-Specific Requirements

| Requirement | Hulul (AA) | WideBot (AAA) |
|-------------|-----------|--------------|
| Text contrast (percentage label) | `--content-tertiary` must have 4.5:1 against surrounding surface | `--content-tertiary` must have 7:1 against surrounding surface (may fail; flag if < 7:1) |
| Target size (if clickable dismiss) | N/A (loading components are not interactive by default) | N/A |
| Focus ring contrast | N/A (not focusable) | N/A |
| Color independence | N/A (progress bar is status-only; actual work completion % must be in text) | N/A |

**Critical**: If overlay is dismissible or has a close button, that button must meet 24px (Hulul) or 44px (WideBot) target size and have visible focus ring.

---

## 6. Token Validation

### Required Token References

| Token | Usage | Exists in globals-v4.css? |
|-------|-------|--------------------------|
| `--primary-filled-bg` | Spinner fill color, progress bar fill, typing dots | VERIFY |
| `--border-subtle` | Spinner track ring, progress bar track | VERIFY |
| `--surface-sunken` | Skeleton background base color | VERIFY |
| `--neutral-tonal-bg` | Skeleton pulse/shimmer color | VERIFY |
| `--content-tertiary` | Progress bar percentage label text color | VERIFY |
| `--radius-full` | Spinner circular shape | VERIFY |
| `--radius-sm` | Progress bar rounded corners | VERIFY |
| `--motion-slow` | Spinner rotation infinite, skeleton pulse infinite | VERIFY |
| `--motion-normal` | Typing dots bounce sequence | VERIFY |
| `--surface-overlay` | Full-screen modal backdrop (optional; may use rgba fallback) | VERIFY |

### Token Panel Requirements

The token panel function must:
- [ ] Accept `config` parameter with shape, size, overlay, reducedMotion, direction properties
- [ ] Show static tokens for all types: `--primary-filled-bg`, `--border-subtle`, `--surface-sunken`, `--neutral-tonal-bg`
- [ ] Add motion tokens when `reducedMotion === false`: `--motion-slow`, `--motion-normal`
- [ ] Omit motion tokens when `reducedMotion === true` (opacity-only fallback, no duration tokens)
- [ ] Show `--content-tertiary` only when progress bar type is active AND label is enabled
- [ ] Show `--radius-full` for spinner type; `--radius-sm` for progress bar type
- [ ] Show `--surface-overlay` only when overlay mode is "full-screen"
- [ ] Token names must match exactly what's in globals-v4.css (e.g., `--motion-slow` not `--motion-duration-slow`)

---

## 7. CSS Architecture

### Borders and Focus Rings

- [ ] Spinner track uses `box-shadow: inset 0 0 0 1px var(--border-subtle)` (not CSS `border`)
- [ ] Progress bar track uses `box-shadow: inset 0 0 0 1px var(--border-subtle)` if visible
- [ ] If loading overlay has a dismiss button, that button uses `box-shadow: 0 0 0 2px var(--primary-filled-bg)` for focus ring

### Text Elements

- [ ] Progress bar percentage label has explicit `font-family: var(--font-sans)`, `font-size: var(--font-size-sm)`, `color: var(--content-tertiary)`
- [ ] If skeleton is placed in a specific context with descriptive text, that text uses proper font tokens
- [ ] Spinner aria-label is visually hidden via `.sr-only` or `position: absolute; clip: rect(0,0,0,0)`, not `display: none` or `visibility: hidden`

### State Classes

- [ ] Spinner in overlay mode may have `.loading-overlay` class to apply fixed positioning and backdrop
- [ ] Skeleton pulsing uses `animation: skeletonPulse var(--motion-slow) infinite`
- [ ] Progress bar indeterminate uses `animation: progressShimmer var(--motion-slow) infinite`
- [ ] Typing dots use `animation: dotsBounce var(--motion-normal) infinite`
- [ ] All animations reference motion tokens, not hardcoded durations

### Motion and Reduced Motion

- [ ] Every animation has `@media (prefers-reduced-motion: reduce)` override
- [ ] Spinner reduced motion: replace rotation with `opacity: 0.6` static state
- [ ] Progress bar indeterminate reduced motion: optional fade in/out opacity animation (slow pulse)
- [ ] Skeleton reduced motion: opacity static at `0.6` or slow opacity pulse
- [ ] Typing dots reduced motion: opacity fade pulse, dots don't bounce
- [ ] No animation duration < `--motion-fast` (even for reduced motion; `--motion-fast` itself is safe for reduced motion)

### No Raw Values

- [ ] No hardcoded hex/rgb/rgba colors (except `rgba(0,0,0,0.X)` for overlay backdrop; verify if token exists)
- [ ] No hardcoded px values for spacing; icon sizing and track heights are acceptable exceptions
- [ ] Spinner stroke-width: hardcoded 2px (component-specific dimension, not spacing)
- [ ] Progress bar height: hardcoded 4px or 6px (component-specific dimension)
- [ ] Skeleton height: hardcoded per shape (text-line 12px, circle 40px for lg size, etc.)

### Disabled Propagation

- [ ] N/A -- Loading components do not have disabled state; they are status indicators

---

## 8. Motion

### Animations and Durations

| Interaction | Animation | Duration Token | Easing Token | Keyframes |
|------------|-----------|----------------|-------------|-----------|
| Spinner rotation | `spin360` | `--motion-slow` | N/A (linear for rotation) | 0%: rotate(0deg), 100%: rotate(360deg) |
| Progress bar shimmer | `shimmerLTR` / `shimmerRTL` | `--motion-slow` | N/A (continuous sweep) | 0%: translateX(-100%), 100%: translateX(100%) |
| Skeleton pulse | `skeletonPulse` | `--motion-slow` | N/A (ease-in-out ok) | 0%: opacity(1), 50%: opacity(0.5), 100%: opacity(1) |
| Typing dots bounce | `dotsBounce` | `--motion-normal` | N/A (ease-in-out ok) | Stagger dots up/down 3 times per cycle |

### Reduced Motion Compliance

- [ ] All animations respect `@media (prefers-reduced-motion: reduce)`
- [ ] Spinner reduced motion: static display, no `animation` property
- [ ] Progress bar reduced motion: static or optional slow opacity pulse, no shimmer
- [ ] Skeleton reduced motion: static opacity, no pulse
- [ ] Typing dots reduced motion: static dots, no bounce
- [ ] No animation entirely removed; instead, replace with static or very slow opacity change if semantically important

---

## 9. Tester Integration

### Component Config Structure

```js
{
  type: 'spinner' | 'progress' | 'skeleton' | 'dots',
  size: 'sm' | 'md' | 'lg',  // for spinner and skeleton shapes
  progress: 0-100,  // for progress bar (determinate only)
  progressType: 'determinate' | 'indeterminate',
  showLabel: true | false,  // for progress bar
  shape: 'line' | 'circle' | 'rect',  // for skeleton
  overlay: 'inline' | 'full-screen',
  direction: 'ltr' | 'rtl',
  reducedMotion: true | false,
}
```

### Tester Controls

| Control | Options | Expected Visual Change |
|---------|---------|----------------------|
| Type dropdown | spinner, progress, skeleton, dots | Render switches between component variants |
| Size slider (spinner/skeleton) | sm, md, lg | Spinner radius/stroke scale; skeleton heights scale |
| Progress value slider | 0-100 | Fill bar grows; percentage label updates |
| Progress type toggle | determinate, indeterminate | Determinate: bar fills to value; indeterminate: shimmer animates |
| Show label checkbox | on/off | Percentage text appears/disappears (progress bar only) |
| Skeleton shape dropdown | line, circle, rect | Shape outline changes; width/height constraints change |
| Overlay mode toggle | inline, full-screen | Position changes, backdrop appears/disappears, spinner centering |
| Direction toggle | LTR, RTL | Progress shimmer/fill direction flips; text label direction flips; overlay centering adjusts |
| Reduced motion toggle | on/off | Animations stop/start; opacity pulse replaces rotation/shimmer/bounce |

### Default Config (Representative Example)

```js
{
  type: 'spinner',
  size: 'md',
  progress: 50,
  progressType: 'determinate',
  showLabel: true,
  shape: 'line',
  overlay: 'inline',
  direction: 'ltr',
  reducedMotion: false,
}
```

### Control Effectiveness Verification

- [ ] Switching `type` visibly changes the entire render: spinner → progress bar shows filled section and percentage; progress bar → skeleton shows placeholder shapes
- [ ] Slider `size` visibly changes spinner/skeleton dimensions
- [ ] Slider `progress` moves the fill bar and updates the percentage label
- [ ] Toggle `progressType` switches between deterministic fill and shimmer animation
- [ ] Checkbox `showLabel` adds/removes the percentage text (progress bar only)
- [ ] Dropdown `shape` changes skeleton outline (line = tall thin rect, circle = round, rect = block)
- [ ] Toggle `overlay` switches spinner from inline (default) to full-screen with backdrop
- [ ] Toggle `direction` reverses progress bar fill direction and flips RTL-specific properties
- [ ] Toggle `reducedMotion` stops all animations, applies opacity or static state

### Token Panel Dynamic Updates

- [ ] Default config shows: `--primary-filled-bg`, `--border-subtle`, `--motion-slow` (spinner)
- [ ] Changing `type` to `progress` shows: `--primary-filled-bg`, `--border-subtle`, `--content-tertiary` (if label on), `--motion-slow` (if indeterminate)
- [ ] Changing `type` to `skeleton` shows: `--surface-sunken`, `--neutral-tonal-bg`, `--motion-slow`
- [ ] Changing `type` to `dots` shows: `--primary-filled-bg`, `--motion-normal`
- [ ] Toggling `overlay` to `full-screen` adds `--surface-overlay` to token list
- [ ] Toggling `reducedMotion` hides motion tokens; opacity fallback shown instead
- [ ] All token references resolve to actual CSS variable values from globals-v4.css

---

## Summary Checklist

Before building, ensure:

- [ ] All 9 state matrices defined (spinner, progress determinate/indeterminate, skeleton, dots, overlay)
- [ ] All 4 skeleton shapes included (line, circle, rect, table-row variant)
- [ ] Content resilience verified for text overflow, empty, and RTL cases
- [ ] RTL shimmer/fill direction will use logical properties, not physical left/right
- [ ] Accessibility: all components have `role="status"` and `aria-label` or `aria-live`
- [ ] Progress bar has `aria-valuenow`, `aria-valuemin`, `aria-valuemax` attributes
- [ ] All token references exist in globals-v4.css
- [ ] Motion tokens used: `--motion-slow`, `--motion-normal`
- [ ] Reduced motion media query implemented for all animations
- [ ] CSS uses inset box-shadow for spinner/progress track borders
- [ ] No hardcoded duration values; all use token references
- [ ] Tester controls visibly affect render output
- [ ] Token panel updates dynamically based on type/overlay/reducedMotion state
- [ ] Spinner rotation: 360deg over `--motion-slow` duration
- [ ] Progress shimmer sweeps full width left-to-right (LTR) or right-to-left (RTL)
- [ ] Skeleton pulse opacity oscillates between 1.0 and 0.5 (or similar)
- [ ] Typing dots bounce sequence animates all 3 dots sequentially
- [ ] Focus ring on any interactive close/dismiss button: `box-shadow: 0 0 0 2px var(--primary-filled-bg)`
