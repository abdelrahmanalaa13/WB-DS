# Pre-Build QC: Notification Center (L6)

**Component Layer:** L6 (Composed)
**Date:** 2026-03-25
**Status:** Pre-Build Review

---

## 1. State Matrix

### Trigger (Bell Icon + Badge)
| State | Unread Count | Badge Display | Interactive | Notes |
|-------|--------------|---------------|-------------|-------|
| Idle | 0 | Hidden | Yes | No notifications |
| Idle | 1-9 | Visible (numeric) | Yes | Single/double digit |
| Idle | 10+ | Visible (9+) | Yes | Cap at 9+ |
| Hover | Any | Enhanced | Yes | Icon color shifts to primary |
| Focus | Any | Ring visible | Yes | :focus-visible required |
| Active | Any | Highlighted | Yes | Panel opening state |
| Disabled | Any | Reduced opacity | No | Prevented interaction |

### Panel Container
| State | Layout | Visibility | Interaction | Shadow |
|-------|--------|------------|-----------|----|
| Closed | N/A | Hidden | N/A | None |
| Opening | Slide from right | Visible | Blocked | --shadow-lg |
| Open | Full visible | Visible | Active | --shadow-lg (persistent) |
| Closing | Slide to right | Fading | Blocked | Fades |

### Notification Item
| State | Text Style | Unread Dot | Read Action | Actions Visible |
|-------|-----------|-----------|------------|-----------------|
| Unread | Bold title + secondary desc | Filled primary dot | Clickable | On hover |
| Read | Regular weight title | Removed/hidden | N/A | On hover |
| Actionable | Bold title + secondary | Primary dot present | Mark read | Always visible |
| Dismissed | Strikethrough (if kept) | Removed | N/A | Disabled |

### Filter Tabs
| Tab | Condition | Count Badge | Content Update | Sticky State |
|-----|-----------|------------|---------------|----|
| All | Show all notifications | Total count | Instant | Persists on reload |
| Unread | filter: unread === true | Unread count | Instant | Persists on reload |
| Mentions | filter: isMention === true | Mention count | Instant | Persists on reload |
| System | filter: type === 'system' | System count | Instant | Persists on reload |

---

## 2. Variant Matrix

### Desktop (Dropdown Panel)
| Aspect | Implementation | Constraints |
|--------|----------------|------------|
| **Trigger Placement** | Header/nav (fixed position) | Aligns to bell icon corner |
| **Panel Dimensions** | Width: 360px, Max-height: 80vh | Flex column layout, --radius-lg |
| **Position Logic** | Anchored to trigger, slide from right | RTL: slide from left |
| **Backdrop** | None (dropdown) | Click outside closes |
| **Scroll Behavior** | Items scroll, header + footer fixed | Height constraint triggers scroll |
| **Dismiss Action** | Close icon in header or escape key | No overlay dismiss |

### Mobile (Full-Screen Sheet)
| Aspect | Implementation | Constraints |
|--------|----------------|------------|
| **Trigger Placement** | Bottom nav or header | Full-screen on tap |
| **Panel Dimensions** | 100vw × 100vh (bottom sheet start) | Slides up to 90vh on mobile |
| **Position Logic** | Bottom sheet with drag handle | RTL: RTL text, standard motion |
| **Backdrop** | Semi-transparent overlay | Tap backdrop closes |
| **Scroll Behavior** | Full scroll, sticky header + footer | Smooth momentum scroll |
| **Dismiss Action** | Swipe down, back button, close icon | Tap backdrop also dismisses |

### Notification Types (Color-Coded)
| Type | Border Color | Icon Color | Use Case |
|------|-------------|-----------|----------|
| Info | --surface-raised (neutral) | --content-secondary | General updates |
| Success | --success-outline-border | --success-filled-bg | Confirmation, completed tasks |
| Warning | --warning-outline-border | --warning-filled-bg | Caution, warnings |
| Error | --danger-outline-border | --danger-filled-bg | Errors, failures |

---

## 3. Content Resilience

### Text Truncation
| Element | Max Width | Lines | Truncation Rule | Fallback |
|---------|-----------|-------|-----------------|----------|
| Notification Title | calc(100% - 60px) | 1 | Ellipsis with U9 trio | Tooltip on hover |
| Notification Description | calc(100% - 60px) | 2 | Ellipsis, break-word | Tooltip on hover |
| Timestamp | 60px | 1 | Right-aligned, no wrap | "Just now" if too long |
| Empty State Message | 100% - 2×--space-3 | Unlimited | Centered, no truncation | N/A |
| Mark All Read | Dynamic | 1 | No wrap, button text | Icon-only if needed |

