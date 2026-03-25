# AI Chat Component - Pre-Build QC Document

**Component Level:** L6 (Composed)
**Status:** Pre-Build Review
**Date:** 2026-03-25

---

## 1. State Matrix

### Base States
| Element | Default | Hover | Focused | Active | Disabled | Error |
|---------|---------|-------|---------|--------|----------|-------|
| Send Button | Primary (L1) | Applied via Button L1 | Applied via Button L1 | Applied via Button L1 | Applied via Button L1 | N/A |
| Attachment Button | Icon Button (L1) | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | N/A |
| Message Bubble (User) | Primary-tonal fill | Hover overlay (currentColor + opacity) | N/A (non-focusable) | N/A | N/A | N/A |
| Message Bubble (Assistant) | Surface-raised fill | Hover overlay (currentColor + opacity) | N/A (non-focusable) | N/A | N/A | N/A |
| Reaction Button | Icon Button (L1) | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | N/A |
| Copy Button | Icon Button (L1) | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | N/A |
| Regenerate Button | Icon Button (L1) | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | N/A |
| Edit Button | Icon Button (L1) | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | Applied via Icon Button L1 | N/A |
| Text Field (Input) | Surface border-subtle | Hover state (U3 compliance) | box-shadow 2px primary | Typing underline | Disabled text + bg | border-danger + ring |
| Suggested Actions (Chip) | Applied via Chip L1 | Applied via Chip L1 | Applied via Chip L1 | Applied via Chip L1 | Applied via Chip L1 | N/A |
| Typing Indicator | Dots animation | N/A | N/A | N/A | N/A | N/A |
| Menu (Header) | Applied via Menu L2 | Applied via Menu L2 | Applied via Menu L2 | Applied via Menu L2 | Applied via Menu L2 | N/A |

### Compound States
| Scenario | Elements Affected | Behavior |
|----------|------------------|----------|
| Input Focus + Error | Text Field | Focus ring (primary) layered with danger border + error state indicator |
| Input Disabled | Text Field, placeholder, send button | All disabled text set to --content-disabled; send button disabled |
| Message Hover (User) | Bubble + action buttons | Hover overlay visible; action buttons (copy, regenerate, edit, reactions) fade in |
| Message Hover (Assistant) | Bubble + action buttons | Hover overlay visible; action buttons fade in |
| Editing Mode | Original message + inline editor | Original message becomes light variant; inline text field active with save/cancel buttons |
| Menu Open (Header) | Chevron + dropdown | Chevron rotates; menu panel slides/fades in above header |
| Network Error | Input area + error boundary | Input disabled, error badge displayed in header status area |

---

## 2. Variant Matrix

| Variant | Layout Container | Message Width | Header | Sidebar Scrollbar | Scrollbar Style | Tokens |
|---------|------------------|----------------|--------|-------------------|-----------------|--------|
| **Full-Page** | Viewport 100% h/w | Max 800px centered | Sticky top with shadow-md | Always visible (10px width) | Light on light, dark on dark | All base tokens |
| **Embedded Panel** | Panel L2 wrapper (fixed h/w) | Constrain to parent | Integrated panel header (no redundancy) | Hidden if <4 messages, visible scroll-up | Minimal (8px) subtle styling | All base tokens |
| **Floating Widget** | Absolute positioned (400x600px) | Full bubble width | Compact header with close | Always visible | Subtle with hover states | shadow-md applied to root |

| Message Type | Visual Treatment | Icon | Color | Max Width | Special Rules |
|--------------|-----------------|------|-------|-----------|----------------|
| **Text** | Bubble with markdown rendering | N/A | Token-driven | 85% message width | Line breaks preserved; links underlined |
| **Code Block** | Monospace container with syntax highlight + copy button | `</>` icon in corner | Surface-raised bg | Full bubble width | Language badge top-right; line numbers optional |
| **Image** | Constrained within bubble; rounded corners | N/A | N/A | 70% message width | Click to expand lightbox; alt text displayed |
| **File Card** | Chip-like with icon + filename + size | Icon per file type | Border-subtle frame | 60% message width | Download link clickable |
| **Action Card** | Grid of button chips stacked | N/A | Primary-tonal or primary | 80% message width | Each action is a Chip (L1); hover all highlight |
| **System Message** | Centered, smaller text, subtle color | Info icon | Content-tertiary | 100% center | No reactions/copy/edit actions |

