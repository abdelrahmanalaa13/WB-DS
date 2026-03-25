# DS Learning Loop Protocol

The design system is a Quiet Intelligence. Every component decision feeds back into the whole system. This document defines how learning propagates.

## The Loop

After finalizing any component, pattern, or architectural decision, run this reflection cycle before moving to the next task:

### Step 1: Extract Learnings

What new patterns, rules, or constraints emerged from this component?

Examples from the Card component:
- Containers use density (compact/default/comfortable), not size (sm/md/lg)
- Width/height are the parent layout's responsibility, never the component's
- ds-surface is the shared visual base for all bounded containers
- Status containers reuse the same role-emphasis token pattern as atoms
- Content types prove slot flexibility, not hardcoded demos

### Step 2: Blast Radius Check

Which existing components are affected by this learning?

For each learning, ask:
- Does this contradict how any existing component works?
- Does this introduce a shared pattern that existing components should adopt?
- Does this change how future components should be built?

Map the impact:

| Learning | Affects | Action needed |
|----------|---------|---------------|
| ds-surface base | All future containers (Panel, Dialog, Alert...) | Extend ds-surface from day one |
| density model | Tabs, Breadcrumb (container-adjacent nav) | Evaluate: should they respond to parent density context? |
| no width/height on containers | Card render demos | Audit: are inline widths on component or wrapper? |
| ghost emphasis naming | DS_RULES.md, LOVABLE_PROMPT.md | Still references "text" emphasis |

### Step 3: Propagate

Apply the learnings to affected components. This means:
- Update CSS if a shared pattern replaces duplicated code
- Update JS configs if controls/defaults need alignment
- Update architecture docs with new rules
- Update reference docs (DS_RULES.md, LOVABLE_PROMPT.md) if terminology changed

### Step 4: Validate

After propagation, verify nothing broke:
- All affected components still render correctly
- No CSS specificity conflicts from new shared classes
- Token references resolve in all brand/theme combos
- Architecture docs are internally consistent

### Step 5: Record

Log what was learned and what changed. This builds the knowledge graph relationships:

```
component:card --learned--> pattern:density-model
pattern:density-model --applies-to--> component:panel (future)
pattern:density-model --applies-to--> component:dialog (future)
pattern:density-model --evaluated-for--> component:tabs (decided: no, fixed-height)
pattern:density-model --evaluated-for--> component:breadcrumb (decided: no, fixed-height)
```

## Decision Ledger

Every architectural decision is recorded here with rationale.

### Wave 1 Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Inset box-shadow for outlined borders | CSS border adds height, box-shadow doesn't | All components with outlined emphasis |
| Radius scales with component size | Prevents pill-shaped small components | All atoms (xs=radius-xs through xl=radius-xl) |
| State classes (.hover) alongside pseudo-classes | Tester needs forced states without actual hover | All interactive components |
| Ghost emphasis (renamed from text) | "Text" doesn't apply to icon-only components | All emphasis references in tokens, docs, components |
| ::before state layer with currentColor + opacity | Single pattern for hover/focus/pressed across all roles | All interactive components |
| Focus ring: box-shadow not outline | Combines with inset border on outlined components | All focusable components |

### Wave 2 Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| ds-surface base class | Prevents surface CSS duplication across containers | All containers (current and future) |
| Density replaces size for containers | Containers are content-driven, not fixed-height | Card, and all future containers |
| No width/height on containers | Dimensions are parent layout responsibility | All containers |
| Status surfaces reuse role-emphasis tokens | No new token architecture needed for alerts/notifications | All Wave 3 status containers |
| Panel = Sheet on mobile | Same component, viewport-driven behavior switch | Wave 3 panel implementation |
| Drag/drop is a composable trait | Not baked into any container | Wave 5 kanban, sortable lists |
| Flow nodes are NOT cards | Different interaction model entirely | Wave 5 flow builder |
| neutral role doubles as info | Avoids 7th color role, 16 extra tokens per brand/theme | All status containers |

## Pending Propagation

Items that have been identified but not yet applied:

- [x] DS_RULES.md: "text" emphasis updated to "ghost" (line 32)
- [x] Chip: added .hover::before, .active::before, .disabled class selectors
- [x] Toggle/Switch thumb: replaced hardcoded `white` with `var(--surface-default)`
- [x] ~~LOVABLE_PROMPT.md: needs v4 token names~~ DROPPED -- Lovable may not be used going forward
- [ ] Tabs: evaluate if density context from parent should affect tab padding/font
- [ ] Breadcrumb: same density evaluation
- [ ] Nav Rail: intentionally standalone (specialized collapse/expand behavior), not a ds-surface candidate
- [ ] Bottom Nav: intentionally standalone (fixed bar with border-top), not a ds-surface candidate
- [x] Sprint A: Panel, Dialog, Alert, Snackbar, Toast built with ds-surface patterns and density from day one

### Sprint A Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Alert uses own `.ds-alert` class, not ds-surface | Alert is inline with distinct anatomy (icon + body + dismiss). ds-surface is for bounded elevated/outlined containers. Alert gets its own role-emphasis CSS. | Clarifies when to use ds-surface vs standalone class |
| Snackbar is always filled emphasis | Snackbar needs maximum contrast since it floats over content. Tonal/outlined would be too subtle. | Snackbar spec confirmed, no tonal/outlined variants |
| Toast accent strip is 4px leading edge only | Toast surface stays neutral (elevated). Only the accent strip carries role color. Distinguishes from Snackbar (entire bg is role-colored). | Toast vs Snackbar distinction is visual hierarchy |
| Dialog uses demo-relative backdrop | Real backdrop would be position:fixed and overlay the whole library. Demo uses relative positioning inside preview container. | All future overlay components in tester need demo mode |
| Panel density scales header/content/footer padding + font sizes | Mirrors Card density pattern exactly. Consistent density language across all containers. | Validates density as universal container pattern |
| Spec token names vs actual token names | Specs referenced `--ease-decelerate`/`--ease-accelerate` but real tokens are `--ease-enter`/`--ease-exit`. Always verify against globals-v4.css. | Updated spec mental model, code uses real tokens |

### Sprint B Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Tooltip uses inverted surface (`--neutral-filled-bg/fg`) | Must contrast against any background it floats over. Inverted = dark on light, light on dark. | Tooltip is the only component that uses neutral-filled for its bg |
| Popover reuses `popoverEnter` animation from Menu | Same enter pattern (fade + translateY). One keyframe definition shared. | Establishes shared overlay animation pattern |
| Menu item states: hover via `--neutral-tonal-bg`, selected via `--primary-tonal-bg` | Hover is neutral (non-committal), selected is primary (committed choice). Danger items override both. | Standard item state pattern for any future list-like components (command palette, notification center) |
| Text Field uses `inset box-shadow` for border, not CSS border | Consistent with outlined Card and all outlined containers. Also allows 2px focus ring without layout shift. | Validates inset box-shadow as universal border pattern |
| Text Field filled variant uses bottom-border only | Visual distinction from outlined. Bottom border acts as focus indicator position. Matches material-style filled fields. | Filled emphasis in inputs is structurally different from filled in atoms |
| Selection Controls reuse Toggle atom pattern for Switch | `ds-form-switch` mirrors the L1 Toggle but lives in form context with label. Same tokens, same motion. | Confirms Toggle and form Switch are visually identical, contextually different |
| Textarea is a separate sub-component of Text Field | Different layout (no fixed height, vertical resize). Shares label/helper/error pattern but not the horizontal input row. | Text Field tester shows textarea as a `type` option |
| Every text element needs explicit CSS class | Unstyled spans inherit browser/page defaults, not DS tokens. Counter, helper, error, label all need font-family + font-size + color. | All future components: never rely on inheritance for typography |
| Disabled state needs label + helper + counter color propagation | Opacity alone is insufficient; text tokens must switch to `--content-disabled`. Use wrapper state class (`.label-disabled`) to cascade. | All input components with labels/helpers |
| Filled-style compound states must mirror outlined compound states | Every outlined state combo (error+hover, focused+error, disabled+hover) must have a filled-style equivalent. | Textarea, and all future filled-style inputs |
| Token panel must reflect all active tokens per state | Showing only base tokens hides state-specific tokens the developer needs. Panel must add/remove tokens dynamically based on current state control. | All component token panels |