### Avatar Fallbacks
- **With Image:** Display image (48px) with --radius-lg
- **No Image:** Display initials on --surface-sunken background
- **Broken Image:** Show placeholder icon, maintain 48px size
- **Multiple Notifications:** Stack up to 3 avatars, +N indicator for overflow

### Empty State Per Tab
| Tab | Message | Icon | CTA |
|-----|---------|------|-----|
| All | "No notifications yet" | Empty bell | None |
| Unread | "All caught up!" | Checkmark | None |
| Mentions | "No mentions" | At symbol | None |
| System | "No system alerts" | Info icon | None |

### Grouping Edge Cases
| Scenario | Behavior | Visual Indicator |
|----------|----------|-----------------|
| Single item in group | Show date header | Minimal divider |
| 50+ items same date | Paginate or virtual scroll | "Show more" link |
| Items with no source | Assign to "Other" | Fallback avatar |
| Cross-timezone times | Convert to user timezone | Timezone label if needed |

---

## 4. RTL Verification

### Layout Mirroring
| Element | LTR | RTL | Verification |
|---------|-----|-----|--------------|
| **Panel slide direction** | Right | Left | CSS logical properties (inset-inline) |
| **Trigger icon position** | Bell center | Bell center | No change (symmetric icon) |
| **Avatar position** | Left of text | Right of text | flex-direction: row-reverse or logical margin |
| **Unread dot position** | Right of text | Left of text | Logical positioning (inset-inline-end) |
| **Actions button group** | Right aligned | Left aligned | Logical direction on flex container |
| **Dismiss icon** | Top-right | Top-left | inset-inline-end / inset-inline-start |
| **Filter tabs** | Left-to-right | Right-to-left | Tab order reversed, scroll direction |
| **Scrollbar position** | Right | Left | Automatic (browser default for ::-webkit-scrollbar) |

### Directional Icons
- **Bell icon (trigger):** Non-directional (symmetric) ✓
- **Slide animation:** Use `scaleX(-1)` + motion or CSS logical transforms
- **Back/chevron arrows:** If used in footer, must flip in RTL
- **Mark all read link:** Text direction automatic, icon if present must flip

### Text Alignment
- **Titles:** logical start (flex-start)
- **Timestamps:** logical end (flex-end)
- **Descriptions:** logical start (flex-start)
- **Empty state text:** text-align: center (no change in RTL)

---

## 5. Accessibility

### WCAG Compliance
| Criterion | Implementation | Status |
|-----------|----------------|--------|
| **ARIA Landmarks** | `<button aria-label="Notifications">` trigger, `role="region" aria-label="Notifications panel"` panel | Required |
| **Focus Management** | Trigger → Panel → First unread item → Tab order loops | Required |
| **Keyboard Navigation** | Escape closes, Tab cycles through items, Enter/Space mark read | Required |
| **Screen Reader** | Announce unread count in trigger: `aria-label="Notifications, 5 unread"` | Required |
| **Color Contrast** | All borders/badges WCAG AA minimum (4.5:1 text, 3:1 graphical) | Required |
| **Motion Announce** | prefers-reduced-motion: disable slide, fade-in on appearance instead | Required |

### Focus Order (Desktop)
1. Trigger button
2. Close icon (if open)
3. Filter tabs (All → Unread → Mentions → System)
4. Notification items (top to bottom)
5. View All link (footer)

### Focus Order (Mobile)
1. Drag handle
2. Trigger button (to dismiss)
3. Filter tabs
4. Notification items
5. View All link

### Announcements
- **Panel open:** "Notifications panel opened"
- **Mark read:** "Notification marked as read"
- **Dismiss:** "Notification dismissed"
- **Filter change:** "Showing [Tab Name] notifications, [Count] items"
- **Empty state:** "No [Tab Name] notifications"

### Disabled State
- Trigger disabled: pointer-events: none, opacity: 0.5, aria-disabled: true
- Child text inherits disabled styling (U8)
- No hover effects apply

---

## 6. Token Validation