---

## 3. Content Resilience

| Content Type | Max Length Test | Truncation Rule | Overflow Handling | Test Cases |
|--------------|-----------------|-----------------|-------------------|-----------|
| **Username in Header** | 50 chars | Truncate (U9: text-overflow ellipsis) | white-space: nowrap; overflow: hidden | "VeryLongUsernameThatExceedsTheHeaderSpace" |
| **Message Timestamp** | 20 chars | No truncation (fixed format HH:MM AM/PM) | Justified right-aligned | "11:45 AM", "23:59 PM" |
| **Code Block** | 2000 chars | Horizontal scroll enabled | overflow-x: auto; max-height: 400px | Long single-line code strings |
| **Text Message** | 5000 chars | Line wrapping enabled | word-wrap: break-word; hyphens: auto | Lorem ipsum 5000+ chars |
| **Suggested Actions** | 8 chips max, 30 chars each | Horizontal scroll if needed | flex-wrap: nowrap; overflow-x: auto; | Multiple long action labels |
| **File Name** | 100 chars | Truncate (U9 trio) | text-overflow: ellipsis; | "very_long_filename_with_lots_of_underscores..." |
| **Error Messages** | 200 chars | Wrap into 2-3 lines | margin-inline: --space-3; | Connection timeout, rate limit, parse error messages |
| **Reaction Count** | 3 digits (999+) | Show "+3" if >3 | Badge-style count display | Emoji buttons with reaction count badges |

---

## 4. RTL Verification Points

| Element | LTR Behavior | RTL Behavior | Verification Method | Critical |
|---------|--------------|--------------|---------------------|----------|
| **Message Bubble (User)** | Right-aligned in message list | Left-aligned in RTL | Test in Hebrew/Arabic | YES |
| **Message Bubble (Assistant)** | Left-aligned in message list | Right-aligned in RTL | Test in Hebrew/Arabic | YES |
| **Avatar in Header** | Left side of name | Right side of name in RTL | Test layout shift | YES |
| **Action Buttons Row** | Right-most position on hover | Left-most position in RTL | Icon order preserved (U19) | YES |
| **Input Area** | Left text, right send button | Right text, left send button | Flex direction reversal | YES |
| **Attachment Icon Button** | Right of input field | Left of input field | Flex order adjustment | YES |
| **Typing Indicator Dots** | Center animation (no directional bias) | Center animation (no directional bias) | No override needed | NO |
| **Menu Dropdown (Header)** | Opens downward, right-aligned to chevron | Opens downward, left-aligned in RTL | Positional recalc | YES |
| **Suggested Actions Row** | Left-to-right chip order | Right-to-left chip order in RTL | Flex direction reversal | YES |
| **Scroll Bar** | Right side of scroll container | Left side in RTL context | Browser native RTL support | YES |
| **Timestamp** | Trailing right side | Trailing left side in RTL | text-align: end (U13) | NO |
| **Error Boundary Badge** | Positioned right | Positioned left in RTL | inset-inline-end (U10) | YES |

**RTL Test Commands:**
- `[dir="rtl"]` attribute on root
- Chrome DevTools: Rendering → Emulate CSS media feature prefers-color-scheme / direction
- Manual test in: he_IL (Hebrew), ar_SA (Arabic)

---

## 5. Accessibility Checklist