### Breadcrumb Overflow Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Breadcrumbs must never wrap (`flex-wrap: nowrap`) | Wrapping breaks the horizontal reading flow and looks broken. Industry consensus from Carbon, Spectrum, SAP Fiori. | Breadcrumb CSS base rule |
| Overflow collapse at 4+ visible items | Show first (root) + last 2 (parent + current), collapse middle into ellipsis popover. Matches SAP Fiori enterprise pattern. | Breadcrumb render logic |
| Individual labels truncated at 160px with ellipsis | ERP entity names can be long. `text-overflow: ellipsis` + `title` attr for hover tooltip. | All breadcrumb items |
| Semantic HTML: `nav > ol > li` with ARIA | W3C APG pattern requires `aria-label="Breadcrumb"`, `aria-current="page"` on last item, `aria-label` on ellipsis button. | Breadcrumb accessibility |
| Overflow popover uses `--surface-overlay` + `--border-subtle` | Same surface pattern as Menu/Popover. Keeps overlay components visually consistent. | Establishes overflow popover as a shared pattern |
| Separator chevron must mirror in RTL | Chevron points right in LTR, left in RTL. CSS `transform: scaleX(-1)` or logical property. | All directional icons in nav components |

### Full Library Audit Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Token panel must map emphasis name to token name correctly | CSS uses `outline` (not `outlined`) and `ghost` has no bg token. Token function must branch per emphasis, not blindly template. | Button, Icon Button token panels |
| Selected state must respect color role for all emphases | Chip outlined.selected was hardcoded to primary. Every selected variant (tonal, outlined) needs per-role CSS rules. | Chip, and any future selectable components |
| Tester controls must affect the render output | Toggle render ignored the state control entirely, showing both states always. Every control must visibly change the preview. | All component testers |
| Snackbar hover must not use raw rgba | `rgba(255,255,255,0.15)` breaks on inverted surfaces in dark mode. Use `currentColor` with `::before` opacity overlay, same pattern as Button. | Any component rendered on filled/inverted surfaces |
| Selection controls need hover and focus states | Bare checkbox/radio/switch had no hover feedback or focus rings. Hover should strengthen the border ring. | Checkbox, Radio, Switch |
| Tooltip positioning must use logical properties | Physical `left`/`right` CSS breaks RTL. Use `inset-inline-start`/`inset-inline-end` for start/end positions. Arrow positioning too. | Tooltip, and any absolutely-positioned component |
| All text elements need explicit font-family | Toggle label, Divider label, Tab badge, Bottom nav item all relied on inheritance. Add `var(--font-sans)` to every text class. | System-wide rule |
| Raw px values must be replaced with tokens | Bottom nav used `10px`, `8px`, `2px`, `3px` instead of `--font-size-xs`, `--space-0-5`, etc. | Bottom nav, and any new component |
| Nav rail text needs white-space: nowrap for ellipsis | `text-overflow: ellipsis` requires `white-space: nowrap` to work. Without it, text wraps instead of truncating. | Any truncated text pattern |

### Sprint C Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Slider thumb hover uses `color-mix()` for focus ring | Creates semi-transparent primary halo. Works with any primary color, no hardcoded rgba. | Slider, any component needing transparent tint of a token |
| Date Picker reuses Text Field as trigger | Calendar opens below a read-only text field. Reuses `.ds-text-field` CSS entirely. | Validates component composition model |
| Date range uses three distinct day classes | `range-start` (filled, rounded left), `in-range` (tonal, no radius), `range-end` (filled, rounded right). | Date Picker range type |
| Tag Input reuses Chip token pattern for tags | Tags use `--primary-tonal-bg/fg` same as Chip tonal. Consistent tag visuals. | Tag Input, any future multi-value input |
| File Upload states map to distinct visuals | idle=dashed, drag-over=primary+tonal bg, uploading=progress bar, error=danger border+per-file error. | File Upload state machine |
| Pagination uses smart ellipsis algorithm | Always shows first+last page, current+neighbors. Same first+last pattern as Breadcrumb. | Pagination numbered type |
| Stepper connecting line uses logical properties | `inset-inline-start/end` for horizontal lines flips correctly in RTL. | Stepper, any component with connecting lines |