### Required Tokens (Must Be Defined)
| Token | Usage | Fallback | Required |
|-------|-------|----------|----------|
| `--surface-raised` | Panel background | #FFFFFF or light surface | Yes |
| `--surface-sunken` | Unread item background | lighten(--surface-raised, 5%) | Yes |
| `--shadow-lg` | Panel drop shadow | 0 20px 25px -5px rgba(0,0,0,0.1) | Yes |
| `--primary-filled-bg` | Unread dot, badge | Brand primary | Yes |
| `--success-outline-border` | Success type border | #10B981 | Yes |
| `--warning-outline-border` | Warning type border | #F59E0B | Yes |
| `--danger-outline-border` | Error type border | #EF4444 | Yes |
| `--content-primary` | Title text | #1F2937 | Yes |
| `--content-secondary` | Description text | #6B7280 | Yes |
| `--content-tertiary` | Timestamp text | #9CA3AF | Yes |
| `--border-subtle` | Divider color | #E5E7EB | Yes |
| `--space-3` | Item padding (compact) | 12px | Yes |
| `--radius-lg` | Panel border radius | 8px | Yes |

### Motion Tokens (Must Be Defined)
| Token | Usage | Value | Required |
|-------|-------|-------|----------|
| `--motion-normal` | Panel slide duration | 300ms ease-out | Yes |
| `--motion-fast` | Dot/dismiss animations | 150ms ease-out | Yes |

### Token Validation Checklist
- [ ] All 13 color/spacing tokens exist in design system
- [ ] 2 motion tokens defined for component motion
- [ ] Tokens applied via CSS custom properties (no hardcoded values) (U15)
- [ ] Fallback values documented for all tokens
- [ ] Dark mode variants tested (if system supports theme toggle)
- [ ] Contrast verified: --content-primary on --surface-raised ≥ 4.5:1

---

## 7. CSS Architecture

### Class Structure (BEM-like)
```
.notification-center                    // Root container
  .notification-center__trigger         // Bell icon button
  .notification-center__badge           // Badge on trigger
  .notification-center__panel           // Slide-in/sheet panel
    .notification-center__header        // Fixed header
      .notification-center__title       // "Notifications"
      .notification-center__mark-all    // "Mark all read" button
      .notification-center__close       // Close icon button
    .notification-center__tabs          // Filter tabs
      .notification-center__tab         // Individual tab
    .notification-center__list          // Scrollable list
      .notification-center__item        // Individual notification
        .notification-center__avatar    // Avatar image/initials
        .notification-center__content   // Text wrapper
          .notification-center__title   // Title text
          .notification-center__desc    // Description text
        .notification-center__timestamp // Timestamp
        .notification-center__dot       // Unread dot
        .notification-center__actions   // Action buttons
    .notification-center__footer        // Fixed footer
      .notification-center__view-all    // "View all" link
    .notification-center__empty         // Empty state

// Modifiers
.notification-center--open              // Panel visible
.notification-center--mobile            // Mobile variant
.notification-center--desktop           // Desktop variant
.notification-center__item--unread      // Unread state
.notification-center__item--read        // Read state
.notification-center__item--type-info   // Type variant
.notification-center__item--type-success
.notification-center__item--type-warning
.notification-center__item--type-error
```

### Universal Checklist Implementation

#### U1: Hover States
```css
.notification-center__item:hover {
  background: var(--surface-sunken);
}
.notification-center__item.hover {
  background: var(--surface-sunken);
}
/* No raw rgba() values - use color-mix or currentColor */
```

#### U2: Focus Visible
```css
.notification-center__trigger:focus-visible,
.notification-center__tab:focus-visible,
.notification-center__item:focus-visible {
  outline: 2px solid var(--primary-filled-bg);
  outline-offset: 2px;
}
```

#### U3: Disabled Hover Suppression
```css
.notification-center__trigger:disabled:hover,
.notification-center__trigger:disabled.hover {
  background: transparent;
  cursor: not-allowed;
}
```

#### U4: Hover Overlay (No Raw RGBA)
```css
.notification-center__item.hover::before {
  background: color-mix(in srgb, currentColor 8%, transparent);
}
```

#### U6: Box-Shadow Borders (Not CSS Border)
```css
.notification-center__panel {
  box-shadow: 0 0 0 1px var(--border-subtle),
              var(--shadow-lg);
}
.notification-center__item--type-error {
  box-shadow: inset -4px 0 0 0 var(--danger-outline-border);
}
```

#### U7: Explicit Typography
```css
.notification-center__title {
  font-family: var(--font-family-body);
  font-size: var(--font-size-sm);
  font-weight: 600;
  color: var(--content-primary);
  line-height: 1.5;
}
.notification-center__desc {
  font-family: var(--font-family-body);
  font-size: var(--font-size-xs);
  color: var(--content-secondary);
}
```