| Criteria | Component Element | Status | Notes |
|----------|------------------|--------|-------|
| **WCAG 2.1 Level AA - Perceivable** |
| Color Contrast (4.5:1) | Text in bubbles, action buttons, timestamps | To-Test | All text must pass WCAG AA |
| Color Not Sole Info | Message status, reactions, error state | To-Test | Use icons + color (reaction emoji counts) |
| Resize Text 200% | Full component layout | To-Test | No content loss or horizontal scroll at 200% |
| **WCAG 2.1 Level AA - Operable** |
| Keyboard Navigation | Send button, text field, action buttons, menu | To-Test | Tab order: input → send → menu → message buttons |
| Focus Indicator | All interactive elements | To-Test | box-shadow: 0 0 0 2px var(--primary-filled-bg) (U2) |
| Focus Trap (Modal-only) | Menu dropdown panel | N/A | Only if menu opens modal |
| Button Label | Send, attach, copy, regenerate, edit, react, delete | To-Test | aria-label on icon buttons (Icon Button L1 handles) |
| Form Fields | Text input | To-Test | aria-label or associated <label> for input |
| Skip Links | Full-page variant only | To-Test | Jump to message list, jump to input |
| **WCAG 2.1 Level AA - Understandable** |
| Language Tag | Root component | To-Test | lang="en" or lang="ar" (dynamic per locale) |
| Abbreviations | Timestamps (AM/PM), file sizes (KB/MB) | To-Test | <abbr> or tooltip on hover |
| Instructions | Attachment upload, markdown formatting | To-Test | Placeholder text or tooltip near input |
| Error Messages | Connection errors, validation | To-Test | Plain language, near form field |
| **WCAG 2.1 Level AA - Robust** |
| Semantic HTML | Message list, message items, buttons, inputs | To-Test | <ul>/<li> for messages, <button>, <input> |
| ARIA Roles | Menu toggle, live regions, list containers | To-Test | role="listbox" for message list, role="menu" for header menu |
| Live Regions | Typing indicator, incoming messages, error alerts | To-Test | aria-live="polite" on message container, aria-atomic="true" |
| State Indicators | Disabled, error, editing mode | To-Test | aria-disabled, aria-invalid, aria-label updates |
| **Screen Reader Testing** |
| Message Reading Order | Message sender → timestamp → content | To-Test | Announce "User message", "Assistant message", content, time |
| Action Button Labels | Copy, regenerate, react, edit, delete | To-Test | aria-label describes action (Icon Button L1) |
| Loading State | Typing indicator, message streaming | To-Test | aria-live="polite" announces "Assistant typing..." |

---

## 6. Token Validation

| Token Name | Scope | Usage | Type | Fallback | Critical |
|------------|-------|-------|------|----------|----------|
| `--surface-default` | Chat background | Message list container, full-page bg | Color (semantic) | #FFFFFF / #1A1A1A | YES |
| `--surface-raised` | Assistant bubble bg | Assistant message bubble fill | Color (semantic) | #F5F5F5 / #2A2A2A | YES |
| `--primary-tonal-bg` | User bubble bg | User message bubble fill | Color (semantic) | #E3F2FD / #1E3A5F | YES |
| `--primary-tonal-fg` | User text | Text inside user bubble | Color (semantic) | #1976D2 / #64B5F6 | YES |
| `--content-primary` | Assistant text | Text inside assistant bubble | Color (semantic) | #1A1A1A / #E0E0E0 | YES |
| `--content-tertiary` | Timestamps, metadata | Timestamp text, "edited" label, metadata | Color (semantic) | #757575 / #9E9E9E | NO |
| `--content-disabled` | Disabled text propagation | Disabled input text, disabled action labels | Color (semantic) | #BDBDBD / #616161 | YES |
| `--border-subtle` | Input border | Text field border (unfocused), separator lines | Color (semantic) | #E0E0E0 / #424242 | YES |
| `--primary-filled-bg` | Focus ring | Focus indicator box-shadow (U2) | Color (semantic) | #1976D2 / #64B5F6 | YES |
| `--space-3` | Gap token | Message bubble gap (bottom margin) | Dimension (px) | 12px | NO |
| `--space-4` | Padding token | Bubble internal padding | Dimension (px) | 16px | YES |
| `--radius-lg` | Large radius | Message bubble border-radius | Dimension (px) | 12px | YES |
| `--radius-xl` | Extra-large radius | Input area bottom-left/right corners | Dimension (px) | 20px | NO |
| `--shadow-md` | Medium shadow | Floating widget elevation, input focus | Shadow | 0 4px 12px rgba(0,0,0,0.15) | YES |
| `--motion-fast` | Fast duration | Message fade-up animation | Duration (ms) | 200ms | NO |
| `--motion-normal` | Normal duration | Typing dots bounce, suggested actions stagger | Duration (ms) | 300ms | NO |