### Sprint C Audit Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| Slider tooltip uses `inset-inline-start` not `left` | Physical `left: 50%` breaks RTL. Tooltip must center via logical property. | Slider, any absolutely-positioned centered element |
| Disabled containers must propagate to label/value text | Slider track had `opacity: 0.5` but label/value stayed default color. Wrapper `.disabled` class cascades `--content-disabled` to child text. | Slider, and any future component with label+value+track pattern |
| Date Picker range uses logical border-radius | Physical `border-radius: radius 0 0 radius` breaks RTL. Use `border-start-start-radius` etc. | Date Picker, any component with directional rounding |
| All interactive elements need `:focus-visible` | Menu items, toast dismiss, snackbar action, breadcrumb items, file upload remove, datepicker nav/day buttons all lacked keyboard focus ring. | System-wide rule for every clickable element |
| Tag Input needs compound states (error+hover, focused+error) | Same pattern as Text Field. Error border must not change on hover. Focus+error shows 2px danger ring. | Tag Input, any future input with error state |
| Tag Input label/helper must propagate disabled/error color | Wrapper state class (`label-disabled`, `label-error`) cascades to label and helper text. Same pattern as Text Field. | Tag Input, any input component with labels |
| File Upload error+hover must lock border | Error zone border should not change to primary on hover. Same locked-border pattern as Text Field error. | File Upload, any container with error+hover conflict |
| Badge overlay uses `inset-inline-end` not `right` | Badge position flips in RTL. All absolute-positioned decorators must use logical properties. | Badge, any positioned overlay element |
| Toggle thumb transition cleaned to logical only | Had both `left` and `inset-inline-start` in transition. Removed physical property. RTL override block was dead code. | Toggle/Switch |
| Breadcrumb overflow popover uses `inset-inline-start` not `left` | Absolute-positioned dropdown must flip in RTL. | Breadcrumb, any popover/dropdown |
| Dashed border is acceptable exception to inset box-shadow rule | File Upload uses `border: 2px dashed` because `box-shadow` cannot produce dashed borders. | File Upload only |

### Sprint D Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| `.hover` class must be added alongside every `:hover` pseudo-class at build time | This rule was established in Wave 1 but still missed during Sprint D for Accordion, Data Grid, Tree View. Must be treated as a build-time checklist item, not just an audit catch. | Every interactive element in every sprint |
| Disabled propagation must cover ALL child text elements | List trailing text, Data Grid cell text, Tree View label/icon/badge/expand all needed explicit `--content-disabled`. Opacity alone is not sufficient. Check every child class. | All components with disabled state |
| Non-input interactive elements still need `:focus-visible` | Timeline items have `tabindex="0"` but no focus ring was added. Any element that receives keyboard focus needs `box-shadow: 0 0 0 2px var(--primary-filled-bg)`. | Timeline, any focusable non-input element |
| `prefers-reduced-motion` is a required gate for animated components | Loading component defines spinner, shimmer, pulse, bounce animations. All must degrade gracefully under reduced motion (static or opacity-only). | Loading, any future animated component |
| Data Grid numeric cells use `text-align: end` + `font-variant-numeric: tabular-nums` | Numbers align to decimal point naturally with end-alignment. Tabular nums keep columns aligned regardless of digit values. | Data Grid, any future numeric display |
| Tree View indent guides use `::before` on children container | Vertical guide line positioned via `inset-inline-start` relative to nesting depth. RTL-safe. | Tree View, any nested/hierarchical component |
| Accordion chevron rotation is RTL-aware | Chevron starts at 0deg (right-pointing in LTR) and rotates to 90deg when expanded. RTL uses `[dir="rtl"]` to start at 180deg. | Accordion, any expandable component with directional icon |
| Timeline connecting line uses `inset-inline-start: 50%` + `translateX(-50%)` | Centers the line on the node column regardless of text direction. Same centering pattern as Slider tooltip. | Timeline, any vertically-connected layout |
| Data Grid wrapper uses `overflow-x: auto` with rounded `box-shadow` border | Table itself has `border-collapse: collapse`. Outer wrapper provides rounded corners and border via inset box-shadow. Overflow scroll is on wrapper, not table. | Data Grid, any scrollable bounded container |
| List `::before` state layer follows the currentColor+opacity pattern | Same hover/active overlay technique used since Wave 1 buttons. Validates this as the universal state feedback pattern across all component types. | Confirms pattern universality |

### Sprint E Learnings