#### U8: Disabled Propagation
```css
.notification-center__trigger:disabled {
  opacity: 0.5;
}
.notification-center__trigger:disabled .notification-center__title {
  color: var(--content-tertiary);
}
```

#### U9: Truncation Trio
```css
.notification-center__title {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
.notification-center__desc {
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
```

#### U10: Logical Positioning
```css
.notification-center__panel {
  inset-block-start: 0;
  inset-inline-end: 0;
  /* Not: top, right */
}
.notification-center__dot {
  inset-inline-end: 12px;
  /* Not: right */
}
```

#### U11: Logical Border-Radius
```css
.notification-center__panel {
  border-start-start-radius: var(--radius-lg);
  border-start-end-radius: var(--radius-lg);
  border-end-start-radius: 0;
  border-end-end-radius: 0;
  /* Desktop variant - only top corners */
}
```

#### U12: Logical Spacing
```css
.notification-center__item {
  padding-block: var(--space-3);
  padding-inline: var(--space-3);
  /* Not: padding: top/right/bottom/left */
}
.notification-center__avatar {
  margin-inline-end: var(--space-3);
  /* Not: margin-right */
}
```

#### U13: Logical Text Alignment
```css
.notification-center__title {
  text-align: start;
  /* Not: text-align: left */
}
.notification-center__timestamp {
  text-align: end;
  /* Not: text-align: right */
}
```

#### U14: prefers-reduced-motion
```css
@media (prefers-reduced-motion: reduce) {
  .notification-center__panel {
    animation: none;
    transition: none;
  }
  .notification-center__item--unread .notification-center__dot {
    animation: none;
  }
}
/* Default: use --motion-normal and --motion-fast */
```

#### U15: No Raw Values
```css
/* CORRECT */
.notification-center__trigger {
  width: var(--size-trigger, 40px);
  padding: var(--space-2);
}

/* WRONG - avoid raw values */
.notification-center__trigger {
  width: 40px;
  padding: 8px;
}
```

#### U16: Dynamic Token Panel
```css
:root {
  --surface-raised: #FFFFFF;
  --surface-sunken: #F9FAFB;
  --shadow-lg: 0 20px 25px -5px rgba(0, 0, 0, 0.1);
  --primary-filled-bg: #3B82F6;
  --content-primary: #1F2937;
  --content-secondary: #6B7280;
  --content-tertiary: #9CA3AF;
  --border-subtle: #E5E7EB;
  --space-3: 12px;
  --radius-lg: 8px;
  --motion-normal: 300ms ease-out;
  --motion-fast: 150ms ease-out;
}

@media (prefers-color-scheme: dark) {
  :root {
    --surface-raised: #1F2937;
    --surface-sunken: #111827;
    --content-primary: #F3F4F6;
    --content-secondary: #D1D5DB;
    --content-tertiary: #9CA3AF;
  }
}
```

#### U17: State Class Pattern
```css
.notification-center__item.is-unread {
  background: var(--surface-sunken);
}
.notification-center__item.is-read {
  background: transparent;
}
.notification-center__panel.is-open {
  visibility: visible;
  opacity: 1;
}
.notification-center__panel.is-closed {
  visibility: hidden;
  opacity: 0;
}
```

#### U19: RTL Directional Icons
```css
.notification-center__panel {
  /* Slide from right (LTR) or left (RTL) */
  animation: var(--motion-normal) ease-out slideIn;
}

@keyframes slideIn {
  from {
    transform: translateX(100%); /* LTR: from right */
  }
  to {
    transform: translateX(0);
  }
}

[dir="rtl"] @keyframes slideIn {
  from {
    transform: translateX(-100%); /* RTL: from left */
  }
}

/* OR use logical transforms */
.notification-center__panel {
  transform: translateInline(100%); /* Browser handles RTL */
}
```

---

## 8. Motion

### Animation Library

#### Panel Slide (Desktop)
```css
.notification-center__panel {
  animation: nc-slide-in var(--motion-normal) ease-out both;
}

@keyframes nc-slide-in {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

[dir="rtl"] @keyframes nc-slide-in {
  from {
    transform: translateX(-100%);
  }
}
```

#### Panel Slide (Mobile / Sheet)
```css
.notification-center__panel--mobile {
  animation: nc-slide-up var(--motion-normal) ease-out both;
}

@keyframes nc-slide-up {
  from {
    transform: translateY(100%);
    opacity: 0;
  }
  to {
    transform: translateY(0);
    opacity: 1;
  }
}
```