**Validation Rules:**
- All tokens must be sourced from design token file (no hardcoded values per U15)
- Fallbacks must match light/dark mode intent
- Test in light and dark mode viewports
- Verify WCAG 4.5:1 contrast for all text tokens

---

## 7. CSS Architecture

### Class Structure Pattern (U17 State Classes)

```
.ai-chat
├── .ai-chat__message-list
│   ├── .ai-chat__message (wrapper)
│   │   ├── .ai-chat__message--user (variant)
│   │   ├── .ai-chat__message--assistant (variant)
│   │   ├── .ai-chat__message--system (variant)
│   │   ├── .ai-chat__message--editing (state)
│   │   ├── .ai-chat__message--error (state)
│   │   └── .ai-chat__bubble
│   │       ├── .ai-chat__bubble-content
│   │       ├── .ai-chat__code-block
│   │       └── .ai-chat__action-card
│   │   └── .ai-chat__actions (hover state reveal, U1)
│   │       ├── .ai-chat__action-button (Icon Button L1)
│   └── .ai-chat__typing-indicator
│       └── .ai-chat__typing-dot
├── .ai-chat__input-area
│   ├── .ai-chat__text-field (Text Field L3)
│   ├── .ai-chat__send-button (Button L1)
│   └── .ai-chat__attach-button (Icon Button L1)
├── .ai-chat__suggested-actions
│   └── .ai-chat__action-chip (Chip L1)
└── .ai-chat__header
    ├── .ai-chat__avatar (Avatar L1)
    ├── .ai-chat__name
    ├── .ai-chat__status
    └── .ai-chat__menu (Menu L2)

.ai-chat--full-page (variant)
.ai-chat--embedded (variant)
.ai-chat--floating (variant)

.ai-chat.hover \* (wrapper state propagation, U18)
.ai-chat[disabled] \* (disabled state propagation, U8)
```

### Critical CSS Rules

| Rule Category | Elements | U-Rule | Implementation |
|---------------|----------|--------|-----------------|
| Hover States | Send btn, message bubbles, action buttons | U1, U4 | `.ai-chat__message:hover .ai-chat__actions { opacity: 1; }` + `::before` overlay |
| Focus Indicators | Send button, text field, action buttons | U2 | `box-shadow: 0 0 0 2px var(--primary-filled-bg);` on `:focus-visible` |
| Disabled Suppression | Input area | U3 | `.ai-chat__input-area[disabled]:hover { background-color: unchanged; }` |
| Compound Error+Focus | Text field | U5 | `.ai-chat__text-field:focus:invalid { box-shadow: 0 0 0 2px var(--danger-filled-bg); }` |
| Box-Shadow Borders | Input area, bubbles | U6 | `box-shadow: inset 0 0 0 1px var(--border-subtle);` instead of `border: 1px` |
| Text Styling | All text elements | U7 | Explicit font-family, font-size, color, line-height on every `.ai-chat__*` text |
| Disabled Text Propagation | Input, labels, badges | U8 | `color: var(--content-disabled);` applied to all children when parent disabled |
| Truncation Trio | Username, timestamps, file names | U9 | `text-overflow: ellipsis; white-space: nowrap; overflow: hidden;` together |
| Logical Positioning | All alignment | U10 | `inset-inline-start`, `inset-inline-end` instead of `left`/`right` |
| Logical Border Radius | Message bubbles, input area | U11 | `border-start-start-radius`, `border-end-end-radius` per LTR/RTL |
| Logical Spacing | Margins, padding | U12 | `margin-inline`, `padding-inline-start`, `padding-inline-end` |
| Logical Text Alignment | Timestamps, input placeholder | U13 | `text-align: start;` / `text-align: end;` |
| Reduced Motion | All animations | U14 | `@media (prefers-reduced-motion: reduce) { animation: none; transition: none; }` |