| Decision | Rationale | Affects |
|----------|-----------|---------|
| DS_UNIVERSAL_CHECKLIST.md as build-time gate eliminates most audit failures | Sprint E had 0 CSS-level failures (vs 12 in Sprint D). Reading U1-U19 before writing CSS ensures rules are applied during construction, not caught after. | All future sprints |
| Composed components reuse L1-L5 token patterns, not component CSS | AI Chat bubble uses `--primary-tonal-bg/fg` directly, not `.ds-chip` classes. Composition is at the token level, not the CSS class level. | All L6 composed components |
| Chat bubble alignment uses `margin-inline-start: auto` for RTL safety | User bubble right-aligns in LTR and left-aligns in RTL via logical margin, not physical float or positioning. | AI Chat, any directional alignment |
| Command palette centering uses `inset-inline-start: 50%` + `translate(-50%)` | Same centering pattern as Slider tooltip and Timeline connecting line. Universal for absolutely-centered overlays. | Command Palette, any centered overlay |
| Notification type borders use `box-shadow: inset 3px 0 0 0` | Left accent border via box-shadow instead of CSS border. Consistent with U6 rule. The 3px inset on the inline-start side flips correctly in RTL. | Notification Center, any component with accent borders |
| Search match highlighting uses `color-mix(in srgb, var(--primary-filled-bg) 20%, transparent)` | No raw rgba(). Token-based semi-transparent highlight that works across themes. | Search, any text highlight pattern |
| Empty states are static components with no animation gate | U14 (reduced motion) does not apply to components with zero animations. Empty States only needs U7 (typography) and U2 (focus on action buttons). | Empty States, any static display component |
| Post-build auditor may false-positive on `:focus-visible` in JS | CSS `:focus-visible` is browser-native, no JS event listeners needed. Auditor flagged "missing focus event binding" but the tester does not use JS for focus, only CSS pseudo-classes. | Auditor calibration for future sprints |
| Overlay components in tester MUST use position:absolute, never fixed | Command Palette used `position: fixed` on backdrop and palette, which escaped the preview container and blocked the entire page. Sprint A Dialog already established this rule ("demo-relative backdrop"). All overlay/modal components must use absolute positioning inside the preview container. | Command Palette, any future overlay component in the tester |

### Sprint D+E Final Audit Learnings

| Decision | Reasoning | Propagation Scope |
|----------|-----------|-------------------|
| CSS double-paren typo `var(--ease-standard))` silently kills transitions | The CSS parser skips the entire rule on syntax error. 6 instances found in Search and Empty States. No visual error, transitions just don't fire. Always verify closing parens in transition shorthand. | All components, build-time lint |
| Data Grid td borders must use box-shadow not CSS border | `border-top: 1px solid` on table cells causes layout micro-shifts when combined with hover/selected states. `box-shadow: inset 0 1px 0 0` is visually identical and layout-stable. | Data Grid, any table-like component |
| Accordion item dividers must use box-shadow not CSS border | Same as Data Grid td. Internal dividers within a component follow U6 even though they are separators not container outlines. | Accordion, any component with internal dividers |
| Every disabled hover must include `.disabled.hover` class variant | Three components (List, Accordion, Data Grid) had `:hover` suppression on disabled but no `.hover` class variant. The tester forces states via class, so `.disabled.hover` must always accompany `.disabled:hover`. | All interactive components, U3 extension |
| Disabled color propagation must enumerate ALL child classes | Accordion badge and List leading icon were missing disabled color rules. Each component audit must enumerate every child CSS class and verify a `.disabled` rule exists for it. | All components with children, U8 audit checklist |
| Dialog backdrop was still position:fixed from Sprint A | The Sprint A to-do to fix Dialog to absolute was never completed. Fixed during this audit. Confirms that the position:absolute rule must apply retroactively to all overlay components. | Dialog, all overlays |
| Dead CSS must be removed during audit | `.ds-chat-dot` CSS (3 rules + 1 keyframe) was orphaned because the render uses `.ds-dot` from Loading. Dead code increases file size and creates confusion about which class is canonical. | All components, post-build cleanup |
| Raw `border-radius: 50%` violates U15 | Three instances found in Sprint E (chat avatar, chat dot, notif dot). Token `var(--radius-full)` must always be used. | All components, U15 |
| Raw `line-height: 1.5` violates U15 | Two instances in Sprint E (chat text, empty description). Token `var(--line-height-normal)` must always be used. | All components, U15 |
| Search bar needs explicit hover and disabled states | A composite input wrapper (search bar) still needs `:hover`/`.hover` for border darkening and `.disabled` for visual suppression. Missing these makes the component feel inert. | Search, any composite input wrapper |
| Earlier sprint components (L1-L4) still have CSS border violations | 12 instances of `border-top/bottom` remained from before U6. After classification: 3 were tester UI (kept), 2 were filled text field design intent (kept), 7 were DS component borders. All 7 converted to `box-shadow: inset 0 1px 0 0` / `inset 0 -1px 0 0`. Components fixed: Card footer, Tabs, Nav Rail header, Bottom Nav, Panel header, Panel footer, Dialog footer. | All L1-L4 components, COMPLETED |