#### New Notification Fade-In
```css
.notification-center__item.is-new {
  animation: nc-fade-in var(--motion-fast) ease-out both;
}

@keyframes nc-fade-in {
  from {
    opacity: 0;
    transform: translateY(-4px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

#### Mark Read Dot Fade
```css
.notification-center__dot {
  animation: nc-dot-fade-out var(--motion-fast) ease-out forwards;
}

@keyframes nc-dot-fade-out {
  from {
    opacity: 1;
    transform: scale(1);
  }
  to {
    opacity: 0;
    transform: scale(0.8);
  }
}
```

#### Dismiss Item Slide
```css
.notification-center__item.is-dismissing {
  animation: nc-slide-out-right var(--motion-fast) ease-out forwards;
}

@keyframes nc-slide-out-right {
  to {
    transform: translateX(100%);
    opacity: 0;
  }
}

[dir="rtl"] @keyframes nc-slide-out-right {
  to {
    transform: translateX(-100%);
  }
}
```

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  .notification-center__panel,
  .notification-center__item,
  .notification-center__dot {
    animation: none;
    transition: opacity var(--motion-fast) ease-out;
  }
}
```

---

## 9. Tester Integration

### Manual Testing Checklist

#### Rendering
- [ ] Panel renders at correct z-index (above fixed nav)
- [ ] Trigger badge counts display correctly (0, 1-9, 10+)
- [ ] Avatar images load or fallback to initials
- [ ] Type borders appear on correct side (left, 4px width)
- [ ] Unread dots visible only on unread items
- [ ] Empty state displays for tabs with no notifications

#### Interaction
- [ ] Trigger click opens panel
- [ ] ESC key closes panel
- [ ] Click outside panel closes it (desktop)
- [ ] Swipe down closes panel (mobile)
- [ ] Tab key cycles through focusable elements
- [ ] Enter/Space on item marks as read
- [ ] Hover shows action buttons on item
- [ ] Mark all read button clears all unread dots
- [ ] Filter tabs switch instantly
- [ ] View all link navigates to notification center page

#### Variants
- [ ] Desktop: dropdown panel 360px wide, right-aligned
- [ ] Mobile: bottom sheet, 90vh height, full width
- [ ] Type colors: Info (neutral), Success (green), Warning (yellow), Error (red)

#### States
- [ ] Unread: bold title, filled dot, --surface-sunken background
- [ ] Read: regular weight, no dot, transparent background
- [ ] Actionable: dot visible, action buttons available
- [ ] Disabled trigger: no hover, pointer-events none, 0.5 opacity

#### Content Resilience
- [ ] Long titles truncate with ellipsis
- [ ] Long descriptions show 2 lines max
- [ ] Missing images show placeholder avatar
- [ ] Missing source groups as "Other"
- [ ] 50+ items paginate or lazy load
- [ ] Timestamps in user timezone (if backend supports)

#### RTL Testing
- [ ] Panel slides from left (RTL) / right (LTR)
- [ ] Avatar position flips to right side (RTL)
- [ ] Unread dot flips to left side (RTL)
- [ ] Action buttons group aligns left (RTL)
- [ ] Close icon in top-left (RTL) / top-right (LTR)
- [ ] All text right-aligned in RTL context

#### Accessibility
- [ ] Screen reader announces "Notifications, N unread"
- [ ] Focus ring visible on all focusable elements
- [ ] Panel announces "Notifications panel opened"
- [ ] Filter change announces tab name and count
- [ ] Color contrast: text on backgrounds ≥ 4.5:1
- [ ] Motion reduced if prefers-reduced-motion

#### Performance
- [ ] Panel opens within 200ms
- [ ] Animations smooth at 60fps
- [ ] Scrolling list smooth with 100+ items (virtual scroll)
- [ ] Badge updates without flickering
- [ ] No layout shift on panel open/close

#### Mobile-Specific
- [ ] Touch targets ≥ 44px
- [ ] Swipe down on drag handle closes
- [ ] Scroll momentum enabled
- [ ] Actions accessible without pinch-zoom
- [ ] Dismiss swiped item leaves no trace

---

## 10. Universal Checklist Mapping