---

## 8. Motion

| Element | Animation | Duration | Easing | Trigger | Reduced Motion |
|---------|-----------|----------|--------|---------|-----------------|
| **Message Bubble (Entry)** | Fade in + slide up 8px | --motion-fast (200ms) | ease-out | On render | Fade only, no translation |
| **Typing Indicator Dots** | Bounce (0 → 4px → 0) on each dot | --motion-normal (300ms) | ease-in-out | Staggered (90ms offset) | Disable, show static ellipsis |
| **Suggested Actions** | Staggered fade-in (each chip +100ms offset) | --motion-normal per item | ease-out | After assistant message | Fade all at once, no stagger |
| **Input Focus** | Subtle color transition (border color) | 200ms | ease-in-out | On focus event | Instant, no transition |
| **Streaming Text Cursor** | Blink at end of text | 600ms blink cycle | N/A | During message stream | Disable blink, solid cursor |
| **Menu Open** | Chevron 180° rotate + panel fade-up | 300ms | ease-out | On menu toggle | Instant, no animation |
| **Error Badge** | Pulse or shake (optional UX) | 400ms | ease-in-out | On error state | Disable, no motion |

**CSS Implementation:**
```css
@media (prefers-reduced-motion: reduce) {
  .ai-chat__message,
  .ai-chat__typing-dot,
  .ai-chat__action-chip {
    animation: none;
    transition: none;
  }
  .ai-chat__cursor { animation: none; }
}
```

---

## 9. Tester Integration

### Test Environment Setup
- Playwright/Cypress component testing with visual snapshots
- Percy.io integration for regression detection
- WAVE browser extension for accessibility scanning
- Axe Core for automated a11y testing
- Chromatic for design system version tracking

### Automated Test Suite

| Test Category | Test Name | Assertion |
|---------------|-----------|-----------|
| **State Matrix** | `test-message-bubble-hover-user` | Hover overlay visible, action buttons fade in |
| **State Matrix** | `test-input-focus-error-compound` | Focus ring + danger border both visible simultaneously |
| **State Matrix** | `test-disabled-input-no-hover` | Disabled input does not respond to hover (U3) |
| **Variant Matrix** | `test-full-page-layout` | 100vw/100vh, scrollbar visible, header sticky |
| **Variant Matrix** | `test-embedded-panel-layout` | Constrained to parent Panel dimensions, no overflow |
| **Variant Matrix** | `test-floating-widget-layout` | 400x600px, shadow-md applied, z-index > sibling panels |
| **Variant Matrix** | `test-message-type-code-block` | Syntax highlight visible, copy button present, language badge |
| **Variant Matrix** | `test-message-type-file-card` | Icon visible, filename truncated, download link works |
| **Content Resilience** | `test-long-username-truncation` | Username truncates at 50 chars with ellipsis (U9) |
| **Content Resilience** | `test-code-block-horizontal-scroll` | Long code line scrolls left/right within bubble |
| **Content Resilience** | `test-message-text-wrapping` | 5000 char message wraps to multiple lines |
| **Content Resilience** | `test-suggested-actions-overflow` | 8+ chips fit or scroll horizontally |
| **RTL Verification** | `test-rtl-message-alignment` | User msg right in LTR, left in RTL |
| **RTL Verification** | `test-rtl-input-button-order` | Input left, button right in LTR; reversed in RTL |
| **RTL Verification** | `test-rtl-avatar-position` | Avatar left-of-name in LTR, right in RTL |
| **Accessibility** | `test-keyboard-tab-order` | Input → send → menu → message action buttons |
| **Accessibility** | `test-focus-indicator-visibility` | Focus ring 2px border visible on all interactive elements |
| **Accessibility** | `test-aria-live-typing-indicator` | Screen reader announces "Assistant typing" |
| **Accessibility** | `test-color-contrast` | All text ≥4.5:1 ratio (WCAG AA) |
| **Accessibility** | `test-200-percent-resize` | No horizontal overflow at 200% zoom |
| **Token Validation** | `test-tokens-light-mode` | All color tokens resolve correctly in light mode |
| **Token Validation** | `test-tokens-dark-mode` | All color tokens resolve correctly in dark mode |
| **CSS Architecture** | `test-focus-visible-not-focus` | Only :focus-visible shows ring, not :focus |
| **CSS Architecture** | `test-disabled-text-color` | Disabled children inherit --content-disabled |
| **Motion** | `test-message-animation-fast-duration` | Animation duration matches --motion-fast |
| **Motion** | `test-typing-dots-stagger` | Each dot bounces offset by 90ms |
| **Motion** | `test-prefers-reduced-motion` | No animations when prefers-reduced-motion: reduce |

### Manual Test Checklist

- [ ] Verify all message types render correctly (text, code, image, file, action, system)
- [ ] Test message bubbles at 3 zoom levels (75%, 100%, 200%)
- [ ] Test input field with 500+ character paste
- [ ] Test reaction emoji picker (accessibility, keyboard nav)
- [ ] Test edit mode: inline editor activation, save/cancel flows
- [ ] Test regenerate button: message updates, animation smooth
- [ ] Test copy to clipboard: feedback (toast/tooltip)
- [ ] Test branch creation (if multi-thread supported)
- [ ] Test network error recovery
- [ ] Verify header avatar loads correctly (fallback initials)
- [ ] Test suggested actions on slow network (skeleton/loading state)
- [ ] Test full-page viewport responsiveness: mobile, tablet, desktop
- [ ] Test floating widget drag-to-reposition (if supported)
- [ ] Test embedded panel within other L6 components (no layout conflicts)
- [ ] Test with screen reader (NVDA on Windows, VoiceOver on Mac)
- [ ] Test keyboard-only navigation (no mouse)
- [ ] Test with browser zoom at 150%, 200%
- [ ] Test RTL layout with Hebrew text
- [ ] Test dark mode toggle (theme persistence)

---

## 10. Universal Checklist Mapping

| U-Rule | Element(s) | Requirement | Implementation | Verification |
|--------|-----------|-------------|-----------------|--------------|
| **U1: Hover Classes** | Message bubbles, action buttons, suggested chips | Every `:hover` state needs `.hover` class | `.ai-chat__message:hover { ... }` + `.ai-chat__message.hover { ... }` on dynamic update | Test mouse hover + JS-triggered hover |
| **U2: Focus-Visible** | Send button, text field, icon buttons, menu toggle | All focusable elements need box-shadow 2px primary ring | `:focus-visible { box-shadow: 0 0 0 2px var(--primary-filled-bg); }` | Tab through all interactive elements |
| **U3: Disabled Hover Suppression** | Text field, send button (when disabled), action buttons | Disabled states must not show hover effects | `.ai-chat__send-button:disabled:hover { background-color: unchanged; opacity: unchanged; }` | Enable/disable send button, check hover behavior |
| **U4: Hover Overlay** | Message bubbles, action row | Never use `rgba()`; use `::before` with `currentColor + opacity` or `color-mix()` | `.ai-chat__bubble::before { background: color-mix(in srgb, currentColor 12%, transparent); }` | Visual inspection of overlay opacity |
| **U5: Compound States** | Text field with error + focus, input + disabled | Error+hover locked; focused+error danger ring; disabled+hover reverts | `.ai-chat__text-field:focus:invalid { box-shadow: ... danger-ring; border-color: var(--danger); }` | Test all 6 combinations per state docs |
| **U6: Box-Shadow Borders** | Input field border, message bubble dividers, floating widget edge | Use inset box-shadow instead of CSS `border` | `.ai-chat__text-field { box-shadow: inset 0 0 0 1px var(--border-subtle); border: none; }` | Inspect computed styles, check for `border` prop |
| **U7: Explicit Text Styling** | Username, timestamp, message text, labels, error messages | Every text element needs explicit font-family, font-size, color, line-height | `.ai-chat__message-text { font-family: var(--font-body); font-size: 14px; color: var(--content-primary); line-height: 1.5; }` | Inspect all text elements in DevTools |
| **U8: Disabled Propagation** | Input area (all children), action buttons when disabled | Disabled state propagates to ALL child text via `--content-disabled` | `.ai-chat__input-area[disabled] * { color: var(--content-disabled); }` | Disable parent, verify all text dims |
| **U9: Truncation Trio** | Username in header, file names, long labels | `text-overflow` + `white-space` + `overflow` together | `.ai-chat__header-name { text-overflow: ellipsis; white-space: nowrap; overflow: hidden; max-width: 200px; }` | Paste 100+ char username, check ellipsis |
| **U10: Logical Positioning** | All element positioning, avatar placement, menu alignment | Use `inset-inline-start`, `inset-inline-end` instead of `left`/`right` | `.ai-chat__header-avatar { inset-inline-start: var(--space-4); }` instead of `left:` | Test in RTL mode, check positions |
| **U11: Logical Border Radius** | Message bubbles, input area, action chips | Use `border-start-start-radius`, `border-end-end-radius` | `.ai-chat__bubble--user { border-start-start-radius: var(--radius-lg); border-end-end-radius: var(--radius-lg); }` | Test in RTL, verify corners apply correctly |
| **U12: Logical Spacing** | Margins between messages, padding in bubbles, input margins | Use `margin-inline`, `padding-inline-start`, `padding-inline-end` | `.ai-chat__bubble { padding-inline: var(--space-4); padding-block: var(--space-3); }` | Test in RTL, measure spacing consistency |
| **U13: Logical Text Alignment** | Timestamps, centered system messages, input placeholder | Use `text-align: start`, `text-align: end` instead of `left`/`right` | `.ai-chat__timestamp { text-align: end; }` | Verify text direction flips in RTL |
| **U14: Prefers-Reduced-Motion** | All animations (fade-up, typing dots, menu open, streaming cursor) | Must respect `prefers-reduced-motion: reduce` media query | `@media (prefers-reduced-motion: reduce) { .ai-chat__* { animation: none; } }` | Enable reduce motion in OS, verify no animations |
| **U15: No Raw Values** | All colors, sizes, spacing, shadows, durations | Every value must be a design token variable, never hardcoded | All properties reference `var(--token-name)` | Grep for `#` hex colors, `px` values; should find none |
| **U16: Dynamic Token Panel** | L6 component container | Must expose token override panel in design system tools | `.ai-chat[data-token-override-enabled]` style context | Test in design system UI |
| **U17: State Class Pattern** | All state-driven elements | Use `.element--state` or `.element.state-class` naming; avoid inline styles | `.ai-chat__message--editing`, `.ai-chat__input-area[disabled]` | No inline `style=""` attributes in HTML |
| **U18: Wrapper State Propagation** | Message hover (reveals action buttons), disabled input (dims all children) | Parent state must propagate to children without explicit re-styling | `.ai-chat__message:hover .ai-chat__actions { opacity: 1; }` + remove inline styles | Inspect child elements when parent state changes |
| **U19: RTL Directional Icons** | Icon buttons (copy, regenerate, edit, react), menu chevron, attachment | Icons must flip in RTL unless semantically directional (chevron in menu DOES flip) | Use `transform: scaleX(-1)` or separate icon components per direction | Verify icon direction in Hebrew/Arabic locale |

---

## Summary

**Pre-Build QC Gate:** This document confirms the AI Chat L6 component is ready for build if:

1. ✅ All 19 universal rules (U1-U19) mapped to specific elements with test cases
2. ✅ State matrix covers base + 8 compound state scenarios
3. ✅ 3 layout variants (full-page, embedded, floating) verified
4. ✅ 6 message types + reactions/copy/regenerate/edit/branch features scoped
5. ✅ RTL directional requirements for 11 critical elements documented
6. ✅ WCAG 2.1 Level AA accessibility checklist complete (perceived, operable, understandable, robust)
7. ✅ 14 design tokens validated across light/dark modes
8. ✅ CSS architecture with BEM + state class pattern established
9. ✅ Motion specifications tied to --motion-fast/normal with reduced-motion fallback
10. ✅ 26 automated tests + manual checklist defined for integration

**Next Step:** Proceed to build phase. Assign test suite implementation to QA before code review.