### Mapping Table
| Rule | Location in Component | Implementation Status | Notes |
|------|----------------------|----------------------|-------|
| **U1: Hover Classes** | `.notification-center__item.hover` | Implement | Add `.hover` for programmatic hover, :hover for CSS |
| **U2: Focus Visible** | All buttons, tabs, items | Implement | Use `outline: 2px solid var(--primary-filled-bg)` |
| **U3: Disabled Hover Suppression** | `.notification-center__trigger:disabled.hover` | Implement | Remove background on disabled hover |
| **U4: Hover Overlay (Color-Mix)** | `.notification-center__item.hover::before` | Implement | Use `color-mix(in srgb, currentColor 8%)` instead of rgba |
| **U6: Box-Shadow Borders** | Panel outline, type borders | Implement | Replace CSS borders with inset box-shadow |
| **U7: Explicit Typography** | All text elements | Implement | Declare font-family, font-size, font-weight, color on every text |
| **U8: Disabled Text Propagation** | `.notification-center__trigger:disabled *` | Implement | Inherit color: var(--content-tertiary) |
| **U9: Truncation Trio** | Title, description, timestamp | Implement | Use overflow/text-overflow/white-space on single-line; -webkit-line-clamp on multi-line |
| **U10: Logical Positioning** | Panel position, dot position | Implement | Use inset-block-start/end, inset-inline-start/end |
| **U11: Logical Border-Radius** | Panel corners, avatar border-radius | Implement | Use border-start-start-radius, etc. |
| **U12: Logical Spacing** | All padding, margin | Implement | Use padding-block, padding-inline, margin-inline-end |
| **U13: Logical Text Alignment** | Title, timestamp, empty state | Implement | Use text-align: start/end instead of left/right |
| **U14: prefers-reduced-motion** | All animations | Implement | Wrap animations in @media query, disable on match |
| **U15: No Raw Values** | Entire component | Implement | All numeric values via CSS variables/tokens |
| **U16: Dynamic Token Panel** | CSS custom properties | Implement | Define --surface-raised, --motion-normal, etc. in :root |
| **U17: State Class Pattern** | `.is-unread`, `.is-open`, `.is-new` | Implement | Use modifier classes for all state changes |
| **U19: RTL Directional Icons** | Slide animation, icon direction | Implement | Use logical transforms or [dir="rtl"] overrides |

### Dependency Validation

#### Required Base Components (Must Verify)
- **L1: Button** - Used in Mark All Read, Close icon, Actions
- **L1: Icon Button** - Trigger bell, close icon, action buttons
- **L1: Avatar** - Notification source avatar
- **L1: Badge** - Unread count badge on trigger
- **L1: Chip** - Alternative to type border (if design evolves)
- **L1: Divider** - Between notification items, after header
- **L2: Panel** - Base for panel container layout
- **L4: Tabs** - Filter tabs (All/Unread/Mentions/System)
- **L5: List** - Notification items list wrapper
- **L5: Loading** - Skeleton loader while notifications fetch

#### Dependency Checklist
- [ ] Button supports disabled state and hover suppression
- [ ] Icon Button has focus-visible and aria-label
- [ ] Avatar has fallback initials and broken image handling
- [ ] Badge supports numeric display (9+)
- [ ] Divider uses --border-subtle token
- [ ] Panel supports max-height and scrolling
- [ ] Tabs support focus management and activestate
- [ ] List supports virtualizing (50+ items)
- [ ] Loading skeleton matches item height/width
- [ ] All dependencies tested at their own layer before composition

---

## Pre-Build Signoff

### Checklist Summary
- [ ] All 10 sections completed and reviewed
- [ ] State Matrix covers all interactive states
- [ ] Variant Matrix addresses desktop/mobile + types
- [ ] Content Resilience handles edge cases
- [ ] RTL Verification includes logical properties
- [ ] Accessibility meets WCAG AA minimum
- [ ] Tokens validated against design system
- [ ] CSS follows U1-U19 rules throughout
- [ ] Motion animations tested for smoothness
- [ ] Tester Integration includes manual test plan
- [ ] All dependencies available and compatible

### Next Steps
1. **Design Review:** Verify design mockups match this spec
2. **Component Build:** Implement using CSS Architecture patterns
3. **Unit Test:** Test individual features per Tester Integration
4. **Integration Test:** Test with actual notification data
5. **Accessibility Audit:** Run axe-core and manual screen reader test
6. **Performance Test:** Verify 60fps animations, <200ms open
7. **QA Sign-Off:** Mark component ready for production

---

**Document Version:** 1.0
**Last Updated:** 2026-03-25
**Prepared For:** L6 Build Phase
